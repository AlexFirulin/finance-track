<template>
  <section class="flex items-center justify-between mb-10">
    <h1 class="text-4xl font-extrabold">Summary</h1>
    <div>
      <ClientOnly>
        <UInputMenu v-model="viewSelected"
                    :options="transactionsView" />
      </ClientOnly>
    </div>
  </section>
  <section class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 sm:gap-16 mb-10">
    <trends color="green"
            title="Income"
            :amount="incomeTotal"
            :lastAmount="3000"
            :loading="isLoading" />
    <trends color="red"
            title="Expense"
            :amount="expenseTotal"
            :lastAmount="8000"
            :loading="isLoading" />
    <trends color="green"
            title="Investments"
            :amount="4000"
            :lastAmount="3000"
            :loading="isLoading" />
    <trends color="red"
            title="Savings"
            :amount="1000"
            :lastAmount="2500"
            :loading="isLoading" />
  </section>

  <section class="flex justify-between mb-10 align-baseline">
    <div>
      <h2 class="text-2xl font-extrabold">Transactions</h2>
      <div class="text-gray dark:text-gray-400">
        You have {{ incomeCount }} incomes and {{ expenseCount }} this period
      </div>
    </div>
    <div>
      <transaction-modal v-model="isOpen" @saved="refreshTransaction()" />
      <UButton icon="i-heroicons-plus-circle"
               color="white"
               variant="solid"
               label="Add"
               @click="isOpen = true" />
    </div>
  </section>

  <section v-if="!isLoading">
    <div v-for="(transactionOnDay, date) in transactionsGroupedByDate"
         :key="date"
         class="mb-10">
      <daily-transaction-summary :date="date"
                                 :transactions="transactionOnDay" />
      <Transaction v-for="transaction in transactionOnDay"
                   :key="transaction.id"
                   :transaction="transaction"
                   @deleted="refreshTransaction()" />
    </div>
  </section>
  <USkeleton class="h-8 w-full mb-2"
             v-for="i in 4"
             :key="i"
             v-else />
</template>

<script setup>
import { transactionsView } from '~/constants';
const viewSelected = ref(transactionsView[1])

const supabase = useSupabaseClient()
const transactions = ref([])
const isLoading = ref(false)
const isOpen = ref(false)

const income = computed(
  () => transactions.value.filter(t => t.type === 'Income')
)


const expense = computed(
  () => transactions.value.filter(t => t.type === 'Expense')
)

const incomeCount = computed(() => income.value.length)
const expenseCount = computed(() => expense.value.length)

const incomeTotal = computed(
  () => income.value.reduce((sum, transaction) => sum + transaction.amount, 0)
)
const expenseTotal = computed(
  () => income.value.reduce((sum, transaction) => sum + transaction.amount, 0)
)

const fetchTransactions = async () => {
  isLoading.value = true
  try {
    const { data } = await useAsyncData('transactions', async () => {
      const { data, error } = await supabase
        .from('transactions')
        .select()
        .order('created_at', { ascending: false })
      if (error) return []

      return data
    })

    return data.value

  } finally {
    isLoading.value = false
  }
}
const refreshTransaction = async () => {
  transactions.value = await fetchTransactions()
}
await refreshTransaction()

const transactionsGroupedByDate = computed(() => {
  let grouped = {}
  for (const transaction of transactions.value) {
    const date = new Date(transaction.created_at).toISOString().split('T')[0]
    if (!grouped[date]) {
      grouped[date] = []
    }
    grouped[date].push(transaction)
  }
  return grouped
})
</script>