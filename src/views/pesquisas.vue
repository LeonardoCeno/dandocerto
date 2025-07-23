<template>

<Header></Header>

<div class="inputpesquisa" >
    <h2>Resultados para:</h2>
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
                <button class="header-btn-quadrado">A</button>
                <button class="header-btn-quadrado">B</button>
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
        <div class="tudo-conteudos" >
            <!-- Aqui entrariam os resultados filtrados -->
        </div>
    </div>
</div>

<Footer></Footer>

</template>

<script setup>

import { ref } from 'vue'
import Header from '../components/Headercomponent.vue'
import Footer from '../components/Footercomponent.vue'

const abertoPrecos = ref(false)
const abertoCategoria = ref(false)
const abertoFormato = ref(false)
const abertoDescontos = ref(false)
const ordemSelecionada = ref('alfabetica')

</script>

<style scoped>

.tudo{
    display: flex;
    justify-content: center;
    width: 100%;
    height: 100vh;
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
    background-color: #28eba0fa;
}

</style>