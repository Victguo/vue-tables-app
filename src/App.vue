<script setup>
import Table from './components/Table.vue'
import drugData from './data/mockDrugData.json'
</script>

<template>
  <div id="app">
    <main>
      <Table :rows="drugData"> </Table>
    </main>
  </div>
</template>

<style>
@import './assets/base.css';

#app {
  max-width: 1280px;
  margin: 0 auto;
  padding: 2rem;

  font-weight: normal;
}

header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

a,
.green {
  text-decoration: none;
  color: hsla(160, 100%, 37%, 1);
  transition: 0.4s;
}

@media (hover: hover) {
  a:hover {
    background-color: hsla(160, 100%, 37%, 0.2);
  }
}

@media (min-width: 1024px) {
  body {
    min-width: 100vh;
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  #app {
    margin: auto;
    width: 100%;
    height: 100%;
  }
  
  main {
    height: 100%;
  }
}
</style>
