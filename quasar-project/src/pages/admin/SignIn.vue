<template>
  <div class="dark-bg page q-px-lg">
  <div>
    <div>
      <p
        v-show="this.denied"
        class="text-center text-red-4 font-regular q-mb-md"
      >
        {{ issue }}
      </p>
    </div>

    <q-card class="my-card semidark-bg br-primary pa-interior">
      <q-card-section class="q-pa-none">
        <div v-if="show">
          <p
            class="font-regular text-white text-center"
            style="margin-top: 18px"
          >
            Name
          </p>
          <div class="q-mt-md q-px-sm q-mb-lg">
            <div class="q-px-md grey-bg input br-secondary q-pb-xl center">
              <q-input borderless v-model="name" autofocus ref="inputRef" 
              :rules="[
                val => val.length >= 4,
              ]"
              />
            </div>
          </div>
          <p class="font-regular text-white text-center">Email</p>
          <div class="q-mt-md q-px-sm">
            <div class=" q-px-sm grey-bg input br-secondary q-pb-xl ">
              <q-input
                borderless
                suffix="@srec.ac.in"
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
              <q-input borderless v-model="otp" autofocus 
              maxlength="6"
              :rules="[
                val => val.length >= 6 ,
              ]" />
            </div>
          </div>
        </div>
        <div style="margin-top: 46px">
          <q-btn
            padding="8px 0px"
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
  </div>
</template>

<script>

import firebase from "firebase";
import { defineComponent, ref } from "vue";
import axios from "axios";
import { useVuelidate } from "@vuelidate/core";
import { required, minLength, maxLength } from '@vuelidate/validators';
import { db, auth, functions } from "../../../firestore/firestore";
export default {
  name: "SignIn",
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
      name: "",
      email: "",
      otp: "",
      buttonText: "Send OTP",
      issue: "",
      denied: false,
      show: true,
      formatEmail: "",
      doc: "",
      password: "adminuser123"
    };
  },
    validations() {
    return {
      name: {
        required,
        minLength: minLength(4)
      },
    };
  },

  methods: {
    submitForm() {
      if (this.buttonText == "Send OTP") {
        this.v$.$validate();
        if (this.v$.$error) {
          console.log(this.v$);
          this.denied = true;
          this.issue = "All fields are required"
        } else {
          this.denied = false;
          this.formatEmail = this.email + "@srec.ac.in";
          this.emailVerify()
        }
      } else {
          this.verifyOTP()
      }
      
    },
     async emailVerify() {
       const db = firebase.firestore();

      var admin = db.collection("staffs").where("email", "==",this.formatEmail)
        console.log(admin.email);
      await admin
        .get()
        .then((querySnapshot) => {
          querySnapshot.forEach((doc) => {
            // doc.data() is never undefined for query doc snapshots
            console.log(doc.id, " => ", doc.data());
            this.doc = doc.data()
            this.denied = false;
            this.sendOTP();

          });
        })
        .catch(() => {
          this.show = true
          this.issue = "Network error";
          this.denied = true;
          
          console.log("Error getting documents: ", error);
        });

       if (this.doc == "") {
         this.denied = true
          this.issue = "You are not Authorized!";
        }

    },
      async sendOTP() {
      console.log("sendotp", this.formatEmail);

      await axios
        .post(
          "http://localhost:3000/sendotp",
          {
            name: this.name,
            email: this.formatEmail.toLowerCase(),
          },
          { headers: { "Content-Type": "application/json" } }
        )

        .then((response) => {
          console.log(response.data);
          this.show = false
          this.buttonText = "SIGN IN";
          // this.output = response.data;
        })

        .catch((error) => {
          console.log(error.response.data);
          if (Object.keys(error.response.data).length <= 2) {
            this.denied = true;
            // this.issue = error.response.data.error;
          }

          // currentObj.output = error;
        });
    },
    async verifyOTP() {

    console.log(this.formatEmail);
    await axios
      .post(
        "http://localhost:3000/verify",
        {
          email: this.formatEmail.toLowerCase(),
          otp: this.otp,
        },
        { headers: { "Content-Type": "application/json" } }
      )

      .then((response) => {
        console.log(response.data);
          if (response.data.found) {
              this.CreateAccount()
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
    async CreateAccount() {
     await  firebase.auth().createUserWithEmailAndPassword(this.formatEmail, this.password)
    .then((userCredential) => {
      // Signed in 
      console.log(userCredential);
      var user = userCredential.user;
      user.updateProfile({
        displayName: this.doc.name
      }).then(() => {
        console.log(user);
      })
        .catch((err) => {
        
      })
      
      // this.addAdmin()
      
      // ...
    })
       .catch((error) => {
         console.log(error);

         console.log(error.code);
         if (error.code == "auth/email-already-in-use") {
            this.SignIn()
      }
      var errorCode = error.code;
      var errorMessage = error.message;
      // ..
    });
    },

    addAdmin() {
      const addAdmin = functions.httpsCallable('addAdminRole')
      addAdmin({ email: this.formatEmail })
        .then(result => {
          console.log(result);
        this.SignIn()
        })
        .catch(err => {
        console.log(err);
      })
    },
    async SignIn() {
    await firebase.auth().signInWithEmailAndPassword(this.formatEmail, this.password)
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

};
</script>

<style scoped>
.my-card {
  width: 100%;
  min-height: 200px;
  height: 100%;
  max-height: 420px;
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

.q-field {
  position: relative;
  font-size: 18px;
  top: -4px;
}

.page {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
