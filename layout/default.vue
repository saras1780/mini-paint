<template>
    <div>
        <header class="bg-blue-500 text-white p-4">
            <h1 class="text-2xl font-bold text-center">Mini-Paint</h1>
        </header>

        <main class="container mx-auto p-4">
            <slot />
        </main>

        <footer class="bg-gray-800 text-white text-center p-4 mt-8">
            <p>&copy; 2024 Mini-Paint - Tous droits réservés</p>
        </footer>
    </div>
</template>

<script>
export default {
    // Logic specific to this layout can go here
}
</script>
