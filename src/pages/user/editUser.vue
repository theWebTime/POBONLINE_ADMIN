<template>
  <div>
    <GlobalBreadCrumbsVue></GlobalBreadCrumbsVue>

    <VCard title="Update User Details">
      <VAlert v-model="isAlertVisible" closable close-label="Close Alert" color="error">
        <ul v-for="(value, key) in errors" :key="key">
          <li v-for="(value1, key1) in value" :key="key1">-> {{ value1 }}</li>
        </ul>
        {{ value }}
      </VAlert>
      <VForm ref="formSubmit">
        <VCardText>
          <VRow>
            <VCol cols="12" md="6">
              <label>Logo Image</label>
              <v-file-input accept="image/*" v-model="image" ref="file"></v-file-input>
            </VCol>
            <VCol cols="12" md="6">
              <VAvatar size="48">
                <VImg :src="`${baseUrl}/images/user/${fetch_photo}`" class="rounded-square" cover />
              </VAvatar>
            </VCol>
            <VCol cols="12" md="6">
              <AppTextField :rules="[globalRequire, nameMin, nameMax].flat()" v-model="insertData.name" label="Name" />
            </VCol>
            <VCol cols="12" md="4">
              <AppTextField v-model="insertData.phone_number" :rules="[globalRequire, numberMin, numberMax].flat()"
                type="number" label="Phone Number" />
            </VCol>
            <VCol cols="12" md="6">
              <AppTextField :rules="[globalRequire, nameMin, nameMax].flat()" v-model="insertData.studio_name"
                label="Studio Name" />
            </VCol>
            <VCol cols="12" md="6">
              <v-textarea v-model="insertData.address" :rules="[globalRequire].flat()" label="Street Address" />
            </VCol>
            <VCol cols="12" md="4">
              <AppTextField v-model="insertData.email" :rules="[email, globalRequire].flat()" label="Email" />
            </VCol>
            <VCol cols="12" md="6">
              <AppTextField v-model="insertData.instagram_link" label="Instagram Link" />
            </VCol>
            <VCol cols="12" md="4">
              <AppTextField v-model="insertData.subscription_date" :rules="[globalRequire].flat()" type="date"
                label="date" />
            </VCol>
            <VCol cols="12" md="4">
              <VRadioGroup v-model="insertData.status" inline label="Status">
                <VRadio label="Active" :value="1" density="compact" />
                <VRadio label="In-Active" :value="0" density="compact" />
              </VRadioGroup>
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
import GlobalBreadCrumbsVue from "@/components/common/GlobalBreadCrumbs.vue";
import http from "../../http-common";
export default {
  components: {
    GlobalBreadCrumbsVue,
  },
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
          if (!value) return "Required.";
          if (value.length < 3) return "Must be at least 3 characters.";
          return true;
        },
      ],
      nameMax: [
        (value) => {
          if (!value) return "Required.";
          if (value.length > 50) return "Must not exceed 50 characters.";
          return true;
        },
      ],
      email: [
        (v) =>
          !v ||
          /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) ||
          "Email must be valid",
      ],
      image: "",
      fetch_photo: "",
      insertData: {
        name: "",
        phone_number: "",
        studio_name: "",
        address: "",
        email: "",
        password: "",
        instagram_link: "",
        subscription_date: "",
        status: "",
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
    async fetchData() {
      this.loader = true;
      await http
        .get("/user/show/" + this.paramsId)
        .then((res) => {
          if (res.data.success) {
            const resData = res.data.data;
            this.insertData.name = resData.name;
            this.insertData.phone_number = resData.phone_number;
            this.insertData.studio_name = resData.studio_name;
            this.insertData.address = resData.address;
            this.insertData.email = resData.email;
            this.insertData.instagram_link = resData.instagram_link;
            this.insertData.status = resData.status;
            this.insertData.subscription_date = resData.subscription_date;
            this.fetch_photo = resData.image == null ? "" : resData.image;
          }
        })
        .catch((e) => {
          console.log(e);
        });
      this.loader = false;
    },
    async updateData() {
      const checkValidation = await this.$refs.formSubmit.validate();
      if (checkValidation.valid) {
        const formData = new FormData();
        if (this.image) {
          const imageData = this.$refs.file.files[0];
          formData.append("image", imageData);
        } else {
          formData.append("image", "");
        }
        for (let x in this.insertData) {
          formData.append(x, this.insertData[x]);
        }
        this.loader = true;
        http
          .post("user/update/" + this.paramsId, formData)
          .then((res) => {
            if (res.data.success) {
              this.$toast.success(res.data.message);
              this.$router.push({
                path: "/user/list/",
              });
              this.isAlertVisible = false;
            } else {
              this.$toast.error(res.data.message);
              this.errors = res.data.data;
              this.isAlertVisible = true;
            }
            this.loader = false;
          })
          .catch((e) => {
            this.loader = false;
          });
      }
    },
  },
};
</script>
