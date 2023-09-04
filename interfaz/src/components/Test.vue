<template>
    <div class="flex items-center space-x-2">
        <input type="file" ref="fileInput" @change="handleFileUpload" accept=".csv"
            class="bg-gray-200 py-2 px-4 rounded-md shadow-md focus:outline-none focus:ring-2 focus:ring-slate-900" />
        <button @click="uploadData"
            class="bg-slate-800 hover:bg-slate-600 text-white py-2 px-4 rounded-md shadow-md focus:outline-none focus:ring-2 focus:ring-slate-600">
            Ejecutar predicción
        </button>
    </div>

    <div v-if="results !== null" class="mt-10">
        <h1>Predicciones del modelo:</h1>
        <div class="relative overflow-x-auto">
            <table class="w-full text-sm text-left text-gray-500">
                <thead class="text-xs text-gray-900 uppercase">
                    <tr>
                        <th scope="col" class="px-6 py-3">Age</th>
                        <th scope="col" class="px-6 py-3">IsFemale</th>
                        <th scope="col" class="px-6 py-3">Fare</th>
                        <th scope="col" class="px-6 py-3">HasCabin</th>
                        <th scope="col" class="px-6 py-3">Parch</th>
                        <th scope="col" class="px-6 py-3">Pclass</th>
                        <th scope="col" class="px-6 py-3">SibSp</th>
                        <th scope="col" class="px-6 py-3 font-bold">Survived</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(result, key) in results" :key="key" class="bg-white">
                        <td class="px-6 py-4">{{ result['Age'] }}</td>
                        <td class="px-6 py-4">{{ result['IsFemale'] }}</td>
                        <td class="px-6 py-4">{{ result['Fare'] }}</td>
                        <td class="px-6 py-4">{{ result['HasCabin'] }}</td>
                        <td class="px-6 py-4">{{ result['Parch'] }}</td>
                        <td class="px-6 py-4">{{ result['Pclass'] }}</td>
                        <td class="px-6 py-4">{{ result['SibSp'] }}</td>
                        <td class="px-6 py-4 font-bold text-gray-900 whitespace-nowrap">{{ result['Survived'] }}</td>
                    </tr>
                </tbody>
            </table>
        </div>


    </div>
</template>
  
<script setup>
import Papa from 'papaparse';
import { ref } from 'vue';

import { useFirebaseData } from '../firebase/useData';
const { results, writeQueryData } = useFirebaseData();

const selectedFile = ref(null);

const handleFileUpload = (event) => {
    const file = event.target.files[0];
    if (!file) {
        return;
    }
    selectedFile.value = file;
    console.log(selectedFile.value)
};

const uploadData = () => {
    if (!selectedFile.value) {
        alert('Please select a CSV file first.');
        return;
    }

    Papa.parse(selectedFile.value, {
        header: true,
        dynamicTyping: true,
        skipEmptyLines: true,
        complete: (result) => {
            // if (result.errors.length > 0) {
            //     console.log(result.errors[0])
            //     alert('Error parsing CSV file. Please check the format.');
            //     return;
            // }
            writeQueryData(result.data);
        },
    });
};

</script>
  