<template>
  <RetroBoard />
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import RetroBoard from './components/RetroBoard.vue';

export default defineComponent({
  name: 'App',
  components: {
    RetroBoard,
  },
});
</script>
