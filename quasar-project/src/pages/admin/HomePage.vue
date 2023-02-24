<template>
  <div class="home">
    <OverallStatus :data="OverallStatus" />
    <div class="q-gutter-y-md q-mt-md">
      <q-card>
        <q-tabs v-model="option" align="justify" indicator-color="transparent">
          <q-tab
            name="all"
            label="All"
            no-caps
            class="fw-semibold"
            indicator-color="transparent"
            :class="{ all: option == 'all' }"
          />
          <q-tab
            name="accepted"
            label="Accepted"
            no-caps
            :class="{ accepted: option == 'accepted' }"
          />
          <q-tab
            name="pending"
            label="Pending"
            no-caps
            :class="{ pending: option == 'pending' }"
          />
          <q-tab
            name="rejected"
            label="Rejected"
            no-caps
            :class="{ rejected: option == 'rejected' }"
          />
        </q-tabs>

        <q-separator />
        <div class="q-mt-lg semidark-bg scroll" style="border-radius: 5px">
          <q-tab-panels
            v-model="option"
            animated
            class="br-secondary"
            swipeable
          >
            <q-tab-panel
              name="all"
              class="text-white semidark-bg"
              style="padding: 20px 20px 0 20px"
            >
              <div v-for="item in all" :key="item" class="box-shadow">
                <InfoCard :item="item" />
              </div>
            </q-tab-panel>

            <q-tab-panel
              name="accepted"
              class="text-white semidark-bg"
              style="padding: 20px 20px 0 20px"
            >
              <div v-if="OverallStatus.accepted != 0">
                <div v-for="item in all" :key="item">
                  <div v-if="item.status == 1">
                    <InfoCard :item="item" />
                  </div>
                </div>
              </div>
              <div v-else>
                <div
                  class="black-r-bg br-secondary q-mb-md"
                  style="
                    padding-block: 20px;
                    padding-inline: 25px;
                    height: 100px;
                  "
                >
                  <p style="text-align: center">The list is empty!</p>
                </div>
              </div>
            </q-tab-panel>

            <q-tab-panel
              name="pending"
              class="text-white semidark-bg"
              style="padding: 20px 20px 0 20px"
            >
              <div v-if="OverallStatus.pending != 0">
                <div v-for="item in all" :key="item" class="box-shadow">
                  <div v-if="item.status == 0">
                    <InfoCard :item="item" />
                  </div>
                </div>
              </div>
              <div v-else>
                <div
                  class="black-r-bg br-secondary q-mb-md"
                  style="
                    padding-block: 20px;
                    padding-inline: 25px;
                    height: 100px;
                  "
                >
                  <p style="text-align: center">The list is empty!</p>
                </div>
              </div>
            </q-tab-panel>

            <q-tab-panel
              name="rejected"
              class="text-white semidark-bg"
              style="padding: 20px 20px 0 20px"
            >
              <div v-if="OverallStatus.rejected != 0">
                <div v-for="item in all" :key="item">
                  <div v-if="item.status == 2">
                    <InfoCard :item="item" />
                  </div>
                </div>
              </div>
              <div v-else>
                <div
                  class="black-r-bg br-secondary q-mb-md"
                  style="
                    padding-block: 20px;
                    padding-inline: 25px;
                    height: 100px;
                  "
                >
                  <p style="text-align: center">The list is empty!</p>
                </div>
              </div>
            </q-tab-panel>
          </q-tab-panels>
        </div>
      </q-card>
    </div>
  </div>
</template>

<script>
import OverallStatus from "../../components/OverallStatus.vue";
import InfoCard from "../../components/InfoCard.vue";
import { ref } from "vue";
import { auth } from "../../../firestore/firestore";

export default {
  components: { OverallStatus, InfoCard },
  data() {
    return {
      all: [
        {
          name: "Hariharan C",
          class: "EEE-A",
          rollno: "21EE038",
          period: "2nd to 3rd",
          purpose: "Placement training",
          date: 1666615858736,
          status: 2,
        },
        {
          name: "Pavan S",
          class: "ECE-A",
          rollno: "21EC078",
          period: "2nd to 3rd",
          purpose: "Placement training",
          date: 1666615858736,
          status: 0,
        },
        {
          name: "Sanraj C",
          class: "EEE-A",
          rollno: "21EE038",
          period: "2nd to 3rd",
          purpose: "Placement training",
          date: 1666615858736,
          status: 1,
        },
        {
          name: "Suraj S",
          class: "ECE-A",
          rollno: "21EC078",
          period: "2nd to 3rd",
          purpose: "Placement training",
          date: 1666615858736,
          status: 0,
        },
      ],

      OverallStatus: { total: 15, accepted: 1, pending: 5, rejected: 8 },
    };
  },
  setup() {
    return {
      option: ref("pending"),
    };
  },
  //   beforeMount() {
  //   auth.onAuthStateChanged((user) => {
  //     if (user) {
  //       // User is signed in.
  //       this.$emit()
  //       console.log(user.displayName != null);
  //       if (user.displayName != null) {
  //         this.$router.push("/staff/home")
  //       } else {
  //         this.$router.push("/user/home")
  //       }
          
  //     } else {
  //       // No user is signed in.
  //       this.$router.push("/")
  //       console.log("signout");
  //     }
  //   });
  // }
};
</script>

<style scoped>
.home {
  height: inherit;
  width: 100%;
}

:deep(.q-tab__label, ) {
  color: #ffffff;
  font-weight: 600;
  padding-inline: 9px;
  border-radius: 10px;
}

.all :deep(.q-tab__label) {
  background-color: #40464a;
}

.accepted :deep(.q-tab__label) {
  background-color: #52d371;
  color: #2a2e31;
}

.pending :deep(.q-tab__label) {
  background-color: #ffa44f;
  color: #2a2e31;
}

.rejected :deep(.q-tab__label) {
  background-color: #ff4b4b;
  color: #2a2e31;
}

.q-tab {
  padding: 0 0px;
}
.q-tabs__content--align-justify .q-tab {
  flex: none;
}
:deep(.q-tab__content) {
  padding: 0 3px;
}

:deep(.q-tab__indicator) {
  background-color: transparent;
}
.q-card {
  background: transparent;
}
:deep(.q-tabs__content) {
  justify-content: center;
}

.q-tab-panels {
  background: none;
}

.panel-color {
  background-color: #2a2e31;
}

.scroll {
  overflow-y: scroll;
}
</style>
