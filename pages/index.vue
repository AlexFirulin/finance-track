<template>
  <section class="flex items-center justify-between mb-10">
<h1 class="text-4xl font-extrabold">Summary</h1>
<div>
  <UInputMenu v-model="viewSelected" :options="transactionsView" />
</div>
  </section>
  <section class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 sm:gap-16 mb-10">
    <trends color="green" title="Income" :amount="4000" :lastAmount="3000" :loading="false"/>
    <trends color="red" title="Income" :amount="4000" :lastAmount="3000" :loading="false"/>
    <trends color="green" title="Income" :amount="4000" :lastAmount="3000" :loading="false"/>
    <trends color="red" title="Income" :amount="4000" :lastAmount="3000" :loading="true"/>
  </section>
</template>

<script setup>
import { transactionsView } from '~/constants';
const viewSelected = ref(transactionsView[1])

</script>