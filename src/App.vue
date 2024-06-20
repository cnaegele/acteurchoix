<template>
  <v-app>
    <v-main>
      <ActeurChoix 
        critereTypeInit="nom"
        nombreMaximumRetour="50"
        @choixActeur="receptionActeur"
      >
      </ActeurChoix>
    </v-main>

  </v-app>
</template>

<script setup>
import ActeurChoix from './components/ActeurChoix.vue'

const receptionActeur = (idacteur, jsonData) => {
  console.log(`Réception d'un acteur. id: ${idacteur} \njson: ${jsonData}`)
}
</script>
