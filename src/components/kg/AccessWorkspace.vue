<!-- src/components/kg/AccessWorkspace.vue -->
<template>
    <v-container fluid class="pa-0">
  <!-- Layout dédié à cette page d’accès -->
  <v-layout class="access-layout">
    <!-- Barre latérale (même principe que l’onglet Explore) -->
    <v-navigation-drawer
      rail
      expand-on-hover
      :model-value="true"
      class="elevation-1"
    >
      <v-list density="compact" nav>
        <v-list-item
          :active="subtab === 'imgtkg'"
          prepend-icon="mdi-database"
          title="IMGT-KG"
          @click="subtab = 'imgtkg'"
        />
        <v-list-item
          :active="subtab === 'mabkg'"
          prepend-icon="mdi-database-outline"
          title="IMGT-MAB-KG"
          @click="subtab = 'mabkg'"
        />
      </v-list>
    </v-navigation-drawer>

    <!-- Contenu principal -->
    <v-main>
      <v-container fluid class="pa-4">
        <KgAccess v-if="subtab === 'imgtkg'" />
        <MabKgAccess v-else />
      </v-container>
    </v-main>
  </v-layout>
  </v-container>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import KgAccess from './KgAccess.vue'
import MabKgAccess from './MabKgAccess.vue'

type SubTab = 'imgtkg' | 'mabkg'
const subtab = ref<SubTab>('imgtkg')
</script>


