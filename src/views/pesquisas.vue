<template>

<Header></Header>

<div class="inputpesquisa" >
    <h2 v-if="isLancamentos">Lançamentos</h2>
    <h2 v-else-if="isLivros">Livros</h2>
    <h2 v-else-if="isMangas">Mangás</h2>
    <h2 v-else> <span v-if="!termoBusca" > Tudo </span> <span v-if="termoBusca">" Resultados para: {{ termoBusca }}"</span></h2>
    </div>

<div class="tudo" >
    <div class="filtros" >
        <div class="filtros-header" >
            <div class="header-cima" >
            <h2>Filtros</h2>
            <img src="../components/img/filtrero.webp" alt="">
            </div>
        <div class="botaoquelimpa">
            <span @click="algumFiltroAtivo && limparFiltros()" :style="`visibility: ${algumFiltroAtivo ? 'visible' : 'hidden'}; user-select: none; `" >Limpar filtros</span>
        </div>
        </div>
        <div class="filtro-botoes-separados">
            <div class="filtro-bloco">
                <button class="filtro-btn" @click="abertoPrecos = !abertoPrecos">
                    <p>Preços</p>
                    <img src="../components/img/setaprabaxo.png" alt=""
                        :style="{ transform: abertoPrecos ? 'rotate(180deg)' : 'rotate(0deg)' }">
                </button>
                <transition name="fade">
                    <div v-if="abertoPrecos" class="filtro-conteudo">
                        <label><input type="radio" name="preco" value="ate20" v-model="precoSelecionado"> Até R$ 20</label><br>
                        <label><input type="radio" name="preco" value="20a50" v-model="precoSelecionado"> R$ 20 a R$ 50</label><br>
                        <label><input type="radio" name="preco" value="50a100" v-model="precoSelecionado"> R$ 50 a R$ 100</label><br>
                        <label><input type="radio" name="preco" value="100a200" v-model="precoSelecionado"> R$ 100 a R$ 200</label><br>
                        <label><input type="radio" name="preco" value="acima200" v-model="precoSelecionado"> Acima de R$ 200</label>
                    </div>
                </transition>
            </div>
            <div class="filtro-bloco">
                <button class="filtro-btn" @click="abertoCategoria = !abertoCategoria">
                    <p>Categoria</p>
                    <img src="../components/img/setaprabaxo.png" alt=""
                        :style="{ transform: abertoCategoria ? 'rotate(180deg)' : 'rotate(0deg)' }">
                </button>
                <transition name="fade">
                    <div v-if="abertoCategoria" class="filtro-conteudo">
                        <div v-for="cat in categorias" :key="cat.id">
                            <label>
                                <input type="checkbox" v-model="categoriasSelecionadas[cat.id]"> {{ cat.name }}
                            </label>
                        </div>
                    </div>
                </transition>
            </div>
            <div class="filtro-bloco">
                <button class="filtro-btn" @click="abertoFormato = !abertoFormato">
                    <p>Formato de capa</p>
                    <img src="../components/img/setaprabaxo.png" alt=""
                        :style="{ transform: abertoFormato ? 'rotate(180deg)' : 'rotate(0deg)' }">
                </button>
                <transition name="fade">
                    <div v-if="abertoFormato" class="filtro-conteudo">
                        <label><input type="checkbox"> Capa dura</label><br>
                        <label><input type="checkbox"> Capa comum</label><br>
                    </div>
                </transition>
            </div>
            <div class="filtro-bloco">
                <button class="filtro-btn" @click="abertoDescontos = !abertoDescontos">
                    <p>Descontos</p>
                    <img src="../components/img/setaprabaxo.png" alt=""
                        :style="{ transform: abertoDescontos ? 'rotate(180deg)' : 'rotate(0deg)' }">
                </button>
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
                <button @click="toggleModo2" class="header-btn-quadrado"><img src="../components/img/MODOV6-Photoroom.png" alt=""></button>
                <button @click="toggleModo" class="header-btn-quadrado"><img src="../components/img/MODOV7-Photoroom.png" alt=""></button>
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
            <div v-else-if="!modoum" class="lista-pesquisa">
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
            <div v-if="modoum" class="lista-pesquisa2">
                <div class="produto2" v-for="produto in produtosFiltrados" :key="produto.id">
                    <div class="nome-preco-imagem2" style="position:relative;">
                        <img :src="produto.image_path" alt="Imagem do produto" class="produto-imagem" />
                        <img :src="produto.stock >= 1 ? DISPONIVELREAL : INDISPONIVELREAL" :alt="produto.stock >= 1 ? 'Disponível' : 'Indisponível'" class="disponivel-selo2" />
                        <div class="aolado" >
                        <h4>{{ produto.name }}</h4>
                        <p>R$ {{ produto.price }}</p>
                        <div class="add2" >
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
    </div>
</div>

<Footer></Footer>

</template>

<script setup>

import { ref, onMounted, watch, computed } from 'vue'
import { useRoute } from 'vue-router'
import Header from '../components/Headercomponent.vue'
import Footer from '../components/Footercomponent.vue'
import api from '../services/api'
import DISPONIVELREAL from '../components/img/DISPONIVELREAL.png'
import INDISPONIVELREAL from '../components/img/INDISPONIVELREAL.png'
import { getCategoriasPorUsuario228 } from '../services/api'

const abertoPrecos = ref(false)
const abertoCategoria = ref(false)
const abertoFormato = ref(false)
const abertoDescontos = ref(false)
const ordemSelecionada = ref('')

const produtos = ref([])
const produtosFiltrados = ref([])
const carregando = ref(false)
const erro = ref('')
const route = useRoute()
const termoBusca = ref('')
const isLancamentos = ref(false)
const isLivros = ref(false)
const isMangas = ref(false)
const modoum = ref(false)

