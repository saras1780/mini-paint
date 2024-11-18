<template>
  <div>
    <!-- Header global de l'application -->
    <header class="bg-rose-700 text-white py-8 px-4 shadow-md">
      <h1 class="text-3xl font-extrabold text-center">🎨 Mini-Paint</h1>
      <p class="text-lg text-center mt-2">
        Créez, modifiez et sauvegardez vos dessins facilement !
      </p>
    </header>

    <!-- Contenu principal rendu par Nuxt -->
    <main class="container mx-auto p-4">
      <NuxtPage />
    </main>

    <!-- Footer global de l'application -->
    <!-- Footer -->
    <footer class="bg-rose-700 text-white py-8 px-4 shadow-md">
      <p class="text-lg text-center mt-2">
        Mini-Paint © {{ new Date().getFullYear() }} | Créé avec ❤️ par toi !
      </p>
    </footer>


  </div>
</template>

<script>
export default {
  // Toute logique ou data globale peut être définie ici
}
</script>

<style>
/* Styles globaux spécifiques, si nécessaire */
body {
  font-family: 'Arial', sans-serif;
}
</style>
