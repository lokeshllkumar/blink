<script setup>

import { defineProps } from 'vue';

const props = defineProps({
  transactions: {
    type: Array,
    requried: true,
  },
});

const emit = defineEmits(['transactionDeleted']);

const deleteTransaction = (id) => {
  emit('transactionDeleted', id);
};
</script>

<template>
    <ul id="list" class="list">
      <li v-for="transaction in transactions" v-bind:key="transaction.id">
        {{ transaction.date }} <span> {{ transaction.description }} </span> <span>$ {{ transaction.amount }}</span><button @click="deleteTransaction(transaction.id)" class="delete-btn">x</button>
      </li>
    </ul>
</template>