const toggleModo = () => {
    modoum.value = true;
}

const toggleModo2 = () => {
    modoum.value = false;
}

// 1. Estado reativo para o filtro de preço exclusivo
const precoSelecionado = ref('') // '', 'ate20', '20a50', '50a100', '100a200', 'acima200'

// 1. Estado reativo para os checkboxes
const categorias = ref([]) // lista de categorias da API
const categoriasSelecionadas = ref({}) // id: boolean

// 1. Computed para saber se há algum filtro ativo
const algumFiltroAtivo = computed(() => {
    // Categoria
    const algumaCategoria = Object.values(categoriasSelecionadas.value).some(v => v)
    // Preço
    const algumPreco = !!precoSelecionado.value
    // (pode adicionar outros filtros aqui se necessário)
    return algumaCategoria || algumPreco
})

// 2. Função para limpar todos os filtros
function limparFiltros() {
    // Limpa categorias
    for (const id in categoriasSelecionadas.value) {
        categoriasSelecionadas.value[id] = false
    }
    // Limpa preço
    precoSelecionado.value = ''
}

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

async function buscarCategorias() {
    try {
        const data = await getCategoriasPorUsuario228()
        categorias.value = data
        // Inicializa o objeto de seleção
        categoriasSelecionadas.value = {}
        for (const cat of data) {
            categoriasSelecionadas.value[cat.id] = false
        }
    } catch (e) {
        // erro ao buscar categorias
    }
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
    // Filtragem por categoria dinâmica
    const idsAtivos = Object.entries(categoriasSelecionadas.value)
        .filter(([id, ativo]) => ativo)
        .map(([id]) => Number(id))
    if (idsAtivos.length > 0) {
        filtrados = filtrados.filter(p => idsAtivos.includes(p.category_id))
    }
    // Filtragem por preço exclusivo
    if (precoSelecionado.value) {
        filtrados = filtrados.filter(p => {
            const preco = Number(p.price)
            if (precoSelecionado.value === 'ate20') return preco <= 20
            if (precoSelecionado.value === '20a50') return preco > 20 && preco <= 50
            if (precoSelecionado.value === '50a100') return preco > 50 && preco <= 100
            if (precoSelecionado.value === '100a200') return preco > 100 && preco <= 200
            if (precoSelecionado.value === 'acima200') return preco > 200
            return true
        })
    }
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

onMounted(async () => {
    termoBusca.value = route.query.termo ? String(route.query.termo) : ''
    isLancamentos.value = !!route.query.lancamentos
    isLivros.value = route.query.categoria === 'livros'
    isMangas.value = route.query.categoria === 'mangás'
    await buscarCategorias()
    await buscarProdutos()
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
// 4. Adicionar um watch para reagir à mudança dos checkboxes
watch(categoriasSelecionadas, filtrarProdutos, { deep: true })
// 4. Adicionar um watch para reagir à mudança do filtro de preço
watch(precoSelecionado, filtrarProdutos)

</script>

<style scoped>

.aolado {
    width: 100%;
    padding: 10px;
    padding-left: 60px;
}

.aolado h4 {
    font-size: 30px;
    color: rgb(65, 65, 65);
    font-family: 'Roboto', sans-serif;
}

.aolado p {
    font-size: 30px;
    font-weight: bold;
    font-family: roboto;
    color: #242424;
}

.add2 {
    display: flex;
    align-items: center;
    justify-content: start;
    gap: 10px;
    margin-top: 15px;
}

.nome-preco-imagem2 .aolado img {
    width: 25px;
    height: 25px;
    border: none;
    opacity: 0.92;
}

.nome-preco-imagem2 .aolado img:hover {
    opacity: 0.8;
}

.nome-preco-imagem2 .aolado button {
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: rgb(255, 255, 255);
    filter: invert(1);
    gap: 10px;
    padding: 7px;
    border-radius: 7px;
    width: 150px;
    opacity: 0.92;
}

.nome-preco-imagem2 .aolado button p {
    font-size: 20px;
}

.nome-preco-imagem2 .aolado button:hover {
    opacity: 0.8;
}

.lista-pesquisa2 {
    display: flex;
    justify-content: center;
    flex-direction: column;
    padding: 40px;
    padding-left: 50px;
    position: relative;
    gap: 10px;
    width: 100%;
}

.produto2 {
    margin-bottom:40px;
}

.produto2:hover {
    box-shadow: 0 0 15px 0 rgba(0, 0, 0, 0.1);
}

.nome-preco-imagem2 {
    display: flex;
}

.nome-preco-imagem2 img {
    width: 220px;
    height: 290px;
    border: 1px solid rgba(185, 185, 185, 0.842);
    cursor: pointer;
}

.nome-preco-imagem2 .disponivel-selo2 {
    width: 140px;
    height: auto;
    border: none;
    position: absolute;
    left: 267px;
    bottom: 5px;
    z-index: 2;
    border-radius: 9px;
}

.botaoquelimpa span {
    color: #e11d48;
    cursor: pointer;
    text-decoration: underline;
    font-size: 1rem;
    font-weight: 500;
    margin-top: 2px;
}

.header-cima {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
}

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
    font-size: 2.2rem;
    font-family: 'Roboto', sans-serif;
    font-weight: bold;
    color: #353535;
}

.filtros {
    width: 290px;
    height: 100%;
    display: flex;
    flex-direction: column;
}

.filtros-header {
    display: flex;
    justify-content: space-between;
    flex-direction: column;
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
    width: 270px;
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
    margin: 35px;
}
.header-btn-quadrado {
    width: 44px;
    height: 44px;
    background: #fffffffa;
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
}

.header-btn-quadrado:hover {
    filter: brightness(0.8);
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