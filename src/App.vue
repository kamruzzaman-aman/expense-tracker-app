<template>
  <div class="container">
    <h1>Expense Tracker</h1>
    <AddTransaction @transaction-added="loadTransactions" />
    <TransactionList :transactions="transactions" @transaction-deleted="loadTransactions" />
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import AddTransaction from './components/AddTransaction.vue';
import TransactionList from './components/TransactionList.vue';

export default {
  components: {
    AddTransaction,
    TransactionList
  },
  setup() {
    const transactions = ref([]);

    const loadTransactions = () => {
      const storedTransactions = localStorage.getItem('transactions');
      transactions.value = storedTransactions ? JSON.parse(storedTransactions) : [];
    };

    onMounted(loadTransactions);

    return { transactions, loadTransactions };
  }
};
</script>

<style>
@import 'bootstrap/dist/css/bootstrap.min.css';
</style>