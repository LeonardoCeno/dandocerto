<template>

<Header></Header>

<div class="inputpesquisa" >
    <h2 v-if="isLancamentos">Lançamentos</h2>
    <h2 v-else-if="isLivros">Livros</h2>
    <h2 v-else-if="isMangas">Mangás</h2>
    <h2 v-else>Resultados para: <span v-if="termoBusca">"{{ termoBusca }}"</span></h2>
    </div>

<div class="tudo" >
    <div class="filtros" >
        <div class="filtros-header" >
        <h2>Filtros</h2>
        <img src="../components/img/filtrero.webp" alt="">
        </div>
        <div class="filtro-botoes-separados">
            <div class="filtro-bloco">
                <button class="filtro-btn" @click="abertoPrecos = !abertoPrecos"><p>Preços</p><img src="../components/img/setaprabaxo.png" alt=""></button>
                <transition name="fade">
                    <div v-if="abertoPrecos" class="filtro-conteudo">
                        <label><input type="checkbox"> Até R$ 20</label><br>
                        <label><input type="checkbox"> R$ 20 a R$ 50</label><br>
                        <label><input type="checkbox"> R$ 50 a R$ 100</label><br>
                        <label><input type="checkbox"> R$ 100 a R$ 200</label><br>
                        <label><input type="checkbox"> Acima de R$ 200</label>
                    </div>
                </transition>
            </div>
            <div class="filtro-bloco">
                <button class="filtro-btn" @click="abertoCategoria = !abertoCategoria"><p>Categoria</p><img src="../components/img/setaprabaxo.png" alt=""></button>
                <transition name="fade">
                    <div v-if="abertoCategoria" class="filtro-conteudo">
                        <label><input type="checkbox"> Mangá</label><br>
                        <label><input type="checkbox"> Livro</label><br>
                        <label><input type="checkbox"> Artbook</label><br>
                        <label><input type="checkbox"> HQ</label><br>
                        <label><input type="checkbox"> Romance</label>
                    </div>
                </transition>
            </div>
            <div class="filtro-bloco">
                <button class="filtro-btn" @click="abertoFormato = !abertoFormato"><p>Formato de capa</p><img src="../components/img/setaprabaxo.png" alt=""></button>
                <transition name="fade">
                    <div v-if="abertoFormato" class="filtro-conteudo">
                        <label><input type="checkbox"> Capa dura</label><br>
                        <label><input type="checkbox"> Capa comum</label><br>
                    </div>
                </transition>
            </div>
            <div class="filtro-bloco">
                <button class="filtro-btn" @click="abertoDescontos = !abertoDescontos"><p>Descontos</p><img src="../components/img/setaprabaxo.png" alt=""></button>
                <transition name="fade">
                    <div v-if="abertoDescontos" class="filtro-conteudo">
                        <label><input type="checkbox"> 10% ou mais</label><br>
                        <label><input type="checkbox"> 20% ou mais</label><br>
                        <label><input type="checkbox"> 30% ou mais</label><br>
                        <label><input type="checkbox"> 40% ou mais</label><br>
                        <label><input type="checkbox"> 50% ou mais</label>
                    </div>
                </transition>
            </div>
        </div>
    </div>
    <div class="conteudos" >
        <div class="Header-conteudos" >
            <div class="header-conteudos-left">
                <button class="header-btn-quadrado"><img src="" alt=""></button>
                <button class="header-btn-quadrado"><img src="" alt=""></button>
            </div>
            <div class="header-conteudos-right">
                <label for="filtroOrdem" class="header-label">Ordenar por:</label>
                <select id="filtroOrdem" v-model="ordemSelecionada" class="header-select">
                    <option value="valor-crescente">Valor: menor para maior</option>
                    <option value="valor-decrescente">Valor: maior para menor</option>
                    <option value="alfabetica">Ordem alfabética</option>
                </select>
            </div>
        </div>
        <div class="tudo-conteudos">
            <div v-if="carregando" class="carregando">Carregando produtos...</div>
            <div v-else-if="erro" class="erro">{{ erro }}</div>
            <div v-else-if="produtosFiltrados.length === 0" class="nenhum-produto">Nenhum produto encontrado.</div>
            <div v-else class="lista-pesquisa">
                <div class="produto" v-for="produto in produtosFiltrados" :key="produto.id">
                    <div class="nome-preco-imagem" style="position:relative;">
                        <img :src="produto.image_path" alt="Imagem do produto" class="produto-imagem" />
                        <img :src="produto.stock >= 1 ? DISPONIVELREAL : INDISPONIVELREAL" :alt="produto.stock >= 1 ? 'Disponível' : 'Indisponível'" class="disponivel-selo" />
                        <h4>{{ produto.name }}</h4>
                        <p>R$ {{ produto.price }}</p>
                    </div>
                    <div class="add">
                        <button>
                            <img src="../components/img/maisumcarrinho.png" alt="">
                            <p>Adicionar</p>
                        </button>
                        <img src="../components/img/coraçaofav.png" alt="">
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<Footer></Footer>

