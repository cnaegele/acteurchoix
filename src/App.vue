<template>
  <v-app>
    <v-main>
      <ActeurChoix 
        critereTypeInit="nom"
        nombreMaximumRetour="50"
      >
      </ActeurChoix>
    </v-main>

  </v-app>
</template>

<script setup>
import ActeurChoix from './components/ActeurChoix.vue'
</script>
