<template>
  <Ucard v-if="!success">
    <template #header>
      Sign in to finance tracker
    </template>

    <form>
      <UFormGroup label="Email"
                  name="email"
                  class="mb-4"
                  :required="true"
                  help="You will recive am email to confirmation link">
        <UInput type="email"
                placeholder="Email *"
                required />
      </UFormGroup>
      <UButton type="submit"
               variant="solid"
               color="black"
               @click="success = !success">Sign in</UButton>
    </form>
  </Ucard>
  <UCard v-else>
    <template #header>
      Email has been sand
    </template>
    <div class="text-center">
        <p class="mb-4">We have sent email to <strong>firulin@gmail.com</strong> with a link </p>
        <p><strong>The link will expire in five minutes</strong></p>
    </div>
  </UCard>
</template>

<script setup>
const success = ref(false)
</script>