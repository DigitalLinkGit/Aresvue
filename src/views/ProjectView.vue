<template>
  <div>
    <input type="file" @change="handleFileUpload" />
    <v-data-table :headers="headers" :items="excelData">
      <template v-slot:body="{ items }">
        <tbody>
          <tr v-for="item in items" :key="item.Affaire">
            <td v-for="(value, index) in item" :key="index">
              {{ value }}
            </td>
          </tr>
        </tbody>
      </template>

      <!-- Ajout de cette section pour afficher les en-têtes -->
      <template v-slot:thead="{ headers }">
        <thead>
          <tr>
            <th v-for="header in headers" :key="header.text">
              {{ header.text }}
            </th>
          </tr>
        </thead>
      </template>
    </v-data-table>
  </div>
</template>

<script>
import * as XLSX from 'xlsx';

export default {
  data() {
    return {
      headers: [],
      excelData: [],
    };
  },
  methods: {
    handleFileUpload(event) {
      const file = event.target.files[0];
      this.readFile(file);
    },
    readFile(file) {
  const reader = new FileReader();
  reader.onload = () => {
    // Analyse du fichier Excel
    const workbook = XLSX.read(reader.result, { type: 'array' });
    const sheet = workbook.Sheets[workbook.SheetNames[0]];

    // Convertir la feuille en tableau de données
    const excelData = XLSX.utils.sheet_to_json(sheet, { header: 1 });

    // Assurez-vous que la première ligne est utilisée comme en-tête
    const headers = excelData[0];

    // Filtrer les lignes vides du tableau d'en-têtes
    const filteredHeaders = headers.filter(header => header);

    // Filtrer les lignes vides du tableau de données
    const filteredData = excelData.slice(1).filter(row => row.some(cell => cell !== null && cell !== undefined));

    // Log pour débogage
    console.log('Headers:', filteredHeaders);

    // Mettre à jour les données et les en-têtes
    this.excelData = filteredData;
    this.headers = filteredHeaders;
  };
  reader.readAsArrayBuffer(file);
}
  },
};
</script>

<style>
/* Ajoutez des styles personnalisés ici si nécessaire */
</style>


<!--
<script setup>
//make a get request to the backend

import axios from 'axios';
import {ref} from "vue"
const data = ref({})
axios.get('https://restcountries.com/v3.1/all')
.then(response => {
  console.log(response.data[0])
  data.value = response.data
})
</script>
-->