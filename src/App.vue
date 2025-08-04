<script setup>
import { ref, watch, onMounted } from "vue";

const showModal = ref(false);
const newNote = ref("");
const notes = ref([]);
const errorMessage = ref("");

// Função para dar uma cor aleatória para as notas.
function getRandomColor() {
  return "hsl(" + Math.random() * 360 + ", 100%, 75%)";
}

// Função para criar uma nota.
const addNote = () => {
  // Retorna um erro caso não tenha nada escrito.
  if (newNote.value.length <= 3) {
    return (errorMessage.value = "Obrigatório ter 3 ou mais caracteres.");
  }
  notes.value.push({
    id: Math.floor(Math.random() * 100000),
    text: newNote.value,
    date: new Date(),
    backgroundColor: getRandomColor(),
  });
  errorMessage.value = "";
  showModal.value = false;
  newNote.value = "";
};

// Salvar as notas no localStorage.
watch(
  notes,
  (newNote) => {
    localStorage.setItem("notes", JSON.stringify(newNote));
  },
  { deep: true }
);

// Puxar as notas do localStorage.
onMounted(() => {
  const savedNotes = localStorage.getItem("notes");
  if (savedNotes) {
    const parsedNotes = JSON.parse(savedNotes);

    // Erro de não conseguir criar nota por conta da data foi corrigido.
    notes.value = parsedNotes.map((note) => {
      return {
        ...note,
        date: new Date(note.date),
      };
    });
  }
});

// Função para excluir nota.
const deleteNote = (noteId) => {
  notes.value = notes.value.filter((note) => note.id !== noteId);
};
</script>

<template>
  <main>
    <div v-if="showModal" class="overlay">
      <div class="modal" @keypress.enter="addNote">
        <textarea
          v-model.trim="newNote"
          name="note"
          id="note"
          cols="30"
          rows="10"
        ></textarea>
        <p v-if="errorMessage">{{ errorMessage }}</p>
        <button @click="addNote">Criar Nota</button>
        <button class="close" @click="showModal = false">Cancelar</button>
      </div>
    </div>

    <div class="container">
      <header>
        <h1>Notas</h1>
        <button @click="showModal = true">+</button>
      </header>
      <div class="cards-container">
        <div
          v-for="note in notes"
          :key="note.id"
          class="card"
          :style="{ backgroundColor: note.backgroundColor }"
        >
          <p class="main-text">{{ note.text }}</p>
          <div class="date-and-delete">
            <p class="date">{{ note.date.toLocaleDateString("pt-BR") }}</p>
            <button @click="deleteNote(note.id)" class="delete" title="Excluir Nota">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                width="20"
                height="20"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                class="lucide lucide-trash2-icon lucide-trash-2"
              >
                <path d="M10 11v6" />
                <path d="M14 11v6" />
                <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6" />
                <path d="M3 6h18" />
                <path d="M8 6V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2" />
              </svg>
            </button>
          </div>
        </div>
      </div>
    </div>
    <div class="direitos">
      <p>{ © 2025 Augusto Almeida }</p>
    </div>
  </main>
</template>

<style scoped>
main {
  font-family: Helvetica, sans-serif;
  color: #222;
}

.container {
  max-width: 1000px;
  padding: 10px;
  margin: 0 auto;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

h1 {
  font-weight: bold;
  font-size: 4.7rem;
}
header button {
  border: none;
  padding: 10px;
  width: 50px;
  height: 50px;
  cursor: pointer;
  background-color: #222;
  border-radius: 100%;
  color: white;
  font-size: 1.25rem;
}

.card {
  width: 225px;
  height: 225px;
  padding: 10px;
  border-radius: 20px;
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  margin-right: 20px;
  margin-bottom: 20px;
  word-wrap: break-word;
}
.date-and-delete {
  display: flex;
  justify-content: space-between;
}
.date-and-delete button {
  width: 40px;
  height: 40px;
  border: 2px solid rgba(0, 0, 0, 0.5);
  border-radius: 100%;
  color: rgba(0, 0, 0, 0.5);
  background-color: transparent;
  cursor: pointer;
}

.date {
  font-size: 0.8rem;
  font-weight: 900;
}

.cards-container {
  display: flex;
  flex-wrap: wrap;
}

.overlay {
  position: fixed;
  width: 100%;
  height: 100%;
  overflow-y: hidden;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.modal {
  width: 500px;
  background-color: white;
  border-radius: 20px;
  padding: 30px;
  position: relative;
  display: flex;
  flex-direction: column;
  box-shadow: 0px 3px 5px #444;
}

.modal button {
  padding: 10px 20px;
  font-size: 1rem;
  width: 100%;
  background-color: #222;
  border: none;
  border-radius: 15px;
  color: #fff;
  cursor: pointer;
  margin-top: 20px;
}

.modal .close {
  border: 2px solid #222;
  background-color: #fff;
  color: #222;
  margin-top: 10px;
}
textarea {
  border-radius: 15px;
  resize: none;
  padding: 10px;
  font-size: 1.05rem;
  border: 2px solid #222;
  font-family: Helvetica, sans-serif;
}
.modal p {
  color: red;
  margin: 5px 0 0 0;
}
.direitos {
  text-align: center;
}
</style>
