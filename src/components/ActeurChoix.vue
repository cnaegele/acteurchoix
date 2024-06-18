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
      <v-col cols="4" md="2">
        <v-text-field 
          clearable 
          v-model="txtCritere" 
          :label="labelTextField"
          @input="onInputCritere"
        ></v-text-field>  
      </v-col>
    </v-row>
    <v-row no-gutters>
      <v-col cols="4" md="4">
        <v-list
          max-height="400" 
        >
          <v-list-subheader>{{ libelleListe }}</v-list-subheader>
          <v-list-item
            v-for="acteur in acteursListeSelect"
            :key="acteur.acteurid"
            :value="acteur.acteurid"
            :title="acteur.acteurnom"
            @click="choixActeur(acteur.acteurid)"
          >

            <template v-slot:append>
              <v-btn
                color="grey-lighten-1"
                icon="mdi-information"
                variant="text"
                @click="infoActeur(acteur.acteurid)"
              ></v-btn>
            </template>

          </v-list-item>
        </v-list>
      </v-col>  
    </v-row>
  </v-container>

</template>

<script setup>
import { reactive, ref, watch } from 'vue'
import { getActeursListe } from '@/axioscalls.js'

const props = defineProps({
  critereTypeInit: String,
  nombreMaximumRetour: String,
})

const libelleListe = ref('choix acteurs (0)')
let txtCritere = ref('')
let bActeurMoral = ref(true)
let bActeurPhysique = ref(true)
let bActeurDesactive = ref(false)
let critereType = ref(props.critereTypeInit)
let nombreMaximumRetour = ref(100)
if (props.nombreMaximumRetour !== undefined) {
  nombreMaximumRetour = ref(props.nombreMaximumRetour)
}
let labelTextField = ref('nom')
const selectedActeurId = ref(null)
const acteursListeSelect = ref([])

watch(critereType, (newValue, oldValue) => {
  switch (newValue) {
    case 'nom':
    case 'nomdebut':
      labelTextField.value = 'nom'
      break
    case 'idgo':
      labelTextField.value = 'id acteu.valuer'
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
    rechercheActeurs(critere, crType, nombreMaximumRetour.value)
  } else if (critere.length >= 3 && crType == 'nom') {
    rechercheActeurs(critere, crType, nombreMaximumRetour.value)
  } else if (critere.length >= 1 && crType == 'idgo') {
    rechercheActeurs(critere, crType, nombreMaximumRetour.value)
  } else if (critere.length >= 15 && crType == 'idche') {
    rechercheActeurs(critere, crType, nombreMaximumRetour.value)
  }
}
const rechercheActeurs = async (critere, crType, nombreMaximumRetour) => {
  let sbActeurMoral, sbActeurPhysique, sbActeurDesactive, snombreMaximumRetour
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
  snombreMaximumRetour = nombreMaximumRetour.toString()
  
  const oCritere = {
    "critere" : critere,
    "crtype" : crType,
    "bacteurmoral" : sbActeurMoral,
    "bacteurphysique" : sbActeurPhysique,
    "bacteurdesactive" : sbActeurDesactive,
    "nombremaximumretour" : snombreMaximumRetour
  }
  //console.log (JSON.stringify(oCritere))
  const acteursListe = await getActeursListe(JSON.stringify(oCritere))
  if (acteursListe.length < nombreMaximumRetour) {
    libelleListe.value = `Choix acteurs (${acteursListe.length})`
  } else {
    libelleListe.value = `Choix acteurs (${acteursListe.length}). Attention, plus de ${nombreMaximumRetour} acteurs correspondent aux critères`
  }
  //console.log(acteursListe)
  acteursListeSelect.value = acteursListe
}

const choixActeur = (acteurId) => {
  console.log(acteurId)
}

const infoActeur = (acteurId) => {
  alert(`todo... information acteur : ${acteurId}`)
}
</script>