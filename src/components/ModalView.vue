<script lang="ts" setup>
import { useKeydown } from '../composition/useKeydown';

const props = defineProps<{
  closeModal: Function
}>();

useKeydown([{ key: 'Escape', fn: props.closeModal }]);
</script>
<template>
  <div class="modal">
    <div class="overlay" @click="closeModal"></div>
    <div class="modal-card">
      <slot />
    </div>
  </div>
</template>

<style scoped></style>