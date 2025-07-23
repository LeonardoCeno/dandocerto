<template>

<Header></Header>

<div class="tudo" >
    <div class="inputpesquisa" >
    <h2>Resultados para:</h2>
    </div>


</div>

<Footer></Footer>

</template>

<script setup>
import Header from '../components/Headercomponent.vue'
import Footer from '../components/Footercomponent.vue'
</script>

<style scoped>

.tudo{
    width: 100%;
    height: 100vh;
    background-color: #acacacfa;
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

</style>