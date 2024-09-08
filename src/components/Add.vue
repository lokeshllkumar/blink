<script setup>
import { ref } from 'vue';
import { useToast } from 'vue-toastification';

const date = ref('');
const description = ref('');
const amount = ref('');

const emit = defineEmits(['transactionSubmitted']);

const toast = useToast();

const addTransaction = () => {
    if (!date.value || !description.value || !amount.value) {
        toast.error('All fields must be filled');
        return;
    }

    const transactionData = {
        date: date.value,
        description: description.value,
        amount: parseFloat(amount.value)
    }

    emit('transactionSubmitted', transactionData);

    date.value = '';
    description.value = '';
    amount.value = '';
};

</script>

<template>
    <form id="form" @submit.prevent="addTransaction">
        <div class="form-control">
            <label for="date">Date</label>
            <br />
            <input type="date" id="date" placeholder="Date" v-model="date" />
        </div>
        <div class="form-control">
            <label for="description">Description</label>
            <input type="text" id="description" placeholder="Description" v-model="description" />
        </div>
        <div class="form-control">
            <label for="amount"
            >Amount</label>
            <input type="number" id="amount" placeholder="Amount" v-model="amount" />
        </div>
        <button class="btn">Add transaction</button>
    </form>
</template>