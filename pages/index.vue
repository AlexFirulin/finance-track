<template>
  <section class="flex items-center justify-between mb-10">
    <h1 class="text-4xl font-extrabold">Summary</h1>
    <div>
      <ClientOnly>
        <UInputMenu v-model="viewSelected"
                    :options="transactionView" />
      </ClientOnly>
    </div>
  </section>
  <section class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 sm:gap-16 mb-10">
    <trends color="green"
            title="Income"
            :amount="incomeTotal"
            :lastAmount="prevIncomeTotal"
            :loading="pending" />
    <trends color="red"
            title="Expense"
            :amount="expenseTotal"
            :lastAmount="prevExpenseTotal"
            :loading="pending" />
    <trends color="green"
            title="Investments"
            :amount="4000"
            :lastAmount="3000"
            :loading="pending" />
    <trends color="red"
            title="Savings"
            :amount="1000"
            :lastAmount="2500"
            :loading="pending" />
  </section>

  <section class="flex justify-between mb-10 align-baseline">
    <div>
      <h2 class="text-2xl font-extrabold">Transactions</h2>
      <div class="text-gray dark:text-gray-400">
        You have {{ incomeCount }} incomes and {{ expenseCount }} this period
      </div>
    </div>
    <div>
      <transaction-modal v-model="isOpen"
                         @saved="refresh()" />
      <UButton icon="i-heroicons-plus-circle"
               color="white"
               variant="solid"
               label="Add"
               @click="isOpen = true" />
    </div>
  </section>

  <section v-if="!pending">
    <div v-for="(transactionOnDay, date) in byDate"
         :key="date"
         class="mb-10">
      <daily-transaction-summary :date="date"
                                 :transactions="transactionOnDay" />
      <Transaction v-for="transaction in transactionOnDay"
                   :key="transaction.id"
                   :transaction="transaction"
                   @edited="refresh()" />
    </div>
  </section>
  <USkeleton class="h-8 w-full mb-2"
             v-for="i in 4"
             :key="i"
             v-else />
</template>


<script setup>
import { transactionView } from '~/constants'
const viewSelected = ref(transactionView[1])
const isOpen = ref(false)
const { current, previous } = useSelectedTimePeriod(viewSelected)

const { pending, refresh, transactions: {
  incomeCount,
  expenseCount,
  incomeTotal,
  expenseTotal,
  grouped: {
    byDate
  }
} } = useFetchTransactions(current)

const { refresh:refreshPrevious, transactions: {
  incomeTotal: prevIncomeTotal,
  expenseTotal: prevExpenseTotal,

} } = useFetchTransactions(previous)

await refreshPrevious()
</script>