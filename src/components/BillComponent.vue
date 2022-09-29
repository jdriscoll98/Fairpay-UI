<script setup>
import { defineEmits, defineProps, ref, computed } from "vue";

const emit = defineEmits(["addPerson", "leaveBill"]);
const props = defineProps({
  amount: Number,
  persons: Array,
  billId: String,
});

const totalSalaries = computed(() => {
  return props.persons.reduce((acc, person) => {
    return acc + person.salary;
  }, 0);
});

function getPercent(person) {
  if (totalSalaries.value === 0) {
    return 0;
  }
  return person.salary / totalSalaries.value;
}
function getFairPayAmount(person) {
  return Math.round(getPercent(person) * props.amount, 2);
}

const name = ref("");
const salary = ref("");

const nameError = ref("");
const salaryError = ref("");

function addPerson() {
  let valid = true;
  if (!name.value) {
    nameError.value = "Name is required";
    valid = false;
  }
  if (!salary.value || salary.value <= 0) {
    salaryError.value = "Salary must be greater than 0";
    valid = false;
  }
  if (valid) {
    emit("addPerson", {
      name: name.value,
      salary: salary.value,
    });
    name.value = "";
    salary.value = "";
    nameError.value = "";
    salaryError.value = "";
  }
}
</script>

<template>
  <div class="bill-menu">
    <h2 class="sub-heading">Bill amount: ${{ props.amount }}</h2>
    <h3 class="info-heading">ID: {{ props.billId }}</h3>
    <div class="person-form-container">
      <label class="form-label">Enter a name</label>
      <span class="error-text">{{ nameError }}</span>
      <input class="form-input" v-model="name" />
      <label class="form-label">Enter a salary</label>
      <span class="error-text">{{ salaryError }}</span>
      <input class="form-input" v-model="salary" type="number" />
      <button @click="addPerson" class="btn btn-small">Add person</button>
    </div>
    <h2 class="sub-heading">Participants</h2>
    <table class="persons-table">
      <thead>
        <tr>
          <th>Name</th>
          <th>Amount</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="person in props.persons"
          :key="person.name"
          class="persons-table-row"
        >
          <td>{{ person.name }}</td>
          <td>${{ getFairPayAmount(person) }}</td>
        </tr>
      </tbody>
    </table>
    <button @click="emit('leaveBill')" class="btn btn-small btn-important">
      Back
    </button>
  </div>
</template>
