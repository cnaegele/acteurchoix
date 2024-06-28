<template>
  <v-app>
    <v-main>
      <div>Choix un seul acteur</div>
      <div>
      <ActeurChoix 
        critereTypeInit="nom"
        nombreMaximumRetour="50"
        modeChoix="unique"
        @choixActeur="receptionActeur"
      >
      </ActeurChoix>
      </div>
      <div>&nbsp;</div>
      <div>Choix plusieurs acteurs</div>
      <div>
      <ActeurChoix 
        critereTypeInit="nom"
        nombreMaximumRetour="50"
        modeChoix="multiple"
        @choixActeur="receptionActeur"
      >
      </ActeurChoix>
      </div>
    </v-main>
  </v-app>
</template>

<script setup>
import ActeurChoix from './components/ActeurChoix.vue'

const receptionActeur = (idacteur, jsonData) => {
  console.log(`Réception acteur(s). id: ${idacteur} \njson: ${jsonData}`)
}
</script>