</template>

<script setup>

import { ref, onMounted, watch } from 'vue'
import { useRoute } from 'vue-router'
import Header from '../components/Headercomponent.vue'
import Footer from '../components/Footercomponent.vue'
import api from '../services/api'
import DISPONIVELREAL from '../components/img/DISPONIVELREAL.png'
import INDISPONIVELREAL from '../components/img/INDISPONIVELREAL.png'

const abertoPrecos = ref(false)
const abertoCategoria = ref(false)
const abertoFormato = ref(false)
const abertoDescontos = ref(false)
const ordemSelecionada = ref('alfabetica')

const produtos = ref([])
const produtosFiltrados = ref([])
const carregando = ref(false)
const erro = ref('')
const route = useRoute()
const termoBusca = ref('')
const isLancamentos = ref(false)
const isLivros = ref(false)
const isMangas = ref(false)

async function buscarProdutos() {
    carregando.value = true
    erro.value = ''
    try {
        const response = await api.get('/products/user/228')
        produtos.value = response.data.map(produto => ({
            ...produto,
            image_path: produto.image_path && !produto.image_path.startsWith('http')
                ? 'http://35.196.79.227:8000' + produto.image_path
                : produto.image_path
        }))
        filtrarProdutos()
    } catch (e) {
        erro.value = 'Erro ao buscar produtos.'
    } finally {
        carregando.value = false
    }
}

function ordenarProdutos(lista) {
    if (ordemSelecionada.value === 'valor-crescente') {
        return lista.slice().sort((a, b) => Number(a.price) - Number(b.price))
    } else if (ordemSelecionada.value === 'valor-decrescente') {
        return lista.slice().sort((a, b) => Number(b.price) - Number(a.price))
    } else if (ordemSelecionada.value === 'alfabetica') {
        return lista.slice().sort((a, b) => a.name.localeCompare(b.name))
    }
    return lista
}

function filtrarProdutos() {
    isLancamentos.value = !!route.query.lancamentos
    isLivros.value = route.query.categoria === 'livros'
    isMangas.value = route.query.categoria === 'mangás'
    if (isLancamentos.value) {
        produtosFiltrados.value = produtos.value.slice().sort((a, b) => b.id - a.id)
        return
    }
    if (isLivros.value) {
        let filtrados = produtos.value.filter(p => p.category && p.category.name && p.category.name.toLowerCase() === 'livros')
        produtosFiltrados.value = ordenarProdutos(filtrados)
        return
    }
    if (isMangas.value) {
        let filtrados = produtos.value.filter(p => p.category && p.category.name && p.category.name.toLowerCase() === 'mangás')
        produtosFiltrados.value = ordenarProdutos(filtrados)
        return
    }
    const termo = termoBusca.value.trim().toLowerCase()
    let filtrados = produtos.value
    if (termo.length > 0) {
        filtrados = filtrados.filter(p => p.name && p.name.toLowerCase().includes(termo))
            .map(p => ({
                ...p,
                matchIndex: p.name.toLowerCase().indexOf(termo)
            }))
            .sort((a, b) => {
                if (a.matchIndex !== b.matchIndex) return a.matchIndex - b.matchIndex
                return a.name.length - b.name.length
            })
    }
    produtosFiltrados.value = ordenarProdutos(filtrados)
}

onMounted(() => {
    termoBusca.value = route.query.termo ? String(route.query.termo) : ''
    isLancamentos.value = !!route.query.lancamentos
    isLivros.value = route.query.categoria === 'livros'
    isMangas.value = route.query.categoria === 'mangás'
    buscarProdutos()
})

watch(() => route.query.termo, (novo) => {
    termoBusca.value = novo ? String(novo) : ''
    filtrarProdutos()
})

watch(() => route.query.lancamentos, (novo) => {
    isLancamentos.value = !!novo
    filtrarProdutos()
})

watch(() => route.query.categoria, (novo) => {
    isLivros.value = novo === 'livros'
    isMangas.value = novo === 'mangás'
    filtrarProdutos()
})

watch(ordemSelecionada, filtrarProdutos)

</script>

<style scoped>

.tudo{
    display: flex;
    justify-content: center;
    width: 100%;
    height: auto;
    min-height: 100vh;
    padding: 15px;
    gap: 20px;
}

.inputpesquisa{
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 15vh;
    background-color: #fffffffa;
}

.inputpesquisa h2 {
    font-size: 2.1rem;
    font-family: 'Roboto', sans-serif;
}

.filtros {
    width: 290px;
    height: 100%;
    display: flex;
    flex-direction: column;
}

.filtros-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 35px;
}

.filtros-header h2 {
    font-size: 2.4rem;
    font-family: Roboto;
}

.filtros-header img {
    width: 45px;
    height: auto;
}

