<template>
  <div>
    <Header />
    <div class="container">
      <Balance :total="+totalBalance"/>
      <IncomeExpenses :income="+totalIncome"  :expenses="+totalExpenses" />
      <TransactionList :transactions="transactions" @deleteTransaction="handleDeleteTransaction" />
      <AddTransaction  @transactionSubmitted="handleTransactionSubmitted"/>
    </div>
  </div>
</template>
<script setup>
import {ref, computed, onMounted} from 'vue'
import Header from  './components/Header.vue'
import Balance from './components/Balance.vue'
import IncomeExpenses from './components/IncomeExpenses.vue'
import TransactionList from './components/TransactionList.vue'
import AddTransaction from './components/AddTransaction.vue'
import {useToast} from 'vue-toastification'

const toast = useToast()

const transactions = ref([])
onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'))
  if(savedTransactions){
    transactions.value = savedTransactions
  }
})
// Get total
const totalBalance = computed(() =>{
  return transactions.value.reduce((acc, transaction) =>{
    return acc + transaction.amount
  } ,0)
})
 //GET Income
const totalIncome = computed(() => {
  return transactions.value.filter((transaction) => transaction.amount > 0)
  .reduce((acc, transaction)=> {
    return acc+ transaction.amount
  }, 0 )
  .toFixed(2)
})

 // Get Expense

 const totalExpenses = computed(() => {
  return transactions.value.filter((transaction) => transaction.amount < 0)
  .reduce((acc, transaction) => {
    return acc + transaction.amount
  } , 0) .toFixed(2)
 })
 const handleTransactionSubmitted = (transactionData)=> {
  console.log(transactionData)
  transactions.value.push({
    id: generateUniqueId(),
    text:transactionData.text,
    amount: transactionData.amount
  })
  saveTransactionsToLocalStorage()
 toast.success("Transaction Added")
 }
const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000)
}

// delete transaction
const handleDeleteTransaction = (id) =>{
  transactions.value = transactions.value.filter((transaction) => {
    return transaction.id !== id
  })
  saveTransactionsToLocalStorage()
  toast.success("Transaction Deleted")
}
// save to local storage

const saveTransactionsToLocalStorage = () =>{
  localStorage.setItem('transactions', JSON.stringify(transactions.value))
}
</script>

<style scoped>

</style>
