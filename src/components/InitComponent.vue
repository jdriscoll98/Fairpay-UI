<script setup>
import { defineEmits } from "vue";
const emit = defineEmits(["changeState", "createBill"]);
</script>

<template>
  <div class="init-menu">
    <button @click="emit('changeState', 'setup')" class="btn">
      Create a bill
    </button>
    <button @click="emit('changeState', 'join')" class="btn btn-secondary">
      Join a bill
    </button>
  </div>
</template>
