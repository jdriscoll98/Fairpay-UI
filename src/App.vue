<script setup>
import { ref, onMounted } from "vue";
import InitComponent from "@/components/InitComponent.vue";
import JoinComponent from "@/components/JoinComponent.vue";
import SetupComponent from "@/components/SetupComponent.vue";
import BillComponent from "@/components/BillComponent.vue";

const state = ref("init");

const amount = ref(0);
const amountError = ref("");

const billId = ref("");
const billIdError = ref("");

const persons = ref([]);

onMounted(() => {
  // if local storage has bill id, use it
  if (localStorage.getItem("billId")) {
    billId.value = localStorage.getItem("billId");
    changeState("created");
    // fetch bill data
    fetchBill(billId.value).then((res) => {
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

async function createBill(val) {
  if (!val || val <= 0) {
    amountError.value = "Amount must be greater than 0";
    return;
  }
  amountError.value = "";
  const res = await fetch("http://localhost:3000/api/bills", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      amount: val,
    }),
  });
  const data = await res.json();
  if (data.id) {
    billId.value = data.id;
    amount.value = val;
    window.localStorage.setItem("billId", billId.value);
    changeState("created");
  }
}

async function joinBillFromCode(code) {
  if (code.length !== 6) {
    billIdError.value = "Bill ID must be 6 characters";
    return;
  }
  billIdError.value = "";
  const res = await fetchBill(code);
  const data = await res.json();
  if (data) {
    billId.value = data.id;
    amount.value = data.amount;
    persons.value = data.persons;
    window.localStorage.setItem("billId", billId.value);
    changeState("created");
  } else {
    billIdError.value = "Bill ID not found";
  }
}

async function fetchBill(id) {
  return fetch("http://localhost:3000/api/bills/" + id, {
    method: "GET",
    headers: {
      "Content-Type": "application/json",
    },
  });
}

async function addPerson(nameVal, salaryVal) {
  const res = await fetch("http://localhost:3000/api/bills/" + billId.value, {
    method: "PATCH",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      name: nameVal,
      salary: salaryVal,
    }),
  });
  const data = await res.json();
  if (data) {
    persons.value = data;
  }
}

function leaveBill() {
  window.localStorage.removeItem("billId");
  persons.value = [];
  amount.value = 0;
  billId.value = "";
  clearErrors();
  changeState("init");
}

function clearErrors() {
  amountError.value = "";
  billIdError.value = "";
}
</script>

<template>
  <div :class="{ 'app-container': true, [`app-container-${state}`]: true }">
    <h1 class="main-heading">FairPay</h1>
    <InitComponent v-if="state === 'init'" @changeState="changeState" />
    <JoinComponent
      v-else-if="state === 'join'"
      @joinBill="joinBillFromCode"
      @leaveBill="leaveBill"
      :billIdError="billIdError"
    />
    <SetupComponent
      v-else-if="state === 'setup'"
      @createBill="createBill"
      @leaveBill="leaveBill"
      :amountError="amountError"
    />
    <BillComponent
      v-else-if="state === 'created'"
      :amount="amount"
      :persons="persons"
      :billId="billId"
      @addPerson="addPerson"
      @leaveBill="leaveBill"
    />
  </div>
</template>

<style scoped></style>
