<template>
    <div class="container mx-auto p-4">
        <h1 class="text-2xl font-bold mb-4">Galerie de Créations</h1>

        <!-- Barre de recherche -->
        <input type="text" v-model="searchQuery" placeholder="Rechercher une création..."
            class="border p-2 mb-4 w-full" />

        <!-- Bouton "Nouvelle création" -->
        <button @click="newCreation" class="mb-4 bg-rose-700 text-white py-2 px-4 rounded">
            Nouvelle création
        </button>

        <!-- Galerie d'images -->
        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4">
            <div v-for="(image, index) in filteredImages" :key="index" class="border rounded overflow-hidden">
                <img :src="image.src" :alt="image.name" class="w-full h-48 object-cover" />
                <div class="p-2">
                    <h2 class="text-lg font-semibold">{{ image.name }}</h2>
                    <!-- Bouton "Modifier" -->
                    <button @click="editCreation(index)" class="mt-2 bg-green-500 text-white py-1 px-3 rounded">
                        Modifier
                    </button>
                    <!-- Bouton "Supprimer" -->
                    <button @click="deleteCreation(index)" class="mt-2 bg-red-500 text-white py-1 px-3 rounded">
                        Supprimer
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            images: [],
            searchQuery: '',
        }
    },
    computed: {
        filteredImages() {
            if (!this.searchQuery) {
                return this.images
            }
            return this.images.filter((image) =>
                image.name.toLowerCase().includes(this.searchQuery.toLowerCase())
            )
        },
    },
    methods: {
        newCreation() {
            // Redirige vers la page de dessin pour une nouvelle création
            this.$router.push('/draw')
        },
        editCreation(index) {
            // Charge l'image sélectionnée et redirige vers la page de dessin
            const selectedImage = this.images[index]
            localStorage.setItem('imageToEdit', JSON.stringify(selectedImage))
            this.$router.push('/draw')
        },
        deleteCreation(index) {
            if (confirm('Êtes-vous sûr de vouloir supprimer cette création ?')) {
                this.images.splice(index, 1)
                localStorage.setItem('images', JSON.stringify(this.images))
            }
        },
    },
    mounted() {
        const savedImages = JSON.parse(localStorage.getItem('images')) || []
        this.images = savedImages
    },
}
</script>
