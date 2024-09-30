<script setup>
import Header from '@/components/Header.vue';
import Balance from '@/components/Balance.vue';
import List from '@/components/List.vue';
import Add from '@/components/Add.vue'
import Navbar from '@/components/Navbar.vue';

import { ref, computed, onMounted } from 'vue';
import { useToast } from 'vue-toastification';
import { db } from './firebase/firebaseInit';
import { collection, getDocs } from 'firebase/firestore';

const toast = useToast();

const transactions = ref([
]);

onMounted(async () => {
  const querySnapshot = await getDocs(collection(db, "transactions"));

  let localStore = [];

  querySnapshot.forEach((doc) => {
    console.log(doc.id, "->", doc.data());
    const transaction = {
      id: doc.id,
      date: doc.data().date,
      description: doc.data().description,
      amount: doc.data().amount,
    };
    localStore.push(transaction);
  });

  transactions.value = localStore;

  console.log(transactions);
  /*
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
    */
});

const total = computed(() => {
  return transactions.value
  .reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});
</script>

<template>
  <Navbar />
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <br />
    <Add />
    <br />
    <List /> 
  </div>
</template>