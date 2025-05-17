<template>
  <VRow no-gutters class="auth-wrapper bg-surface fill-height d-flex align-center justify-center pa-4">
    <VCol cols="12" sm="8" md="6" lg="4">
      <VCard flat class="elevation-4 rounded-xl pa-6">
        <VCardText class="text-center">
          <!-- Logo -->
          <div class="d-flex justify-center mb-4">
            <!-- <VNodeRenderer :nodes="themeConfig.app.logo" /> -->
            <!-- OR use <img> if you have a static image like: -->
            <img src="@/assets/images/POB_Circle_Logo.png" alt="Logo" height="200" width="200" />
          </div>
          <!-- Title and subtitle -->
          <h4 class="text-h4 font-weight-bold mb-1">
            Welcome to {{ themeConfig.app.title }}
          </h4>
          <p class="text-body-2 mb-4">Please sign in to your account</p>
        </VCardText>

        <!-- Error Alert -->
        <VAlert v-if="isAlertVisible" v-model="isAlertVisible" closable close-label="Close Alert" color="error"
          class="mb-4">
          <ul v-for="(value, key) in errors" :key="key">
            <li v-for="(value1, key1) in value" :key="key1">• {{ value1 }}</li>
          </ul>
        </VAlert>

        <VCardText>
          <VForm ref="formSubmit">
            <VRow>
              <!-- Email with icon -->
              <VCol cols="12">
                <AppTextField v-model="loginObject.email" label="Email" type="email" prepend-inner-icon="tabler-mail"
                  :rules="[globalRequire, email].flat()" autofocus density="comfortable" />
              </VCol>

              <!-- Password with visibility toggle icon -->
              <VCol cols="12">
                <AppTextField v-model="loginObject.password" label="Password"
                  :type="isPasswordVisible ? 'text' : 'password'" prepend-inner-icon="tabler-lock"
                  :append-inner-icon="isPasswordVisible ? 'tabler-eye-off' : 'tabler-eye'"
                  :rules="[globalRequire].flat()" density="comfortable"
                  @click:append-inner="isPasswordVisible = !isPasswordVisible" />
              </VCol>

              <!-- Login Button -->
              <VCol cols="12" class="mt-2">
                <VBtn block color="primary" @click="login" :disabled="isSubmit" size="large">
                  <VProgressCircular v-if="isSubmit" :size="22" width="2" indeterminate color="white" />
                  <span v-else>Login</span>
                </VBtn>
              </VCol>

              <!-- Book a Demo Button -->
              <VCol cols="12">
                <RouterLink to="/bookDemo" class="text-decoration-none">
                  <VBtn block variant="outlined" size="large">Book a Demo</VBtn>
                </RouterLink>
              </VCol>
            </VRow>
          </VForm>
        </VCardText>
      </VCard>
    </VCol>
  </VRow>
</template>


<script setup>
import { useGenerateImageVariant } from "@core/composable/useGenerateImageVariant";
import authV2LoginIllustrationBorderedDark from "@images/pages/auth-v2-login-illustration-bordered-dark.png";
import authV2LoginIllustrationBorderedLight from "@images/pages/auth-v2-login-illustration-bordered-light.png";
import authV2LoginIllustrationDark from "@images/pages/auth-v2-login-illustration-dark.png";
import authV2LoginIllustrationLight from "@images/pages/auth-v2-login-illustration-light.png";
import authV2MaskDark from "@images/pages/misc-mask-dark.png";
import authV2MaskLight from "@images/pages/misc-mask-light.png";
import { themeConfig } from "@themeConfig";

const authThemeImg = useGenerateImageVariant(
  authV2LoginIllustrationLight,
  authV2LoginIllustrationDark,
  authV2LoginIllustrationBorderedLight,
  authV2LoginIllustrationBorderedDark,
  true
);
const authThemeMask = useGenerateImageVariant(authV2MaskLight, authV2MaskDark);
const isPasswordVisible = ref(false);
</script>

<script>
import ls from "localstorage-slim";
import { useToast } from "vue-toastification";
import http from "../http-common";

const toast = useToast();

export default {
  name: "login",
  data() {
    return {
      globalRequire: [
        (value) => (value ? true : "Required."),
      ],
      email: [
        (v) =>
          !v ||
          /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) ||
          "Email must be valid",
      ],
      loginObject: {
        email: "",
        password: "",
      },
      isSubmit: false,
      errors: {},
      isAlertVisible: false,
    };
  },
  methods: {
    async login() {
      const checkValidation = await this.$refs.formSubmit.validate();
      if (checkValidation.valid) {
        this.isSubmit = true;
        http
          .post("/login", this.loginObject)
          .then((res) => {
            if (res.data.success) {
              ls.set("user-info", res.data.data, { encrypt: true });
              location.reload();
            } else {
              toast.error(res.data.message);
              if (res.data.data) {
                this.errors = res.data.data;
                this.isAlertVisible = true;
              }
            }
            this.isSubmit = false;
          })
          .catch((e) => {
            toast.error("Login failed");
            console.error(e);
            this.isSubmit = false;
          });
      }
    },
  },
};
</script>

<style lang="scss">
@use "@core/scss/template/pages/page-auth.scss";

.auth-wrapper {
  min-height: 100vh;
}
</style>

<route lang="yaml">
meta:
  layout: blank
</route>
