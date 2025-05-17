<template>
  <VContainer class="d-flex justify-center align-center py-10 px-4" style="min-height: 100vh;">
    <VCard class="pa-6" elevation="8" style="width: 100%; max-width: 900px;" rounded="xl">
      <!-- Title / Instruction -->
      <VCardTitle class="text-h5 text-primary font-weight-bold mb-4 text-center">
        🎉 Registration Successful!
      </VCardTitle>

      <VCardText class="text-body-1 text-center mb-6">
        Please connect with our admin for payment confirmation on:
        <span class="font-weight-bold text-primary">+91 9662961868</span><br />
        Once your payment is confirmed, your account will be activated and you can log in. <br />
        <strong>Thank you!</strong>
      </VCardText>

      <!-- Alert Box -->
      <VAlert v-if="isAlertVisible" v-model="isAlertVisible" closable close-label="Close Alert" color="error"
        class="mb-6" border="start" variant="tonal">
        <ul v-for="(value, key) in errors" :key="key">
          <li v-for="(value1, key1) in value" :key="key1">→ {{ value1 }}</li>
        </ul>
      </VAlert>

      <!-- Login Button -->
      <VCardActions class="justify-center">
        <VBtn to="/" color="primary" size="large" rounded>
          Go to Login
        </VBtn>
      </VCardActions>
    </VCard>

    <!-- Loader -->
    <VDialog v-model="loader" persistent width="300">
      <VCard class="pa-3" color="primary">
        <VCardText class="text-center white--text">
          <div class="mb-2">Saving, please wait...</div>
          <VProgressLinear indeterminate color="white" />
        </VCardText>
      </VCard>
    </VDialog>
  </VContainer>
</template>

<script>
export default {
  data() {
    return {
      loader: false,
      errors: {},
      isAlertVisible: false,
    };
  },
};
</script>

<route lang="yaml">
meta:
  layout: blank
</route>

<style scoped>
@media (max-width: 600px) {
  .text-h5 {
    font-size: 1.25rem !important;
  }

  .text-body-1 {
    font-size: 0.95rem !important;
  }
}
</style>
