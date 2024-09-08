<script setup>
import Header from '@/components/Header.vue';
import Balance from '@/components/Balance.vue';
import List from '@/components/List.vue';
import Add from '@/components/Add.vue'
import Navbar from '@/components/Navbar.vue';

import { ref, computed, onMounted } from 'vue';
import { useToast } from 'vue-toastification';

const toast = useToast();

const transactions = ref([
]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
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

  transactions.value.push({
    id: genUniqueId(),
    date: transactionData.date,
    description: transactionData.description,
    amount: transactionData.amount
  });

  toast.success('Transaction added');
};

const handleTransactionDeletion = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id);

  toast.success('Transaction deleted');
};

const saveTransactions = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions, value));
}
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