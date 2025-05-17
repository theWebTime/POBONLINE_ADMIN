<template>
  <div>
    <VCard>
      <VAlert v-model="isAlertVisible" closable close-label="Close Alert" color="error">
        <ul v-for="(value, key) in errors" :key="key">
          <li v-for="(value1, key1) in value" :key="key1">-> {{ value1 }}</li>
        </ul>
        {{ value }}
      </VAlert>
      <VForm ref="formSubmit">
        <VCardText>
          <h3>Profile</h3>
          <br />
          <VRow>
            <VCol cols="12" md="6">
              <AppTextField :rules="[globalRequire, nameMin].flat()" v-model="insertData.name" label="Name" />
            </VCol>
            <VCol cols="6">
              <AppTextField type="password" :rules="[passwordMin].flat()" v-model="insertData.password"
                label="Password (make it empty if you don't want to update)" />
            </VCol>
            <VCol cols="12" md="6">
              <AppTextField v-model="insertData.instagram_link" label="Instagram Link" />
            </VCol>
            <VCol cols="12" md="6">
              <AppTextField v-model="insertData.facebook_link" label="Facebook Link" />
            </VCol>
            <VCol cols="12" md="6">
              <AppTextField v-model="insertData.youtube_link" label="Youtube Channel Link" />
            </VCol>
            <VCol cols="12" md="6">
              <AppTextField v-model="insertData.website_link" label="Website Link" />
            </VCol>
            <VCol cols="12" md="6">
              <AppTextField :rules="[globalRequire, email].flat()" v-model="insertData.email" label="Email" disabled />
            </VCol>
            <VCol cols="12" md="6">
              <AppTextField v-model="insertData.studio_name" label="Studio Name" disabled />
            </VCol>
            <VCol cols="12" md="6">
              <label>Logo</label><br /><br />
              <VAvatar size="100">
                <VImg :src="`${baseUrl}/images/user/${fetch_photo}`" class="rounded-square" cover disabled />
              </VAvatar>
            </VCol>
          </VRow>
        </VCardText>

        <VCardText class="d-flex justify-end flex-wrap gap-3">
          <VBtn @click="updateData"> Update </VBtn>
        </VCardText>
      </VForm>
    </VCard>
    <!-- global loader modal -->
    <VDialog v-model="loader" width="300">
      <VCard color="primary" width="300">
        <VCardText class="pt-3">
          loading.........
          <VProgressLinear indeterminate class="mb-0" />
        </VCardText>
      </VCard>
    </VDialog>
  </div>
</template>
<script>
import { useToast } from "vue-toastification";
import http from "../http-common";
const toast = useToast();
export default {
  data() {
    return {
      baseUrl: import.meta.env.VITE_APP_BASE_URL,
      globalRequire: [
        (value) => {
          if (value) return true;
          return "Required.";
        },
      ],
      nameMin: [
        (value) => {
          if (value?.length >= 3) return true;
          return "Must be at least 3 characters.";
        },
      ],
      passwordMin: [
        (value) => {
          if (value?.length > 6) return true;
          return "Must be at least 6 characters.";
        },
      ],
      fetch_photo: "",
      insertData: {
        name: "",
        password: "",
        instagram_link: "",
        facebook_link: "",
        youtube_link: "",
        website_link: "",
        email: "",
        studio_name: "",
      },
      loader: false,
      paramsId: this.$route.params.id,
      errors: {},
      isAlertVisible: false,
    };
  },
  created() {
    this.fetchData();
  },
  methods: {
    resetValues() {
      this.insertData.password = "";
    },
    fetchData() {
      this.loader = true;
      http
        .get("/profile")
        .then((res) => {
          if (res.data.success) {
            const resData = res.data.data;
            this.insertData.name = resData.name;
            this.insertData.email = resData.email;
            this.insertData.studio_name = resData.studio_name;
            this.insertData.instagram_link = resData.instagram_link;
            this.insertData.facebook_link = resData.facebook_link;
            this.insertData.youtube_link = resData.youtube_link;
            this.insertData.website_link = resData.website_link;
            this.insertData.studio_name = resData.studio_name;
            this.fetch_photo = resData.image == null ? "" : resData.image;
          }
          this.loader = false;
        })
        .catch((e) => {
          this.loader = false;
          console.log(e);
        });
    },
    async updateData() {
      this.loader = true;
      http
        .post("profile_update", this.insertData)
        .then((res) => {
          if (res.data.success) {
            this.resetValues();
            this.fetchData();
            toast.success(res.data.message);
            this.isAlertVisible = false;
          } else {
            toast.error(res.data.message);
            this.errors = res.data.data;
            this.isAlertVisible = true;
          }
          this.loader = false;
        })
        .catch((e) => {
          this.loader = false;
        });
    },
  },
};
</script>
