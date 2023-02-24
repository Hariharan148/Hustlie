<template>
  <div class="dark-bg page q-px-lg">



    <q-card class="my-card semidark-bg br-primary pa-interior">
            <p
        v-show="this.denied"
        class="text-center text-red-4 font-regular q-mb-md"
      >
        {{ issue }}
      </p>
      <q-card-section class="q-pa-none">
        <div v-if="!verified">
          <p
            class="font-regular text-white text-center"
            style="margin-top: 18px"
          >
            Name
          </p>
          <div class="q-mt-md q-px-md q-mb-lg">
            <div class="q-px-md grey-bg input br-secondary q-pb-xl center">
              <q-input borderless v-model="name" autofocus />
            </div>
          </div>
          <p class="font-regular text-white text-center">Email</p>
          <div class="q-mt-md q-px-md">
            <div class="q-px-md grey-bg input br-secondary q-pb-xl">
              <q-input
                borderless
                suffix="@kpriet.ac.in"
                v-model="email"
                autofocus
               
              >
              </q-input>
            </div>
          </div>
        </div>
        <div v-else>
          <p class="font-regular text-white text-center" style="margin-top: ">
            Enter OTP
          </p>
          <div class="q-mt-md q-px-md">
            <div class="q-px-md grey-bg input br-secondary q-pb-xl center">
              <q-input borderless v-model="otp" autofocus  />
            </div>
          </div>
        </div>
        <div style="margin-top: 46px">
          <q-btn
            size="25px"
            v-model="otp"
            class="bg-prime fw-bold button"
            text-color="black"
            :label="buttonText"
            @click="submitForm()"
          />
        </div>
      </q-card-section>
    </q-card>
  </div>
</template>

<script>
import { defineComponent, ref } from "vue";
// import SignIn from "../../components/SignIn.vue";
import firebase from "firebase";
import { db, auth } from "firebase/firestore";
import axios from "axios";
import { collection, query, where } from "firebase/firestore";

export default defineComponent({
  name: "SignIn",
  // components: { SignIn },
  data() {
    return {
      verified: false,
      name: "",
      otp: "",
      denied: false,
      issue:"",
      email: "",
      formatEmail:"",
      buttonText: "Send OTP",
      password:"12345678",

    };
  },

  methods: {

    submitForm() {
      if (this.buttonText == "Send OTP") {
        this.sendOTP()
      } else if (this.buttonText == "Verify") {
        this.verifyOTP()
      }
    },
    async sendOTP() {
          this.email = this.email + "@kpriet.ac.in"
      console.log(this.email);

      await axios
        .post(
          "http://localhost:3000/sendotp",
          {
            name: this.name,
            email: this.email.toLowerCase(),
          },
          { headers: { "Content-Type": "application/json" } }
        )
        .then((response) => {

          console.log(response.data);
          this.verified = true
          this.buttonText = "Verify"

          console.log(this.sendemail);

          // this.output = response.data;
        })
        .catch((error) => {
          if (error.code == "ERR_NETWORK") {
            console.log("hi");
            this.denied = true;
            this.issue = "ERR_NETWORK";
          }
          // console.log(error.code);
          if (Object.keys(error.response.data).length <= 2) {
            this.denied = true;
            this.issue = error.response.data.error;
          }
          // currentObj.output = error;
        });
    },
       async verifyOTP() {
      console.log(this.email.toLowerCase());
     await axios
        .post(
          "http://localhost:3000/verify",
          {
            email: this.email.toLowerCase(),
            otp: this.otp,
          },
          { headers: { "Content-Type": "application/json" } }
        )

        .then((response) => {
          console.log(response.data);
          if (response.data.found) {
              this.SignIn()
          } else {
            this.denied = true;
            this.issue = "Invalid OTP"
          }
        })
        .catch((error) => {
          console.log(error.response.data);
          if (Object.keys(error.response.data).length <= 2) {
            this.denied = true;
            this.issue = error.response.data.error;
          }

        });
    },
    async SignIn() {
    await firebase.auth().signInWithEmailAndPassword(this.email, this.password)
      .then((userCredential) => {
        // Signed in
        console.log("inside");
        var user = userCredential.user;
        // ...
      })
      .catch((error) => {
        console.log("outside");
        console.log(error);
        this.denied = true
        this.issue = "Create an user account!"
        var errorCode = error.code;
        var errorMessage = error.message;
      });
},
  },
});
</script>

<style scoped>
.page {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* .my-card {
  width: 100%;
  min-height: 200px;
  height: 100%;
  max-height: 420px;
} */

.vertical-align {
  display: flex;
  align-items: center;
  justify-content: center;
}

.text-center {
  text-align: center;
}

.button {
  width: 100%;
  height: 50px;
  display: flex;
  align-items: center;
}

.input {
  width: 100%;
  height: 42px;
}

.q-field {
  position: relative;
  font-size: 18px;
  top: -4px;
}
</style>
