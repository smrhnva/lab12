<template>
  <div id="app">
    <h1 class="jumbotron text-center">Записная книга</h1>
    <contacts></contacts>
  </div>
</template>

<script>
import contacts from './components/contacts.vue'

export default {
  name: 'App',
  components: {
    contacts: contacts
  }
}
</script>

<style>
.jumbotron{
  padding: 10px;
}
</style>