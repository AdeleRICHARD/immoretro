<template>
  <div id="sidebar">
    <div
      class="color-picker"
      v-for="color in colors"
      :key="color"
      :style="{ backgroundColor: color }"
      @click="selectColor(color)"
    ></div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  emits: ['color-selected'],
  setup(_, { emit }) {
    const colors = ['#FFCDD2', '#F8BBD0', '#E1BEE7', '#D1C4E9', '#C5CAE9'];

    const selectColor = (color: string) => {
      emit('color-selected', color);
    };

    return {
      colors,
      selectColor,
    };
  },
});
</script>

<style scoped>
#sidebar {
  width: 60px;
  background-color: #fff;
  padding: 10px;
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
</style>
