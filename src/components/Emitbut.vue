
<template>
  <button @click="updateText">Click me</button>
</template>

<script setup>
const emit = defineEmits(['response'])

function updateText() {
  emit('response', 'hello from child')
}
</script>