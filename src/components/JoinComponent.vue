<script setup>
import { defineEmits, ref } from "vue";
const emit = defineEmits(["changeState", "joinBill", "leaveBill"]);
const props = defineProps({
  billIdError: String,
});

const val = ref("");
</script>

<template>
  <div class="join-menu">
    <label class="form-label">Enter Bill Code</label>
    <span class="error-text">{{ props.billIdError }}</span>
    <input class="form-input" type="text" v-model="val" />
    <button @click="emit('joinBill', val)" class="btn">Join</button>
    <button @click="emit('leaveBill')" class="btn btn-small btn-important">
      Back
    </button>
  </div>
</template>
