<script setup>
import { ref } from 'vue'

defineProps({
  msg: String,
})

const count = ref(0)
</script>

<template>
<h1 className="text-5xl font-bold underline">Liste des personnages de Rick et Morty !</h1>

<a href="#">
  <div class="grid grid-cols-4 gap-4 font-mono antialiased my-5">
    <div v-for="user in users" class="hover:scale-110 shadow-md transition-all ease-in-out bg-slate-100 rounded-lg">
      <img :src="user.image" alt="User Image" class="rounded-lg">
      <p class="text-gray-900">{{ user.name }}</p>
    </div>
  </div>
</a>
<div class="pagination">
  <button @click="changePage(currentPage - 1)" :disabled="currentPage <= 1">Précédent</button>
  <span class="mx-3">Page {{ currentPage }} sur {{ totalPages }}</span>
  <button @click="changePage(currentPage + 1)" :disabled="currentPage >= totalPages">Suivant</button>
</div>
</template>

<script>

import { reactive, onMounted } from 'vue';
import { ApolloClient, InMemoryCache, gql } from '@apollo/client/core';


export default {
  data() {
    return {
      users: [],
      currentPage: 1,
      totalPages: 0
    };
  },
  mounted() {
    this.loadUsers(this.currentPage);
  },
  methods: {
    async loadUsers(page) {
      const client = new ApolloClient({
        uri: 'https://rickandmortyapi.com/graphql',
        cache: new InMemoryCache()
      });

      const result = await client.query({
        query: gql`
        query getCharacters($page: Int!) {
          characters(page: $page) {
            info {
              pages
            }
            results {
              name
              image
            }
          }
        }`,
        variables: {
          page: page
        }
      });

      this.users = result.data.characters.results;
      this.totalPages = result.data.characters.info.pages;
    },
    changePage(page) {
      if (page < 1 || page > this.totalPages) return; // Vérifiez si la page est valide
      this.currentPage = page;
      this.loadUsers(this.currentPage); // Rechargez les utilisateurs
    }
  }
};
</script>

<style scoped>
</style>
