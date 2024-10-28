<template>
  <SideBar @color-selected="changeSelectedNoteColor" />
  <div id="main-content">
    <button @click.stop="addNote" id="addNoteBtn">Ajouter un post-it</button>
    <div id="board" @click="deselectNote">
      <Note
        v-for="note in notes"
        :key="note.id"
        :note="note"
        :selected="note.id === selectedNoteId"
        @update:note="updateNote"
        @delete-note="deleteNote"
        @note-selected="selectNote"
      />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import Note from './StickyNote.vue';
import SideBar from './SideBar.vue';

export default defineComponent({
  components: { Note, SideBar },
  setup() {
    const notes = ref([]);
    const noteId = ref(0);
    const selectedNoteId = ref<number | null>(null);

    onMounted(() => {
      const savedNotes = JSON.parse(localStorage.getItem('notes') || '[]');
      if (savedNotes.length) {
        notes.value = savedNotes;
        noteId.value = notes.value.reduce((maxId, note) => Math.max(note.id, maxId), 0) + 1;
      }
    });

    const addNote = () => {
      const newNote = {
        id: noteId.value++,
        content: '',
        top: 100,
        left: 100,
        color: '#fff475',
      };
      notes.value.push(newNote);
      saveNotes();
    };

    const updateNote = (updatedNote) => {
      const index = notes.value.findIndex(note => note.id === updatedNote.id);
      if (index !== -1) {
        notes.value.splice(index, 1, updatedNote);
        saveNotes();
      }
    };

    const deleteNote = (id) => {
      notes.value = notes.value.filter(note => note.id !== id);
      saveNotes();
    };

    const selectNote = (id) => {
      selectedNoteId.value = id;
    };

    const deselectNote = () => {
      selectedNoteId.value = null;
    };

    const saveNotes = () => {
      localStorage.setItem('notes', JSON.stringify(notes.value));
    };

    const changeSelectedNoteColor = (color) => {
      if (selectedNoteId.value !== null) {
        const note = notes.value.find(note => note.id === selectedNoteId.value);
        if (note) {
          note.color = color;
          updateNote(note);
        }
      }
    };

    return {
      notes,
      addNote,
      updateNote,
      deleteNote,
      selectNote,
      deselectNote,
      changeSelectedNoteColor,
    };
  },
});
</script>

<style scoped>
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background-color: #f0f0f0;
  display: flex;
}

#sidebar {
  width: 60px;
  background-color: #fff;
  padding: 10px;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  align-items: center;
  border-right: 1px solid #ccc;
}

.color-picker {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  margin-bottom: 10px;
  cursor: pointer;
  border: 1px solid #ccc;
}

#main-content {
  flex: 1;
  padding: 20px;
}

#addNoteBtn {
  margin-bottom: 10px;
  padding: 10px 20px;
  font-size: 16px;
  background-color: #6200ee;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

#addNoteBtn:hover {
  background-color: #3700b3;
}

#board {
  position: relative;
  width: 100%;
  height: calc(100vh - 100px);
  border: 1px solid #ccc;
  background-color: #fff;
  overflow: hidden;
}

.note {
  position: absolute;
  width: 150px;
  height: 150px;
  background-color: #fff475;
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 10px;
  box-sizing: border-box;
  cursor: move;
}

.note textarea {
  width: 100%;
  height: 100%;
  border: none;
  background: transparent;
  resize: none;
  outline: none;
  font-size: 14px;
}

.delete-btn {
  position: absolute;
  top: 5px;
  right: 5px;
  background: transparent;
  border: none;
  font-size: 18px;
  cursor: pointer;
}

.note.selected {
  outline: 2px solid blue;
}
</style>
