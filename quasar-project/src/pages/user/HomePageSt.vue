<template>
  <div>
    <p class="text-white q-mt-lg">Apply for an OD</p>
    <q-card class="my-card semidark-bg q-mt-md" style="border-radius:10px">

      <q-card-section style="padding: 25px 25px">
        <p class="font-xsm fw-medium text-white text-start">Name</p>
        <div class="q-mt-sm">
          <div class="q-px-md black-r-bg input br-secondary">
            <q-input
              ref="inputRef"
              :rules="[myRule]"
              borderless
              v-model="name"
              readonly
            >
            </q-input>
          </div>
        </div>
        <p class="font-xsm fw-medium text-white text-start q-mt-md">Rollno</p>
        <div class="q-mt-sm">
          <div class="q-px-md black-r-bg input br-secondary">
            <q-input
              ref="inputRef"
              :rules="[myRule]"
              borderless
              v-model="rollno"
              readonly
            >
            </q-input>
          </div>
        </div>
        <p class="font-xsm fw-medium text-white text-start q-mt-md">Class</p>
        <div class="q-mt-sm">
          <div class="q-px-md black-r-bg input br-secondary">
            <q-input
              :rules="[myRule]"
              borderless
              v-model="section"
            >
            </q-input>
          </div>
        </div>
        <div class="q-pa-md">
          <div class="q-gutter-y-md" style="max-width: 350px">
            <q-option-group
              v-model="panel"
              inline
              class="text-white"
              color="orange-5"
              :options="[
                { label: 'Less than 1 Day', value: 'less_than' },
                { label: '> 1 Day', value: 'greater_than' },
              ]"
            />

            <q-tab-panels
              v-model="panel"
              animated
              class="shadow-2 rounded-borders"
              swipeable
              style="background-color: transparent"
            >
              <q-tab-panel
                name="less_than"
                class="semidark-bg"
                style="overflow-y: hidden"
              >
                <p class="font-xsm fw-medium text-white text-start q-mt-md">
                  Period/Hr
                </p>
                <div class="q-mt-sm">
                  <div class="black-r-bg input br-secondary">
                    <q-input
                      ref="inputRef"
                      :rules="[myRule]"
                      borderless
                      v-model="period"
                      class="q-ml-md"
                    >
                    </q-input>
                  </div>
                </div>
              </q-tab-panel>

              <q-tab-panel name="greater_than">
                <div class="q-pa-md">
                  <q-input
                    filled
                    
                    v-model="qdate.to"
                    class="black-r-bg br-secondary"
                  >
                    <template v-slot:prepend>
                      <q-icon name="event" class="cursor-pointer" @click="reset()">
                        <q-popup-proxy
                          cover
                          transition-show="scale"
                          transition-hide="scale"
                        >
                          <div class="q-pa-md" style="text-align: center">

                            <q-date
                              v-model="qdate"
                              range
                              dark
                              color="orange-5"
                            />
                          </div>
                        </q-popup-proxy>
                      </q-icon>
                      <p
                        class="q-ml-md font-small text-white"
                        style="margin-top: 1px"
                      >
                        Till
                      </p>
                    </template>
                  </q-input>
                </div>
              </q-tab-panel>
            </q-tab-panels>
          </div>
        </div>
        <p class="font-xsm fw-medium text-white text-start q-mt-md">Purpose</p>
        <div class="q-mt-sm">
          <div class="q-px-md black-r-bg br-secondary q-mt-md">
            <q-input
              ref="inputRef"
              :rules="[myRule]"
              borderless
              autogrow
              v-model="this.purpose"
            >
            </q-input>
          </div>
        </div>
        <p class="font-xsm fw-medium text-white text-start q-mt-md">Mentor</p>
        <div class="q-mt-md">
          <q-btn-dropdown
            padding="4px 0px"
            size="14px"
            class="black-r-bg button fw-xsm text-white dept-btn"
            no-caps
            :label="this.mentor"
            icon=""
            dropdown-icon="none"
            ref="inputRef"
            :rules="[myRule]"
            style="padding-inline: auto"
          >
            <q-list separator class="br-primary">
              <q-item
                clickable
                v-close-popup
                @click="mentorClick"
                class="button"
                v-for="item in faculty_list"
                :key="item"
              >
                <q-item-section class="fw-xsm">
                  <q-item-label>{{ item.name }}</q-item-label>
                </q-item-section>
              </q-item>
            </q-list>
          </q-btn-dropdown>
        </div>
        <p class="font-xsm fw-medium text-white text-start q-mt-md">
          Chief Mentor
        </p>
        <div class="q-mt-md">
          <q-btn-dropdown
            padding="4px 0px"
            size="14px"
            class="black-r-bg button text-white dept-btn"
            no-caps
            :label="this.chiefMentor"
            icon=""
            dropdown-icon="none"
            ref="inputRef"
            :rules="[myRule]"
            style="padding-inline: auto"
          >
            <q-list separator class="br-primary">
              <q-item
                clickable
                v-close-popup
                @click="chiefMentorClick"
                class="button"
                v-for="item in faculty_list"
                :key="item"
              >
                <q-item-section>
                  <q-item-label>{{ item.name }}</q-item-label>
                </q-item-section>
              </q-item>
            </q-list>
          </q-btn-dropdown>
        </div>
          <div>
          <p
            v-show="this.denied"
            class="text-center text-red-4 font-regular q-mt-md"
          >
            {{ issue }}
          </p>
        </div>
        <div class="flex items-aligned justify-center">
        <q-btn
          padding="5px 0px"
          size="20px"
          @click="submitForm()"
          class="box-shadow bg-prime fw-bold button2"
          style="margin-top: 35px"
          text-color="black"
          label="APPLY"
          no-caps
        />
        </div>
      </q-card-section>
    </q-card>
    </div>

