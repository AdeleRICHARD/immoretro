<template>
  <div
    class="note"
    :style="{ top: `${currentTop}px`, left: `${currentLeft}px`, backgroundColor: currentColor }"
    @mousedown="onMouseDown"
    @click.stop="changeColor"
  >
    <button class="delete-btn" @click.stop="deleteNote">×</button>
    <textarea v-model="content" @input="updateNote"></textarea>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from 'vue';

export default defineComponent({
  props: {
    note: {
      type: Object,
      required: true,
    },
    selected: {
      type: Boolean,
      default: false,
    },
  },
  emits: ['update:note', 'delete-note', 'note-selected'],
  setup(props, { emit }) {
    const currentTop = ref(props.note.top);
    const currentLeft = ref(props.note.left);
    const currentColor = ref(props.note.color);
    const content = ref(props.note.content);

    const colors = ['#FFCDD2', '#F8BBD0', '#E1BEE7', '#D1C4E9', '#C5CAE9'];

    watch(() => props.note, (newNote) => {
      currentTop.value = newNote.top;
      currentLeft.value = newNote.left;
      currentColor.value = newNote.color;
      content.value = newNote.content;
    });

    const updateNote = () => {
      emit('update:note', {
        ...props.note,
        top: currentTop.value,
        left: currentLeft.value,
        color: currentColor.value,
        content: content.value,
      });
    };

    const deleteNote = () => {
      emit('delete-note', props.note.id);
    };

    const changeColor = () => {
      const currentIndex = colors.indexOf(currentColor.value);
      const nextIndex = (currentIndex + 1) % colors.length;
      currentColor.value = colors[nextIndex];
      updateNote();
    };

    const onMouseDown = (event: MouseEvent) => {
      const shiftX = event.clientX - (event.target as HTMLElement).getBoundingClientRect().left;
      const shiftY = event.clientY - (event.target as HTMLElement).getBoundingClientRect().top;

      const onMouseMove = (e: MouseEvent) => {
        currentLeft.value = e.pageX - shiftX;
        currentTop.value = e.pageY - shiftY;
      };

      document.addEventListener('mousemove', onMouseMove);

      document.addEventListener(
        'mouseup',
        () => {
          document.removeEventListener('mousemove', onMouseMove);
          updateNote();
        },
        { once: true }
      );
    };

    return {
      currentTop,
      currentLeft,
      currentColor,
      content,
      updateNote,
      deleteNote,
      onMouseDown,
      changeColor,
    };
  },
});
</script>

<style scoped>
.note {
  position: relative;
  width: 150px;
  height: 150px;
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 10px;
  box-sizing: border-box;
  cursor: move;
  overflow: hidden;
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
  color: red;
}

.note.selected {
  outline: 2px solid blue;
}
</style>
