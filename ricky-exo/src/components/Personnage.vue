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

<script lang="ts">

import { ApolloClient, InMemoryCache, gql, ApolloQueryResult } from '@apollo/client/core';

interface User {
  name: string;
  image: string;
}

// Affichage de la page 1 par défaut
export default {
  data() {
    return {
      users: [] as User[],
      currentPage: 1,
      totalPages: 0
    };
  },
  mounted() {
    this.loadUsers(this.currentPage);
  },
  methods: {
    // Charger les personnages d'une certaine page en GraphQL avec Apollo
    async loadUsers(page: number) {
      const client = new ApolloClient({
        uri: 'https://rickandmortyapi.com/graphql',
        cache: new InMemoryCache()
      });

      const result: ApolloQueryResult<any> = await client.query({
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

      this.users = result.data.characters.results; // Tableau de donnée de personnage contenant le nom et l'image
      this.totalPages = result.data.characters.info.pages; // Le nombre de pages, issu de l'api Rick et Morty
    },
    changePage(page: number) {
      if (page < 1 || page > this.totalPages) return; // Vérifier si la page est valide
      this.currentPage = page;
      this.loadUsers(this.currentPage); // Recharger les personnages
    }
  }
};
</script>