<template>
    <form @submit.prevent="addTransaction" class="row g-3 align-items-end">
    <div class="col-md-4">
      <label for="title" class="form-label">Title</label>
      <input type="text" class="form-control" id="title" v-model="newTransaction.title" required>
      <div v-if="titleError" class="text-danger">{{ titleError }}</div>
    </div>
    <div class="col-md-3">
      <label for="amount" class="form-label">Amount</label>
      <input type="number" class="form-control" id="amount" v-model="newTransaction.amount" required>
      <div v-if="amountError" class="text-danger">{{ amountError }}</div>
    </div>
    <div class="col-md-3">
      <label for="type" class="form-label">Type</label>
      <select class="form-select" id="type" v-model="newTransaction.type">
        <option value="income">Income</option>
        <option value="expense">Expense</option>
      </select>
    </div>
    <div class="col-md-2">
      <button type="submit" class="btn btn-primary w-100">Add</button>
    </div>
  </form>
</template>

<script>
import { ref, reactive } from 'vue';

export default {
  emits: ['transaction-added'],
  setup(props, { emit }) {
    const newTransaction = reactive({
      title: '',
      amount: null,
      type: 'income'
    });

    const titleError = ref(null);
    const amountError = ref(null);

    const addTransaction = () => {
      titleError.value = newTransaction.title.trim() === '' ? 'Title is required' : null;
      amountError.value = newTransaction.amount === null || newTransaction.amount <= 0 ? 'Amount must be greater than 0' : null;

      if (titleError.value || amountError.value) {
        return;
      }

      const transactionToAdd = {
        id: Date.now(),
        title: newTransaction.title.trim(),
        amount: parseFloat(newTransaction.amount),
        type: newTransaction.type
      };

      let storedTransactions = localStorage.getItem('transactions');
      const transactions = storedTransactions ? JSON.parse(storedTransactions) : [];
      transactions.push(transactionToAdd);

      localStorage.setItem('transactions', JSON.stringify(transactions));

      newTransaction.title = '';
      newTransaction.amount = null;

      emit('transaction-added');
    };

    return { newTransaction, titleError, amountError, addTransaction };
  }
};
</script>