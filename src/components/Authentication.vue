<template>
  <div>
    <q-input v-model="emailInput" label="Email" />
    <q-btn @click="sendEmailClicked">Send Email</q-btn>
    {{ message }}
    <q-btn @click="logOut">Log Out</q-btn>
  </div>
</template>

<script>
import * as firebase from "firebase/app";
import "firebase/firestore";
import "firebase/auth";

const firebaseConfig = {
  apiKey: "AIzaSyB88kZALOsIXU1_zCtczSgCKQuqWs7vOVo",
  authDomain: "iomoneyjsdev.firebaseapp.com",
  databaseURL: "https://iomoneyjsdev.firebaseio.com",
  projectId: "iomoneyjsdev",
  storageBucket: "iomoneyjsdev.appspot.com",
  messagingSenderId: "861374511473",
  appId: "1:861374511473:web:25a8cdd4a11d2bb85b5ed0"
};

export default {
  // name: 'ComponentName',
  data() {
    return {
      emailInput: "",
      message: ""
    };
  },
  computed: {
    email: {
      get() {
        return window.localStorage.getItem("email");
      },
      set(newEmail) {
        window.localStorage.setItem("email", newEmail);
      }
    }
  },
  mounted() {
    firebase.initializeApp(firebaseConfig);
    firebase.firestore().settings({
      cacheSizeBytes: firebase.firestore.CACHE_SIZE_UNLIMITED
    });
    firebase.firestore().enablePersistence({ synchronizeTabs: true });

    firebase.auth().onAuthStateChanged(account => {
      this.message = account
        ? "Welcome to firebase " + account.email
        : "Logged Out";
    });

    if (firebase.auth().isSignInWithEmailLink(window.location.href)) {
      firebase
        .auth()
        .signInWithEmailLink(this.email, window.location.href)
        .then(() => {
          window.location.replace("https://localhost:8080");
        })
        .catch(error => {
          this.message = error.message;
        });
    }
  },
  methods: {
    sendEmailClicked() {
      firebase
        .auth()
        .sendSignInLinkToEmail(this.emailInput, {
          url: "https://localhost:8080/finishSignUp",
          handleCodeInApp: true,
          iOS: {
            bundleId: "iOmoneyLite"
          },
          dynamicLinkDomain: "iomoneylite.page.link"
        })
        .then(() => {
          this.email = this.emailInput;
          this.message = "Click link in email";
        })
        .catch(error => {
          this.message = error.message;
        });
    },
    logOut() {
      firebase
        .auth()
        .signOut()
        .then(() => {
          window.location.replace("https://localhost:8080");
        })
        .catch(() => {});
    }
  }
};
</script>
