<style scoped>

</style>

<template>
  <h1>{{ critereTypeInit }}</h1>
  <v-container>
    <v-row no-gutters>
      <v-col cols="4" md="4">
        <v-radio-group
          label="critère selon"
          v-model="critereType"
          inline
          density="compact">
          <v-radio label="nom" :value="'nom'"></v-radio>&nbsp;&nbsp;
          <v-radio label="nom début" :value="'nomdebut'"></v-radio>&nbsp;&nbsp;
          <v-radio label="id goéland" :value="'idgo'"></v-radio>&nbsp;&nbsp;
          <v-radio label="id CHE" :value="'idche'"></v-radio>&nbsp;&nbsp;
        </v-radio-group>  
      </v-col>
    </v-row>
    <v-row no-gutters>
      <v-col cols="4" md="1">
        <v-checkbox
          v-model="bActeurMoral" 
          label="personnes morales"
          @click="onInputCritere">
        </v-checkbox>  
      </v-col>
      <v-col cols="4" md="1">
        <v-checkbox
          v-model="bActeurPhysique" 
          label="personnes physiques"
          @click="onInputCritere">
        </v-checkbox>  
      </v-col>
      <v-col cols="4" md="1">
         <v-checkbox
          v-model="bActeurDesactive" 
          label="y.c. acteurs désactivés"
          @click="onInputCritere">
        </v-checkbox>  
      </v-col>
    </v-row>
    <v-row  no-gutters>
      <v-col cols="4" md="4">
        <v-text-field 
          clearable 
          v-model="txtCritere" 
          :label="labelTextField"
          @input="onInputCritere"
        ></v-text-field>  
      </v-col>
    </v-row>
  </v-container>

</template>

<script setup>
import { ref, watch } from 'vue'
import { getActeursListe } from '@/axioscalls.js'

const props = defineProps({
  critereTypeInit: String,
})

let txtCritere = ref('')
let bActeurMoral = ref(true)
let bActeurPhysique = ref(true)
let bActeurDesactive = ref(false)
let critereType = ref(props.critereTypeInit)
let labelTextField = ref('nom')
//console.log(props.critereTypeInit)

watch(critereType, (newValue, oldValue) => {
  switch (newValue) {
    case 'nom':
    case 'nomdebut':
      labelTextField.value = 'nom'
      break
    case 'idgo':
      labelTextField.value = 'id acteur'
      break
    case 'idche':
      labelTextField.value = 'id fédéral (CHE-)'
      txtCritere.value = 'CHE-'
      break
  }
  onInputCritere()
})

//pour démarrer la recherche seulement si la frappe au clavier a cessé depuis 0.7 secondes
let typingTimer
const typingInterval = 700
const onInputCritere = () => {
  //console.log('oninput')
  clearTimeout(typingTimer)
  
  typingTimer = setTimeout(() => {
    prepareRechercheActeurs()
  }, typingInterval)
}

const prepareRechercheActeurs = () => {
  const critere = txtCritere.value
  const crType = critereType.value

   if (critere.length >= 1 && crType == 'nomdebut') {
    rechercheActeurs(critere, crType)
  } else if (critere.length >= 3 && crType == 'nom') {
    rechercheActeurs(critere, crType)
  } else if (critere.length >= 1 && crType == 'idgo') {
    rechercheActeurs(critere, crType)
  } else if (critere.length >= 15 && crType == 'idche') {
    rechercheActeurs(critere, crType)
  }
}
const rechercheActeurs = async (critere, crType) => {
  let sbActeurMoral, sbActeurPhysique, sbActeurDesactive
  if (bActeurMoral.value == true) {
    sbActeurMoral = '1'  
  } else {
    sbActeurMoral = '0'  
  }
  if (bActeurPhysique.value == true) {
    sbActeurPhysique = '1'  
  } else {
    sbActeurPhysique = '0'  
  }
  if (bActeurDesactive.value == true) {
    sbActeurDesactive = '1'  
  } else {
    sbActeurDesactive = '0'  
  }
 
  const oCritere = {
    "critere" : critere,
    "crtype" : crType,
    "bacteurmoral" : sbActeurMoral,
    "bacteurphysique" : sbActeurPhysique,
    "bacteurdesactive" : sbActeurDesactive,
  }
  //console.log (JSON.stringify(oCritere))
  const acteursListe = await getActeursListe(JSON.stringify(oCritere))
  console.log(acteursListe)
}
</script>