</template>

<script>
import {db, auth } from "../../../firestore/firestore";
import { ref } from "vue";
import firebase from "firebase";
import { format } from "date-fns";
import { useVuelidate } from "@vuelidate/core";
import { required, minLength } from "@vuelidate/validators";

export default {
  data() {
    return {
      v$: useVuelidate(),
      name: "",
      rollno: "",
      data: "",
      section: "",
      chief_mentor: "",
      faculty_list: [],
      mentor: "",
      mentorID: "",
      chiefMentor: "",
      chiefMentorID: "",
      period: "",
      purpose: "",
      denied: false,
      issue: "",
    };
  },
  validations() {
    return {
      name: {
        required,
      },
      rollno: { required },
      section: { required },
      purpose: { required },
      mentor: { required },
      chiefMentor: { required },
    };
  },
  setup() {
    const inputRef = ref(null);
    return {

      model: ref(""),
      year: ref(""),
      dept_name: ref(""),
      inputRef,
      panel: ref("less_than"),
      date: ref(Date.now()),
      qdate: ref({ from: "", to: "" }),
      myRule(val) {
        // simulating a delay

        return new Promise((resolve, reject) => {
          setTimeout(() => {
            resolve(!!val || "");
          }, 500);
        });
      },
    };
  },
  computed: {
    formatDate(v) {
      return (v) => {
        return format(v, new Date());
      };
    },
    _hours() {
      return 60000 * 60
    }

  },

  methods: {
    submitForm() {
      this.v$.$validate();

      if (this.v$.$error) {
        this.denied = true;
        this.issue = "All fields are required!"
      } else {
        console.log(this.period != "" || this.qdate.to != "");
        if (this.period != "" || this.qdate.to != "") {
          if (this.period == "") {
            this.period = `${this.qdate.from} - ${this.qdate.to}`
          }
          this.denied = false;
          this.applyOd()
        } else {
            this.denied = true;
            this.issue = "All fields are required!"
        }
      }
    },
    reset() {
      this.period = ""
     },
      
    mentorClick(e) {
      this.mentor = e.target.innerHTML;
      var result = this.faculty_list.find(item => item.name === this.mentor);
      this.mentorID = result.id
      console.log(this.mentorID);
    },
    chiefMentorClick(e) {
      this.chiefMentor = e.target.innerHTML;
      var result = this.faculty_list.find(item => item.name === this.chiefMentor);
      this.chiefMentorID = result.id
      console.log(this.chiefMentorID);
    },
    async getUserData() {
      var docRef = db.collection("students").doc(firebase.auth().currentUser.uid);

      await docRef.get().then((doc) => {
          this.show = true
          if (doc.exists) {
            console.log("Document data:", doc.data());
            this.data = doc.data()
            this.section = this.data.dept + "-" + this.data.section
            this.name = this.data.name
            this.rollno = this.data.roll_no
            console.log(this.data);
              this.getMentors()
          } else {
              // doc.data() will be undefined in this case
              console.log("No such document!");
          }
      }).catch((error) => {
          console.log("Error getting document:", error);
      });
    },
    Dateformat(date, period) {
      if (!period) {
        let Todate = date.to
        const mydate = Todate.split("/");
        let newDate = `${mydate[0]}-${mydate[1]}-${mydate[2]} 0:0:0.0`
        var millis = Date.parse(newDate);
        this.timer(distance)
        console.log(millis);

      } else {
          let newDate = date
        var millis = Date.parse(newDate);
          
          this.timer(millis)
      }

      console.log(this.expiresIn);

    },
    timer(end) {
      const timer = setInterval(() => { 
        const now = Date.now()
        let distance = end - Date.now()
        console.log(distance);
        if (end < 0) {
          clearInterval(timer)
          this.odExpired()
          return
        }

        this.expiresIn = Math.floor( (distance/ this._hours)/2)
      }, 1000)
    },
    // async setData() {
    //   await db.collection("history").doc().set({
    //     id: this.odData.id,
    //     name: this.odData.name,
    //     roll_no: this.odData.roll_no,
    //     section: this.odData.section,
    //     period: this.odData.period,
    //     purpose: this.odData.purpose,
    //     mentor: this.odData.mentor,
    //     chief_mentor: this.odData.chief_mentor,
    //     status: this.odData.status,
    //   })
    //     .then((docRef) => {
    //     this.deleteOD()
    //     console.log("Document written with ID: ", docRef);
        
          
    //   })
    //   .catch((error) => {
    //       console.error("Error adding document: ", error);
    //   });
    // },
    // async deleteOD() {
    //   await db.collection("od").doc(this.odID).delete().then(() => {
    //   console.log("Document successfully deleted!");
    //   }).catch((error) => {
    //       console.error("Error removing document: ", error);
    //   });
    // },
    // async odExpired() {
    //   console.log("inside od", this.odData );
    //   this.show = true
    //   await db.collection("od").where("id", "==", firebase.auth().currentUser.uid)
    //     .get()
    //     .then((querySnapshot) => {

    //       console.log("emp", querySnapshot.empty);
    //       querySnapshot.forEach((doc) => {
    //         this.odData = doc.data()
    //         this.odID = doc.id
    //         this.setData()
    //       })
    //     })
    //   .catch((error) => {
    //     console.log("Error getting documents: ", error);

    //   });
    //   console.log(this.odData)
      
    //   console.log("hello i ");
    // },
    async getMentors() {
      this.faculty_list = []
      await db.collection("staffs").where("dept", "==", "EEE")
        .onSnapshot((querySnapshot) => {
          querySnapshot.forEach((doc) => {
            var mentor = { id: doc.id, name: doc.data().name }
          
          this.faculty_list.push(mentor);
          
          });
        console.log(this.faculty_list);
    });
    },
    async applyOd() {
      await db.collection("od").doc().set({
        id: firebase.auth().currentUser.uid,
        name: this.name,
        roll_no: this.rollno,
        section: this.section,
        period: this.period,
        purpose: this.purpose,
        mentor: this.mentorID,
        chief_mentor: this.chiefMentorID,
        status: 0,
      })
        .then((docRef) => {
          this.show = false
          console.log("apply");
          if (this.qdate.to != "") {
            this.Dateformat(this.qdate.to, false)
          } else {
              let currentDate = new Date().toJSON().slice(0, 10);
              var date = `${currentDate} 23:59:59.600Z`
              this.Dateformat(date, true )
          }
          
           db.collection("info").doc(firebase.auth().currentUser.uid).set({
              total: +1,
          })
        console.log("Document written with ID: ", docRef);
      })
      .catch((error) => {
        console.error("Error adding document: ", error);
      });
    },
    async isThereOD(uid) {
      await db.collection("od").where("id", "==", uid)
      .get()
        .then((querySnapshot) => {
          if (!querySnapshot.empty) {
            console.log("emp", querySnapshot.empty);
            querySnapshot.forEach((doc) => {
              this.odData = doc.data()

              this.odID = doc.id
              console.log(doc.id, " => ", this.odData.name);
              this.show = false

            });


          }
        })
      .catch((error) => {
        console.log("Error getting documents: ", error);
        this.query = false
        this.show = true
        this.getUserData()

      });


    }
  },

  beforeMount() {
    auth.onAuthStateChanged((user) => {
      if (user) {
        // User is signed in.
        console.log(user.displayName != null);
        if (user.displayName == null) {
          this.isThereOD(user.uid)
          console.log("st home");
          this.$router.push("/user/home")

        } else {
          this.$router.push("/staff/home")
        }
          
      } else {
        // No user is signed in.
        this.$router.push("/")
        console.log("signout");
      }
    });
  }
};
</script>

<style scoped>
.input {
  width: 100%;
  height: 30px;
}

:deep(.q-field__native) {
  line-height: normal;
  height: fit-content;
  color: white;
}

:deep(.q-field__append) {
  height: 30px;
}

:deep(.q-field--filled .q-field__control) {
  height: 30px;
}

:deep(.q-field__prepend) {
  height: 30px;
}

.button {
  width: 100%;
  min-height: 0px;
  padding: 0px;
}

.button2 {
  width: 80%;
  min-height: 0px;
  padding: 0px;
}

.q-item__label {
  height: 30px;
  padding: 6px;
}

/* .dept-btn {
  min-height: 48px !important;
} */
</style>
