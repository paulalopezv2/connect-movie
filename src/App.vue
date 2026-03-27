<script setup>
import { ref } from 'vue'
import axios from 'axios'

const API_KEY = '24286c9e'

const busqueda = ref('')
const peliculas = ref([])
const mensaje = ref('')
const loading = ref(false)

const buscarPeliculas = async () => {
  if (!busqueda.value.trim()) {
    mensaje.value = 'Escribe algo para buscar'
    peliculas.value = []
    return
  }

  try {
    loading.value = true
    mensaje.value = ''
    peliculas.value = []

    const response = await axios.get(
      `https://www.omdbapi.com/?apikey=${API_KEY}&s=${busqueda.value}`
    )

    if (response.data.Response === 'True') {
      peliculas.value = response.data.Search
    } else {
      mensaje.value = response.data.Error || 'No se encontraron películas'
    }
  } catch (error) {
    console.error(error)
    mensaje.value = 'Error al buscar'
  } finally {
    loading.value = false
  }
}
</script>

<template>
  <main class="min-h-screen bg-gradient-to-b from-pink-50 to-white py-12 px-4">
    <div class="max-w-6xl mx-auto">

      <!-- Encabezado -->
      <section class="text-center mb-10">
        <div class="text-6xl mb-4">🎬</div>
        <h1 class="text-4xl md:text-5xl font-bold text-gray-800 mb-3">
          Buscador de Películas
        </h1>
        <p class="text-gray-600 max-w-2xl mx-auto">
          Busca tus películas favoritas por título y revisa sus resultados de forma rápida y visual.
        </p>
      </section>

      <!-- Buscador -->
      <section class="bg-white shadow-sm rounded-3xl p-6 md:p-8 border border-pink-100 mb-10">
        <div class="flex flex-col md:flex-row gap-4">
          <input
            v-model="busqueda"
            @keyup.enter="buscarPeliculas"
            type="text"
            placeholder="Ej: Barbie, Batman, Titanic..."
            class="flex-1 border border-pink-200 rounded-full px-5 py-3 outline-none focus:ring-2 focus:ring-pink-300"
          />

          <button
            @click="buscarPeliculas"
            class="bg-black text-white px-8 py-3 rounded-full hover:bg-gray-800 transition"
          >
            Buscar
          </button>
        </div>
      </section>

      <!-- Estados -->
      <p v-if="loading" class="text-center text-gray-500 mb-8">
        Cargando películas...
      </p>

      <p v-if="mensaje" class="text-center text-pink-600 mb-8 font-medium">
        {{ mensaje }}
      </p>

      <!-- Resultados -->
      <section v-if="peliculas.length > 0">
        <h2 class="text-2xl font-semibold text-gray-800 mb-6 text-center">
          Resultados encontrados
        </h2>

        <div class="grid sm:grid-cols-2 lg:grid-cols-3 gap-8">
          <article
            v-for="pelicula in peliculas"
            :key="pelicula.imdbID"
            class="bg-white rounded-3xl overflow-hidden shadow-sm border border-pink-100 hover:shadow-md transition"
          >
            <img
              :src="pelicula.Poster !== 'N/A'
                ? pelicula.Poster
                : 'https://via.placeholder.com/300x450?text=Sin+Imagen'"
              :alt="pelicula.Title"
              class="w-full h-[420px] object-cover"
            />

            <div class="p-5 text-center">
              <h3 class="text-xl font-semibold text-gray-800 mb-2">
                {{ pelicula.Title }}
              </h3>

              <p class="text-gray-500 text-sm">
                Año: {{ pelicula.Year }}
              </p>
            </div>
          </article>
        </div>
      </section>
    </div>
  </main>
</template>