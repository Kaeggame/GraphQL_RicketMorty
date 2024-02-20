<script setup>
import { ref } from 'vue'

defineProps({
  msg: String,
})

const count = ref(0)
</script>

<template>
    <h1>Liste des personnages</h1>
    <ul>
      <li v-for="user in users">
        {{ user.name }} - {{ user.image }}
      </li>
    </ul>
</template>





<script>

import { reactive, onMounted } from 'vue';
import { ApolloClient, InMemoryCache, gql } from '@apollo/client/core';


export default {
  data() {
    return {
      users: [] // Initialiser la liste des utilisateurs à vide
    };
  },
  mounted() {
    // Appeler la méthode pour charger les utilisateurs lors du montage du composant
    this.loadUsers();
  },
  methods: {
    async loadUsers() {

      const client = new ApolloClient({
        uri: 'https://rickandmortyapi.com/graphql', // Remplacez avec l'URL de votre serveur GraphQL
        cache: new InMemoryCache()
      });
      
      const result = await client.query({
        query: gql`
        query {
          characters(page : 1){
            results{
              name
              image
            }
          }
            
        }

        `
      });
      this.users = result.data.characters.result
      console.log(this.users)
    }
  }
};
</script>







<style scoped>
.read-the-docs {
  color: #888;
}
</style>
