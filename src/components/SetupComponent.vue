<script setup>
import { defineEmits, defineProps, ref } from "vue";
const emit = defineEmits(["createBill", "leaveBill"]);
const props = defineProps({
  amountError: String,
});

const val = ref(0);
</script>

<template>
  <div class="create-menu">
    <label class="form-label">Enter bill amount ($):</label>
    <span class="error-text">{{ props.amountError }}</span>
    <input class="form-input" v-model="val" type="number" />
    <button @click="emit('createBill', val)" class="btn">Create bill</button>
    <button @click="emit('leaveBill')" class="btn btn-small btn-important">
      Back
    </button>
  </div>
</template>
