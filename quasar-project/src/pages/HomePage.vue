<template>
  <q-layout class="dark-bg">
    <q-page-container>
      <q-page class="ma-y-40 vertical-align flex">
        <img
          src="../assets/Hustlie.png"
          alt="logo"
          class="fly"
          style="top: 5%"
        />
        <p
          class="fly text-white font-small"
          style="top: 19%; text-align: center"
        >
          வணக்கம்!, Hello, Bonjour, Hola, Guten Tag, Здравствуйте!
        </p>
        <q-card class="my-card semidark-bg br-primary pa-interior">
          <q-card-section class="q-pa-none">
            <p class="font-regular text-white text-center">Login as</p>
            <div style="margin-top: 15px">
              <router-link to="/staff/signin" style="text-decoration: none"
                ><q-btn
                  padding="8px 0px"
                  size="25px"
                  class="bg-prime fw-bold button"
                  text-color="black"
                  label="PROFESSOR"
              /></router-link>
            </div>
            <p
              class="font-regular text-white text-center"
              style="margin-top: 30px"
            >
              Login as
            </p>
            <div style="margin-top: 15px">
              <router-link to="/user/signup" style="text-decoration: none">
                <q-btn
                  padding="8px 0px"
                  size="25px"
                  class="grey-bg fw-bold button"
                  text-color="black"
                  label="STUDENT"
                />
              </router-link>
            </div>
          </q-card-section>
        </q-card>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "LoginPage",
  data() {},
});
</script>

<style scoped>
.my-card {
  width: 100%;
  height: 290px;
  /* max-width: 250px; */
}

.vertical-align {
  display: flex;
  align-items: center;
  justify-content: center;
}

.button {
  width: 100%;
  display: flex;
  align-items: center;
}

.fly {
  position: absolute;
}
</style>
