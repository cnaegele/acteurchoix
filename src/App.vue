<template>
  <v-app>
    <v-main>
      <div v-if="modeChoix == 'multiple'">
        <ActeurChoix 
          critereTypeInit="nom"
          nombreMaximumRetour="50"
          modeChoix="multiple"
          @choixActeur="receptionActeur"
        >
        </ActeurChoix>
      </div>
      <div v-else>
        <ActeurChoix 
          critereTypeInit="nom"
          nombreMaximumRetour="50"
          modeChoix="unique"
          @choixActeur="receptionActeur"
        >
        </ActeurChoix>
      </div>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref } from 'vue'
import ActeurChoix from './components/ActeurChoix.vue'

const urlParams = new URLSearchParams(window.location.search)
let modeChoix = ref('unique')
if (urlParams.has('mode')) {
  modeChoix = ref(urlParams.get('mode'))
}

const receptionActeur = (idacteur, jsonData) => {
  console.log(`Réception acteur(s). id: ${idacteur} \njson: ${jsonData}`)
  if (window.opener && typeof window.opener.receptionActeurs24 === 'function') {
    window.opener.receptionActeurs24(jsonData)
  }
}
</script>
