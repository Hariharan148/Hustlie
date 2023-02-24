<template>
  <div v-if="!sendemail">
    <q-layout class="dark-bg">
      <q-page-container>
        <q-page class="ma-y-40 vertical-align">
          <q-card class="my-card semidark-bg br-primary pa-interior">
            <q-card-section class="q-pa-none">
              <div>
                <p
                  v-show="this.denied"
                  class="text-center text-red-4 font-regular q-mb-md"
                >
                  {{ issue }}
                </p>
              </div>
              <p
                class="fw-semibold text-center text-white"
                style="font-size: 30px; top: -10px; position: relative"
              >
                SIGN UP
              </p>
              <p class="font-regular grey-fg text-center q-mt-md">Name</p>
              
              <div class="q-mt-md">
                <div class="q-px-md grey-bg input br-secondary q-pb-xl">
                  <q-input
                    ref="inputRef"
                    :rules="[
                        val => val.length > 4 ,
                      ]"
  
                    borderless
                    autofocus
                    v-model="user_name"
                    placeholder="Ex: Pawan S" 
                    
                  >
                  </q-input>
                </div>
              </div>
              <div style="width: 100%; height: 100%" class="flex">
                <div>
                  <p
                    class="font-regular grey-fg text-center"
                    style="margin-top: 18px"
                  >
                    Roll No
                  </p>
                  <div class="q-mt-md q-pr-md">
                    <div
                      class="q-px-md grey-bg input br-secondary q-pb-xl center"
                    >
                      <q-input
                        ref="inputRef"
                        borderless
                        v-model="roll_no"
                        :dense="this.dense"
                        
                        mask="xxxxxxx"
                        :rules="[
                            val => val.length > 6 ,
                          ]"
                      />
                    </div>
                  </div>
                </div>
                <div>
                  <p
                    class="font-regular grey-fg text-center"
                    style="margin-top: 18px"
                  >
                    Section
                  </p>
                  <div class="q-mt-md q-pr-md">
                    <div
                      class="q-px-md grey-bg input br-secondary q-pb-xl center"
                    >
                      <q-input
                        ref="inputRef"
                        borderless
                        v-model="section"
                        :dense="this.dense"
                        autofocus
                        mask="A"
                      />
                    </div>
                  </div>
                </div>
              </div>
              <div class="flex">
                <div class="input-2">
                  <p
                    class="font-regular grey-fg text-center"
                    style="margin-top: 18px"
                  >
                    Year
                  </p>
                  <div class="q-mt-md q-pr-md">
                    <div
                      class="q-px-md grey-bg input br-secondary q-pb-xl center"
                    >
                      <q-input
                        ref="inputRef"
                        borderless
                        v-model="year"
                        :dense="this.dense"
                        autofocus
                        mask="#"
                      />
                    </div>
                  </div>
                </div>

                <div class="input-2 q-mb-xl">
                  <p
                    class="font-regular grey-fg text-center"
                    style="margin-top: 18px"
                  >
                    Dept
                  </p>
                  <div class="q-mt-md">
                    <q-btn-dropdown
                      padding="6px 0px"
                      size="16px"
                      class="grey-bg button font-semi-medium shadow-6 dept-btn"
                      no-caps
                      :label="this.dept_name_item"
                      icon=""
                      dropdown-icon="none"
                      ref="inputRef"
        
                    >
                      <q-list separator class="br-primary">
                        <q-item
                          clickable
                          v-close-popup
                          @click="onItemClick"
                          class="button"
                          v-for="item in dept_list"
                          :key="item"
                        >
                          <q-item-section>
                            <q-item-label>{{ item }}</q-item-label>
                          </q-item-section>
                        </q-item>
                      </q-list>
                    </q-btn-dropdown>
                  </div>
                </div>
                <div style="width: 100%" class="q-mt-md">
                  <p
                    class="font-regular grey-fg text-center"
                    style="margin-top: 18px"
                  >
                    Email
                  </p>
                </div>
                <div class="q-mt-md q-pr-md">
                  <div
                    class="q-px-md grey-bg input br-secondary q-pb-xl center"
                  >
                    <q-input
                      ref="inputRef"
                      borderless
                      v-model="roll_no"
                      :dense="this.dense"
                      autofocus
                      suffix="@kpriet.ac.in"
                      
                    />
                  </div>
                </div>
              </div>

              <div style="margin-top: 100px" class="q-px-lg">
                <p
                  class="text-red-4 q-mt-md q-mb-md text-center"
                  style=""
                  v-show="alert"
                >
                  *All Fields Are Required
                </p>
                <q-btn
                  icon="fa-brands fa-google"
                  padding="12px 0px"
                  size="18px"
                  class="bg-prime fw-bold button shadow-6"
                  text-color="black"
                  label="Sign up"
                  @click="submitForm()"
                />
                <p class="grey-fg text-center font-small q-mt-lg">
                  Already have an account?
                </p>
                <router-link to="/user/signin" style="text-decoration: none">
                <q-btn
                  icon="fa-brands fa-google"
                  padding="12px 0px"
                  size="18px"
                  class="grey-bg fw-bold button shadow-6 q-mt-md"
                  text-color="black"
                  label="Sign in"
                
                />
                </router-link>
              </div>
            </q-card-section>
          </q-card>
        </q-page>
      </q-page-container>
    </q-layout>
  </div>
  <div v-else>
    <div class="dark-bg page q-px-xl">
      <VerifyOTP :data="data" />
    </div>
  </div>
