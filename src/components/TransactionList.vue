<template>
  <div>
    <div class="filter-buttons">
      <button @click="filterType = 'all'">All</button>
      <button @click="filterType = 'income'">Income</button>
      <button @click="filterType = 'expense'">Expense</button>
    </div>

    <table class="table">
      <thead>
        <tr>
          <th>Title</th>
          <th>Amount</th>
          <th>Type</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-if="paginatedTransactions.length === 0">
          <td colspan="4">No transactions recorded yet.</td>
        </tr>
        <tr v-for="transaction in paginatedTransactions" :key="transaction.id">
          <td>{{ transaction.title }}</td>
          <td :style="{ color: transaction.type === 'income' ? 'green' : 'red', fontWeight: Math.abs(transaction.amount) >= 500 ? 'bold' : 'normal' }">
            {{ transaction.amount }}
          </td>
          <td :style="{ textTransform: 'uppercase' }">{{ transaction.type }}</td>
          <td>
            <button @click="deleteTransaction(transaction.id)" class="btn btn-danger btn-sm">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>

    <div class="pagination ">
      <button :disabled="currentPage === 1" @click="currentPage--">Previous</button>
      <span>Page {{ currentPage }} of {{ totalPages }}</span>
      <button :disabled="currentPage === totalPages" @click="currentPage++">Next</button>
    </div>
  </div>
</template>

<script>
import { computed, ref } from 'vue';

export default {
  props: ['transactions'],
  emits: ['transaction-deleted'],
  setup(props, { emit }) {
    const filterType = ref('all');
    const currentPage = ref(1);
    const itemsPerPage = 5; // Number of transactions per page

    const filteredTransactions = computed(() => {
      if (filterType.value === 'all') {
        return props.transactions;
      } else {
        return props.transactions.filter(transaction => transaction.type === filterType.value);
      }
    });

    const totalPages = computed(() => Math.ceil(filteredTransactions.value.length / itemsPerPage));

    const paginatedTransactions = computed(() => {
      const start = (currentPage.value - 1) * itemsPerPage;
      const end = start + itemsPerPage;
      return filteredTransactions.value.slice(start, end);
    });

    const deleteTransaction = (id) => {
      let storedTransactions = localStorage.getItem('transactions');
      let transactions = storedTransactions ? JSON.parse(storedTransactions) : [];
      transactions = transactions.filter(transaction => transaction.id !== id);

      localStorage.setItem('transactions', JSON.stringify(transactions));
      emit('transaction-deleted');
    };

    return { filteredTransactions, paginatedTransactions, filterType, currentPage, totalPages, deleteTransaction };
  }
};
</script>

<style scoped>
.filter-buttons {
  margin-bottom: 10px;
}

.pagination {
  margin-top: 10px;
  display: flex;
  justify-content: space-between;
}

.pagination button {
  padding: 5px 10px;
  margin: 0 5px;
  border: 1px solid #ffffff;
  background-color: #d0c8c8;
  cursor: pointer;
}

.pagination button:disabled {
  opacity: 0.5;
  cursor: default;
}
</style>