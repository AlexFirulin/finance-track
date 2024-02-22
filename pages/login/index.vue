<template>
  <Ucard v-if="!success">
    <template #header>
      Sign in to finance tracker
    </template>

    <form @submit.prevent="handleLogin">
      <UFormGroup label="Email"
                  name="email"
                  class="mb-4"
                  :required="true"
                  help="You will recive am email to confirmation link">
        <UInput type="email"
                placeholder="Email *"
                v-model="email"
                required />
      </UFormGroup>
      <UButton type="submit"
               variant="solid"
               color="black"
               :loading="pending"
               :disabled="pending">Sign in</UButton>
    </form>
  </Ucard>
  <UCard v-else>
    <template #header>
      Email has been sand
    </template>
    <div class="text-center">
      <p class="mb-4">We have sent email to <strong>{{ email }}</strong> with a link </p>
      <p><strong>The link will expire in five minutes</strong></p>
    </div>
  </UCard>
</template>

<script setup>
const success = ref(false)
const email = ref('')
const pending = ref(false)
const toast = useToast()
const supabase = useSupabaseClient()
const handleLogin = async () => {
  pending.value = true
    try {
    const { error } = await supabase.auth.signInWithOtp({
      email: email.value,
      options: {
        redirectTo: 'http://localhost:3000'
      }
    })
    if (error) {
      toast.add({
        title: 'Error authenticating',
        icon: 'i-heroicons-exclamation-circle',
        description: error.message,
        color: 'red'
      })
    } else {
      success.value = true
    }
  } finally {
    pending.value = false
  }
}
</script>