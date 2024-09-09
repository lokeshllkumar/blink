<script setup>
import Header from '@/components/Header.vue';
import Balance from '@/components/Balance.vue';
import List from '@/components/List.vue';
import Add from '@/components/Add.vue'
import Navbar from '@/components/Navbar.vue';

import { RouterView } from 'vue-router';

import { ref, computed, onMounted } from 'vue';
import { useToast } from 'vue-toastification';
import { db } from './firebase/firebaseInit';
import { query, orderBy, addDoc, collection, onSnapshot, doc, deleteDoc } from 'firebase/firestore';

const toast = useToast();

const transactions = ref([
]);

const transactionsCollectionRef = collection(db, "transactions");
const transactionsCollectionQuery = query(transactionsCollectionRef, orderBy("date", "desc"));

onMounted(() => {
  /*
  const querySnapshot = await getDocs(collection(db, "transactions"));

  let localStore = [];

  querySnapshot.forEach((doc) => {
    console.log("Data fetched from database successfully!");
    const transaction = {
      id: doc.id,
      date: doc.data().date,
      description: doc.data().description,
      amount: doc.data().amount,
    };
    localStore.push(transaction);
  });

  transactions.value = localStore;

  */

  onSnapshot(transactionsCollectionQuery, (querySnapshot) => {
    const localStore = [];
    querySnapshot.forEach((doc) => {
      const transaction = {
        date: doc.data().date,
        description: doc.data().description,
        amount: doc.data().amount,
      };
      localStore.push(transaction);
    });
    transactions.value = localStore;
  })

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

const genUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
};

const handleTransactionSubmission = (transactionData) => {
  addDoc(transactionsCollectionRef, {
    date: transactionData.date,
    description: transactionData.description,
    amount: transactionData.amount
  });
  /*
  transactions.value.push({
    id: genUniqueId(),
    date: transactionData.date,
    description: transactionData.description,
    amount: transactionData.amount
  });
  

  saveTransactions();
  */

  toast.success('Transaction added');
};

const handleTransactionDeletion = (id) => {
  deleteDoc(doc(transactionsCollectionRef, id));
  /*
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id);

  saveTransactions();
  */

  toast.success('Transaction deleted');
};

/*
const saveTransactions = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}
*/
</script>

<template>
  <Navbar />
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <br />
    <Add @transactionSubmitted="handleTransactionSubmission" />
    <br />
    <List :transactions="transactions" @transactionDeleted="handleTransactionDeletion" /> 
  </div>
</template>