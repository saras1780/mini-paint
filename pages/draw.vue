<template>
    <div class="h-screen flex flex-col bg-gray-50">
        <!-- Barre d'outils latérale -->
        <div class="flex flex-row flex-grow">
            <aside class="w-1/5 bg-rose-100 p-6 shadow-lg flex flex-col justify-between">
                <div class="space-y-6">
                    <h2 class="text-xl font-bold text-rose-700 mb-2">Outils</h2>
                    <button @click="selectTool('pencil')" class="btn-tool">✏️ Crayon</button>
                    <button @click="selectTool('eraser')" class="btn-tool">🧽 Gomme</button>
                    <button @click="selectTool('brush')" class="btn-tool">🖌️ Pinceau</button>

                    <h2 class="text-xl font-bold text-rose-700">Personnalisation</h2>
                    <label class="block mb-2 text-gray-700">🎨 Couleur</label>
                    <input type="color" v-model="strokeColor" class="border rounded p-1 mb-4" />
                    <label class="block mb-2 text-gray-700">✂️ Taille</label>
                    <input type="range" v-model="strokeWidth" min="1" max="20" class="slider mb-4"
                        title="Taille du pinceau" />
                    <label class="block mb-2 text-gray-700">🌫️ Opacité</label>
                    <input type="range" v-model="opacity" min="0.1" max="1" step="0.1" class="slider mb-4"
                        title="Opacité" />
                </div>

                <!-- Actions globales avec espacement -->
                <div class="space-y-4 mt-6">
                    <h2 class="text-xl font-bold text-rose-700">Actions Globales</h2>
                    <button @click="undo" class="btn-action">⏪ Annuler</button>
                    <button @click="redo" class="btn-action">⏩ Refaire</button>
                    <button @click="saveDrawing" class="btn-action">💾 Enregistrer</button>
                    <button @click="goToHome" class="btn-action bg-rose-700 text-white">🏠 Retour</button>
                </div>
            </aside>

            <!-- Zone de dessin -->
            <main class="flex-1 bg-white shadow-lg m-6 p-4 rounded-lg border">
                <canvas id="drawingCanvas" class="w-full h-full rounded-md shadow-sm"></canvas>
            </main>
        </div>
    </div>
</template>


<script>
export default {
    data() {
        return {
            canvas: null,
            context: null,
            tool: 'pencil',
            isDrawing: false,
            startX: 0,
            startY: 0,
            strokeColor: '#000000',
            strokeWidth: 2,
            opacity: 1,
            history: [],
            redoStack: [],
        }
    },
    methods: {
        selectTool(tool) {
            this.tool = tool
        },
        startDrawing(event) {
            this.isDrawing = true
            this.startX = event.offsetX
            this.startY = event.offsetY
            this.context.strokeStyle = this.strokeColor
            this.context.lineWidth = this.strokeWidth
            this.context.globalAlpha = this.opacity
            this.context.beginPath()
            this.context.moveTo(this.startX, this.startY)
        },
        draw(event) {
            if (!this.isDrawing) return
            const x = event.offsetX
            const y = event.offsetY
            if (this.tool === 'pencil' || this.tool === 'brush') {
                this.context.lineTo(x, y)
                this.context.stroke()
            } else if (this.tool === 'eraser') {
                this.context.clearRect(x, y, this.strokeWidth, this.strokeWidth)
            }
        },
        stopDrawing() {
            if (this.isDrawing) {
                this.saveState()
                this.isDrawing = false
                this.context.closePath()
            }
        },
        saveState() {
            const dataURL = this.canvas.toDataURL()
            this.history.push(dataURL)
            this.redoStack = []
        },
        undo() {
            if (this.history.length > 1) {
                const lastState = this.history.pop()
                this.redoStack.push(lastState)
                this.restoreState(this.history[this.history.length - 1])
            }
        },
        redo() {
            if (this.redoStack.length > 0) {
                const redoState = this.redoStack.pop()
                this.history.push(redoState)
                this.restoreState(redoState)
            }
        },
        restoreState(dataURL) {
            const img = new Image()
            img.src = dataURL
            img.onload = () => {
                this.context.clearRect(0, 0, this.canvas.width, this.canvas.height)
                this.context.drawImage(img, 0, 0)
            }
        },
        saveDrawing() {
            const dataURL = this.canvas.toDataURL()
            const savedImages = JSON.parse(localStorage.getItem('images')) || []
            const name = prompt('Nom de votre création :', `Création ${savedImages.length + 1}`)
            if (name) {
                savedImages.push({ name, src: dataURL })
                localStorage.setItem('images', JSON.stringify(savedImages))
                alert('Dessin enregistré avec succès !')
            }
        },
        goToHome() {
            this.$router.push('/')
        },
        loadImageToEdit() {
            const imageToEdit = JSON.parse(localStorage.getItem('imageToEdit'))
            if (imageToEdit) {
                const img = new Image()
                img.src = imageToEdit.src
                img.onload = () => {
                    this.context.clearRect(0, 0, this.canvas.width, this.canvas.height)
                    this.context.drawImage(img, 0, 0)
                }
                localStorage.removeItem('imageToEdit') // Nettoie après le chargement
            }
        },
    },
    mounted() {
        this.canvas = document.getElementById('drawingCanvas')
        this.context = this.canvas.getContext('2d')
        this.canvas.width = this.canvas.offsetWidth
        this.canvas.height = this.canvas.offsetHeight

        this.context.strokeStyle = this.strokeColor
        this.context.lineWidth = this.strokeWidth
        this.context.globalAlpha = this.opacity
        this.context.lineCap = 'round'

        this.saveState()

        this.canvas.addEventListener('mousedown', this.startDrawing)
        this.canvas.addEventListener('mousemove', this.draw)
        this.canvas.addEventListener('mouseup', this.stopDrawing)
        this.canvas.addEventListener('mouseout', this.stopDrawing)

        // Charger l'image à modifier si elle existe
        this.loadImageToEdit()
    },
}
</script>
