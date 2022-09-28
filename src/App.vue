<script setup>
import { ref, computed } from "vue";

const state = ref("init");

const amount = ref(1000);

const name = ref("Jack");
const salary = ref(1500);

const billId = ref("123456");

const persons = ref([
  {
    name: "John",
    salary: 1000,
  },
  {
    name: "Jane",
    salary: 2000,
  },
]);

function changeState(newState) {
  state.value = newState;
}

function createBill() {
  changeState("created");
}

function joinBillFromCode() {
  // TODO: Fetch bill from server and assign to billId
  changeState("created");
}
function addPerson() {
  persons.value.push({
    name: name.value,
    salary: salary.value,
  });
}

function getPercent(person) {
  if (totalSalaries.value === 0) {
    return 0;
  }
  return person.salary / totalSalaries.value;
}

const totalSalaries = computed(() => {
  return persons.value.reduce((acc, person) => {
    return acc + person.salary;
  }, 0);
});

function getFairPayAmount(person) {
  return Math.round(getPercent(person) * amount.value, 2);
}
</script>

<template>
  <h1 class="main-heading">FairPay</h1>
  <template v-if="state === 'init'">
    <button @click="changeState('setup')" class="btn">Create a bill</button>
    <button @click="changeState('join')" class="btn btn-secondary">
      Join a bill
    </button>
  </template>
  <template v-if="state === 'join'">
    <label class="form-label">Enter Bill Code</label>
    <input class="form-input" type="text" v-model="billId" />
    <button @click="joinBillFromCode" class="btn">Join</button>
  </template>
  <template v-if="state === 'setup'">
    <label class="form-label">Enter bill amount: </label>
    <input class="form-input" v-model="amount" type="number" />
    <button @click="createBill" class="btn">Create bill</button>
  </template>
  <template v-if="state === 'created'">
    <h2 class="sub-heading">Bill amount: ${{ amount }}</h2>
    <h3 class="info-heading">ID: {{ billId }}</h3>
    <div class="person-form-container">
      <label class="form-label">Enter a name</label>
      <input class="form-input" v-model="name" />
      <label class="form-label">Enter a salary</label>
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
          v-for="person in persons"
          :key="person.name"
          class="persons-table-row"
        >
          <td>{{ person.name }}</td>
          <td>${{ getFairPayAmount(person) }}</td>
        </tr>
      </tbody>
    </table>
  </template>
</template>

<style scoped></style>
