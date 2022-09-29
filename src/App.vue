<script setup>
import { ref, computed, onMounted } from "vue";

const state = ref("init");

const amount = ref(0);

const name = ref("");
const salary = ref(0);

const billId = ref("");

const persons = ref([]);

onMounted(() => {
  // if local storage has bill id, use it
  if (localStorage.getItem("billId")) {
    billId.value = localStorage.getItem("billId");
    changeState("created");
    // fetch bill data
    fetchBill().then((res) => {
      res.json().then((data) => {
        persons.value = data.persons;
        amount.value = data.amount;
      });
    });
  }
});

function changeState(newState) {
  state.value = newState;
}

async function createBill() {
  const res = await fetch("http://137.184.221.21:3000/api/bills", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      amount: amount.value,
    }),
  });
  const data = await res.json();
  if (data.id) {
    billId.value = data.id;
    window.localStorage.setItem("billId", billId.value);
    changeState("created");
  }
}

async function joinBillFromCode() {
  const res = await fetchBill();
  const data = await res.json();
  if (data) {
    billId.value = data.id;
    amount.value = data.amount;
    persons.value = data.persons;
    // push billId to url
    window.localStorage.setItem("billId", billId.value);
    changeState("created");
  }
}

async function fetchBill() {
  return fetch("http://137.184.221.21:3000/api/bills/" + billId.value, {
    method: "GET",
    headers: {
      "Content-Type": "application/json",
    },
  });
}
async function addPerson() {
  const res = await fetch(
    "http://137.184.221.21:3000/api/bills/" + billId.value,
    {
      method: "PATCH",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        name: name.value,
        salary: salary.value,
      }),
    }
  );
  const data = await res.json();
  if (data) {
    persons.value = data;
    name.value = "";
    salary.value = 0;
  }
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

function leaveBill() {
  window.localStorage.removeItem("billId");
  changeState("init");
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
    <button @click="leaveBill()" class="btn btn-small">Leave bill</button>
  </template>
</template>

<style scoped></style>