</template>

<script>
import { defineComponent, ref } from "vue";
import VerifyOTP from "../../components/VerifyOtp";

import firebase from "firebase";
import { collection, addDoc } from "firebase/firestore";
import { useVuelidate } from "@vuelidate/core";
import { required, minLength } from "@vuelidate/validators";

import axios from "axios";

import router from "src/router";
// import VerifyOtp from "src/components/VerifyOtp.vue";

export default defineComponent({
  name: "SignUp",
  components: { VerifyOTP },
  setup() {
    const inputRef = ref(null)

    return {
      model: ref(''),
      inputRef,

      reset() {
        inputRef.value.resetValidation()
      }
    }
  },
  data() {
    return {
      v$: useVuelidate(),
      dept_name_item: "",
      dept_list: ["EEE", "ECE", "CSE", "CE", "CH", "AD", "BME", "MECH"],
      user_name: "",
      roll_no: "",
      year: "",
      denied: false,
      dept_name: "",
      alert: false,
      dense: true,
      issue: "",
      section: "",
      email: "",
      data: {
        name: "", roll_no: "", section: "", 
        year: "", dept: "", email: "",
      },
      spam: "",
      dataLength: null,
      sendemail: false,
    };
  },
  validations() {
    return {
      user_name: {
        required,
        minLength: minLength(5)
      },
      dept_name_item: { required },
      roll_no: { required, minLength: minLength(7) },
      section: { required },
      year: { required },
    };
  },
  methods: {
    onItemClick(e) {
      this.dept_name_item = e.target.innerHTML;
    },
    submitForm() {
      this.v$.$validate();
      if (this.v$.$error) {
        this.alert = true;
      } else {
        this.alert = false;
        this.email = this.roll_no + "@kpriet.ac.in";
        this.data = {
        name: this.user_name, roll_no: this.roll_no, section: this.section, 
        year: this.year, dept: this.dept_name_item, email: this.email,
      }
        this.sendOTP()
      }
    },
    showOtp() { 
      this.sendemail = true
      console.log("otp");
    },
    async sendOTP() {
      
      console.log(this.email);
      await axios
        .post(
          "http://localhost:3000/sendotp",
          {
            name: this.user_name,
            email: this.email.toLowerCase(),
          },
          { headers: { "Content-Type": "application/json" } }
        )
        .then(function (response) {

          console.log(response.data);
          this.sendemail = true;
          console.log(this.sendemail);

          // this.output = response.data;
        }.bind(this))
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

  }
},);
</script>

<style scoped>
.page {
  width: 100%;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
.my-card {
  width: 100%;
  height: 100%;
  /* max-width: 250px; */
}

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
  display: flex;
  align-items: center;
}

.input {
  width: 100%;
  height: 42px;
}

.input-2 {
  width: 50%;
  height: 42px;
}

.q-field {
  position: relative;
  /* top: -4px; */
  font-size: 14px;
}

.text-center {
  text-align: center;
}

.button {
  width: 100%;
  display: flex;
  align-items: center;
}

.q-item {
  padding: 0px;
  min-height: 0px;
}
.q-item__label {
  height: 30px;
  padding: 6px;
}

.dept-btn {
  min-height: 48px !important;
}
</style>
