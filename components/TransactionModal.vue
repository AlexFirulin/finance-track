<template>
  <UModal v-model="isOpen">
    <UCard>
      <template #header>
        Add Transaction
      </template>
      <UForm :state="state"
             :schema="schema"
             ref="form"
             @submit="save">
        <UFormGroup label="Transaction type"
                    name="type"
                    placeholder="Select the transaction type"
                    :required="true"
                    class="mb-4">
          <USelect placeholder="category"
                   :options="transactionsType"
                   v-model="state.type" />
        </UFormGroup>
        <UFormGroup label="Amount"
                    :required="true"
                    name="amount"
                    class="mb-4">
          <UInput type="number"
                  placeholder="Amount"
                  v-model.number="state.amount" />
        </UFormGroup>
        <UFormGroup label="Transaction date"
                    :required="true"
                    name="created_at"
                    class="mb-4">
          <UInput type="date"
                  icon="i-heroicons-calendar-days-20-solid"
                  v-model="state.created_at" />
        </UFormGroup>
        <UFormGroup label="Description"
                    hint="opional"
                    name="description"
                    class="mb-4">
          <UInput type="text"
                  placeholder="Description"
                  v-model="state.description" />
        </UFormGroup>
        <UFormGroup label="Category"
                    name="category"
                    :required="true"
                    v-if="state.type === 'Expense'"
                    class="mb-4">
          <USelect placeholder="category"
                   :options="category"
                   v-model="state.category"
                   />
        </UFormGroup>
        <UButton type="submit" color="black" variant="solid" label="Save" :loading="isLoading" />
      </UForm>

    </UCard>
  </UModal>
</template>

<script setup>
import { category, transactionsType } from '~/constants.js'
import { z } from 'zod'

const props = defineProps({ modelValue: Boolean })
const emit = defineEmits(['update:modelValue', 'saved'])
const isLoading = ref(false)
const toast = useToast()

const defaultSchema = z.object({
  created_at: z.string(),
  description: z.string().optional(),
  amount: z.number().positive('Amount needs to be more than 0')
})
const incomeSchema = z.object({
  type: z.literal('Income')
})
const expenseSchema = z.object({
  type: z.literal('Expense'),
  category: z.enum(category)
})
const investmentSchema = z.object({
  type: z.literal('Investment')
})
const savingSchema = z.object({
  type: z.literal('Saving')
})
const schema = z.intersection(
  z.discriminatedUnion('type', [incomeSchema, expenseSchema, investmentSchema, savingSchema]),
  defaultSchema
)
const form = ref()
const supabase = useSupabaseClient()

const save = async () => {
  if (form.value.errors.length) return
  isLoading.value = true
  try {
    const { error } = await supabase.from('transactions')
      .upsert({ ...state.value })
    if (!error) {
      toast.add({
        'title': 'Transaction saved',
        'icon': 'i-heroicons-check-circle'
      })
      isOpen.value = false
      emit('saved')
      return
    }
    throw error
  } catch (e) {
    toast.add({
      title: 'Transaction not saved',
      description: e.message,
      icon: 'i-heroicons-exclamation-circle',
      color: 'red'
    })
  } finally {
    isLoading.value = false
  }
}

const initialState = {
  type: "Income",
  amount: 0,
  created_at: undefined,
  description: undefined,
  category: undefined
}
const state = ref({
  ...initialState
})
const resetForm = () =>{
Object.assign(state.value, initialState)
form.value.clear()
}

const isOpen = computed({
  get: () => props.modelValue,
  set: (value) => {
    if(!value) resetForm()
    emit('update:modelValue', value)
  }
  })
</script>