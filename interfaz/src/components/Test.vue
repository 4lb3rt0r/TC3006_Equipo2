<template>
    <div class="flex justify-center space-x-2 w-full items-center mb-5">
        <Switch v-model="enabled" :class="enabled ? 'bg-slate-800' : 'bg-slate-500'"
            class="relative inline-flex h-[38px] w-[74px] shrink-0 cursor-pointer rounded-full border-2 border-transparent transition-colors duration-200 ease-in-out focus:outline-none focus-visible:ring-2 focus-visible:ring-white focus-visible:ring-opacity-75">
            <span aria-hidden="true" :class="enabled ? 'translate-x-9' : 'translate-x-0'"
                class="pointer-events-none inline-block h-[34px] w-[34px] transform rounded-full bg-white shadow-lg ring-0 transition duration-200 ease-in-out" />
        </Switch>
        <h1 class="font-semibold">
            {{ enabled ? "Archivo" : "Formulario" }}
        </h1>
    </div>

    <div class="flex items-center space-x-2" v-if="enabled">
        <input type="file" ref="fileInput" @change="handleFileUpload" accept=".csv"
            class="bg-gray-200 py-2 px-4 rounded-md shadow-md focus:outline-none focus:ring-2 focus:ring-slate-900" />
        <button @click="uploadData"
            class="bg-slate-800 hover:bg-slate-600 text-white py-2 px-4 rounded-md shadow-md focus:outline-none focus:ring-2 focus:ring-slate-600">
            Ejecutar predicción
        </button>
    </div>

    <div class="w-full max-w-xs" v-else>
        <form class="px-8 pt-6 pb-8 mb-4">
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="username">
                    Username
                </label>
                <input
                    class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                    id="username" type="text" placeholder="Username">
            </div>
            <div class="mb-6">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="password">
                    Password
                </label>
                <input
                    class="shadow appearance-none border border-red-500 rounded w-full py-2 px-3 text-gray-700 mb-3 leading-tight focus:outline-none focus:shadow-outline"
                    id="password" type="password" placeholder="******************">
                <p class="text-red-500 text-xs italic">Please choose a password.</p>
            </div>
            <div class="flex items-center justify-between">
                <button
                    class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
                    type="button">
                    Sign In
                </button>
                <a class="inline-block align-baseline font-bold text-sm text-blue-500 hover:text-blue-800" href="#">
                    Forgot Password?
                </a>
            </div>
        </form>
        <p class="text-center text-gray-500 text-xs">
            &copy;2020 Acme Corp. All rights reserved.
        </p>
    </div>

    <div v-if="results !== null" class="mt-10">
        <h1 class="font-semibold" >Predicciones del modelo:</h1>
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
import { Switch } from '@headlessui/vue'

import { useFirebaseData } from '../firebase/useData';

const { results, writeQueryData } = useFirebaseData();

const selectedFile = ref(null);
const enabled = ref(false);

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
  