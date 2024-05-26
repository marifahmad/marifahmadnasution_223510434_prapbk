<template>
    <div>
        <h2 class="headingpostandtodos" >
            {{ title }}
        </h2>
    </div>
    </template>

<script setup>
import { ref, defineProps } from 'vue'

const title =ref('Post And To do List')
const props = defineProps({
  title: String,
})
</script>


<style>
.headingpostandtodos{
    font-size: 50px;
    color: #30b416;
}
</style>