<template>
  <v-footer class="bg-grey-lighten-4 text-body-1 py-4 px-6" elevation="2" app>
    <v-container>
      <v-row align="center" justify="space-between" class="flex-wrap">
        <!-- 👉 Left side -->
        <v-col cols="12" md="6" class="d-flex align-center justify-center justify-md-start text-center text-md-start">
          <span>
            &copy; {{ new Date().getFullYear() }}
            <strong>Quotation Hub</strong>
            &nbsp;&nbsp;
            <a href="tel:+917862813861" class="text-primary text-decoration-none font-weight-medium">
              Developed By The Web Time IT Solution
            </a>
          </span>
        </v-col>

        <!-- 👉 Right side (optional links) -->
        <!--
        <v-col
          cols="12"
          md="6"
          class="d-flex justify-center justify-md-end text-center text-md-end"
        >
          <v-btn
            variant="text"
            class="text-primary"
            href="https://themeforest.net/licenses/standard"
            target="_blank"
          >
            License
          </v-btn>
          <v-btn
            variant="text"
            class="text-primary"
            href="https://1.envato.market/pixinvent_portfolio"
            target="_blank"
          >
            More Themes
          </v-btn>
          <v-btn
            variant="text"
            class="text-primary"
            href="https://demos.pixinvent.com/vuexy-vuejs-admin-template/documentation/"
            target="_blank"
          >
            Documentation
          </v-btn>
        </v-col>
        -->
      </v-row>
    </v-container>
  </v-footer>
</template>