.filtro-botoes-separados {
    display: flex;
    flex-direction: column;
    gap: 32px;
    width: 100%;
    align-items: center;
}
.filtro-bloco {
    width: 100%;
    border: 1px solid #cccccc;
    border-radius: 7px;
    padding: 7px;
}
.filtro-btn {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 15px;
    width: 100%;
    height: 50px;
    background-color: #060f18fa;
    border: none;
    border-radius: 7px;
    color: white;
    font-family: helvetica;
    font-size: 1.1rem;
    cursor: pointer;
    transition: background 0.2s;
}
.filtro-btn img {
    width: 20px;
    height: 17px;
    filter: invert(1);
}
.filtro-btn:hover {
    background: #1a2633;
}
.filtro-conteudo {
    width: 100%;
    color: #222;
    border-radius: 0 0 7px 7px;
    padding: 18px 18px 12px 18px;
    font-size: 1.08rem;
    z-index: 1;
    position: relative;
}
.filtro-conteudo input[type="checkbox"] {
    width: 22px;
    height: 22px;
    accent-color: #2961a1;
    border: 2.5px solid #222;
    border-radius: 4px;
    margin-right: 10px;
    box-shadow: 0 1px 2px rgba(0,0,0,0.07);
}

.conteudos {
    width: 1100px;
    height: 100%;
    background-color: #bbbbbbfa;
}

.Header-conteudos {
    width: 100%;
    height: 55px;
    background-color: #fffffffa;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0 18px;
    box-sizing: border-box;
}
.header-conteudos-left {
    display: flex;
    align-items: center;
    gap: 0;
}
.header-btn-quadrado {
    width: 44px;
    height: 44px;
    background: #060f18fa;
    color: #fff;
    border: none;
    border-radius: 7px;
    font-size: 1.3rem;
    font-weight: bold;
    margin-right: 0;
    margin-left: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    transition: background 0.2s;
}
.header-btn-quadrado + .header-btn-quadrado {
    margin-left: 2px;
}
.header-btn-quadrado:hover {
    background: #1a2633;
}
.header-conteudos-right {
    display: flex;
    align-items: center;
    gap: 10px;
}
.header-label {
    color: #000000;
    font-size: 1rem;
    font-weight: 500;
}
.header-select {
    padding: 7px 12px 7px 12px;
    border-radius: 6px;
    border: 1px solid #bdbdbd;
    font-size: 1rem;
    background: #fff;
    color: #222;
    outline: none;
}

.tudo-conteudos {
    display: flex;
    flex-direction: column;
    gap: 32px;
    width: 100%;
    align-items: center;
    background-color: #fffffffa;
}

.lista-pesquisa {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    justify-items: center;
    padding: 4px;
    position: relative;
    gap: 10px;
    width: 100%;
}
.produto {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    text-align: center;
    width: 230px;
    height: 92%;
    margin-top: 3vh;
    padding-left: 10px;
    padding-right: 10px;
}
.nome-preco-imagem {
    display: flex;
    flex-direction: column;
    align-items: center;
}
.nome-preco-imagem img {
    margin-top: 10px;
    height: 225px;
    width: 160px;
    border: 0.1px solid rgb(212, 212, 212);
    filter: contrast(100%);
}
.nome-preco-imagem .disponivel-selo {
    width: 95px;
    height: auto;
    border: none;
    position: absolute;
    left: 0px;
    bottom: 80px;
    z-index: 2;
    border-radius: 9px;
}
.add {
    display: flex;
    flex-direction: row;
    width: 100%;
    align-items: center;
    justify-content: center;
    gap: 12px;
    opacity: 0;
    pointer-events: none;
    margin-top: 5px;
    margin-bottom: 10px;
    padding-bottom: 10px;
}
.add button {
    display: flex;
    align-items: center;
    background-color: #030a11f5;
    justify-content: center;
    padding: 8px;
    border-radius: 7px;
    gap: 7px;
    width: 150px;
}
.add button:hover {
    background-color: #02060ade;
}
.add button p {
    color: white;
}
.add button img {
    width: 20px;
    height: auto;
    border: none;
    filter: invert(1);
}
.add img {
    width: 20px;
    height: auto;
    filter: invert(6%) sepia(50%) saturate(200%) hue-rotate(160deg) brightness(100%) contrast(100%);
}
.add img:hover {
    opacity: 0.9;
}
.produto:hover .add {
    opacity: 1;
    pointer-events: auto;
}
.produto:hover {
    box-shadow: 0 0 10px 0 rgba(0, 0, 0, 0.1);
}
.produto h4 {
    font-family: 'Roboto', sans-serif;
    font-size: 15px;
    color: rgb(65, 65, 65);
    margin-top: 10px;
    height: 40px;
}
.nenhum-produto {
    color: #444;
    font-size: 1.25rem;
    font-weight: 500;
    margin-top: 32px;
    text-align: center;
    width: 100%;
}
.carregando {
    color: #3a9c73;
    margin-top: 20px;
    font-size: 1.1rem;
}
.erro {
    color: #c0392b;
    margin-top: 20px;
    font-size: 1.1rem;
}

</style>