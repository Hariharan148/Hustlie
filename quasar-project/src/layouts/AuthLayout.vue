<template>
  <div id="auth">
    <router-view />
  </div>
</template>

<script>
import { db, auth } from "../../firestore/firestore";
export default {

  beforeMount() {
    auth.onAuthStateChanged((user) => {
      if (user) {
        // User is signed in.
        if (user.displayName != null) {
          this.$router.push("/staff/home");
        } else {
          this.$router.push("/user/home");
        }
        
      } else {
        // No user is signed in.
        this.$router.push("/");
        console.log("signout");
      }
    });
  },
};
</script>

<style scoped>
#auth {
  width: 100%;
  height: 100vh;
}
</style>
