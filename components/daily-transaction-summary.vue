<template>
  <div class="grid grid-cols-2 py-4 border-b border-gray-200 dark:border-gray-800 text-gray-500 dark:text-gray-400 font-bold">
    <div class="flex items-center justify-between">
      {{ date }}
    </div>
    <div class="flex items-center justify-end mr-8">
      {{ currency }}
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  date:String,
  transactions: Array
})
const sum = computed( ()=>{
  let totalSum = 0
  for(const transaction of props.transactions) {
    if(transaction.type === "Income" ) {
      totalSum+= transaction.amount
    } else {
      totalSum -= transaction.amount
    }
  }
  return totalSum
})

const { currency } =useCurrence(sum)
</script>