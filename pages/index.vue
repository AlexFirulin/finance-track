<template>
  <section class="flex items-center justify-between mb-10">
<h1 class="text-4xl font-extrabold">Summary</h1>
<div>
  <ClientOnly>
  <UInputMenu v-model="viewSelected" :options="transactionsView" />
</ClientOnly>
</div>
  </section>
  <section class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 sm:gap-16 mb-10">
    <trends color="green" title="Income" :amount="4000" :lastAmount="3000" :loading="false"/>
    <trends color="red" title="Expense" :amount="7000" :lastAmount="8000" :loading="false"/>
    <trends color="green" title="Investments" :amount="4000" :lastAmount="3000" :loading="false"/>
    <trends color="red" title="Savings" :amount="1000" :lastAmount="2500" :loading="false"/>
  </section>
</template>

<script setup>
import { transactionsView } from '~/constants';
const viewSelected = ref(transactionsView[1])

</script>