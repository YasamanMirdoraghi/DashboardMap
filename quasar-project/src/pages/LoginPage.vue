<template>
  <q-page class="relative overflow-hidden window-height">
    <div class="map-background fixed-full bg-center bg-cover"></div>

    <!--AnimationDot-->
    <div class="fixed-full z-1" style="pointer-events: none">
      <div v-for="n in 10" :key="n" class="dot" :class="`dot-${n}`"></div>
    </div>

    <!-- Login-->
    <div class="flex justify-center items-center window-height q-px-md z-max relative" >
      <q-card
        class="login-card full-width q-pa-xl z-5 text-center"
        :class="{
          'q-pa-lg': $q.screen.lt.sm,
          'q-pa-xl': $q.screen.gt.xs,
          'mobile-width': $q.screen.lt.sm,
          'desktop-width': $q.screen.gt.xs,
        }"
      >
        <!-- Logo -->
        <div
          class="logo-area flex justify-center items-center overflow-hidden z-1 relative "
          :class="{
            'logo-mobile': $q.screen.lt.sm,
            'logo-desktop': $q.screen.gt.xs,
          }"
        >
          <q-icon
            name="place"
            size="35px"
            color="white"
            :class="{
              'icon-mobile': $q.screen.lt.sm,
              'icon-desktop': $q.screen.gt.xs,
            }"
          />
        </div>

        <!-- Title -->
        <h2
          class="text-h4 text-weight-bold q-mb-lg text-center"
          :class="{
            'text-h5': $q.screen.lt.sm,
            'text-h4': $q.screen.gt.xs,
          }"
        >
          Welcome My App
        </h2>

        <!-- Filed User-->
        <q-input
          v-model="userId"
          label="User ID"
          placeholder="Enter your User ID"
          class="q-mb-5 custom-input"
          :rules="[(val) => !!val || 'User ID is required']"
        >
          <template v-slot:prepend>
            <q-icon
              size="23px"
              name="person"
              class="input-icon"
              :class="{
                'input-icon-mobile': $q.screen.lt.sm,
                'input-icon-desktop': $q.screen.gt.xs,
              }"
            />
          </template>
        </q-input>

        <!-- Filed Password -->
        <q-input
          v-model="password"
          label="Password"
          placeholder="Enter your Password"
          type="password"
          class="q-mb-lg custom-input"
          :rules="[
            (val) => !!val || 'User ID is required',
            (val) => val.length >= 3 || 'Must be at least 3 characters',
            (val) => /^[a-zA-Z0-9]+$/.test(val) || 'Only letters and numbers allowed',
          ]"
          :class="{
            'input-mobile': $q.screen.lt.sm,
            'input-desktop': $q.screen.gt.xs,
          }"
        >
          <template v-slot:prepend>
            <q-icon
              size="23px"
              name="lock"
              class="input-icon"
              :class="{
                'input-icon-mobile': $q.screen.lt.sm,
                'input-icon-desktop': $q.screen.gt.xs,
              }"
            />
          </template>
        </q-input>

        <!--ButtonLogin -->
        <q-btn
          label="Sign In"
          class="login-btn full-width q-pa-sm"
          @click="handleLogin"
          :loading="loading"
          :size="$q.screen.lt.sm ? 'md' : 'lg'"
        />
        <!-- ForgotLink-->
        <p
          class="forgot-password q-mt-md decoration-none fz-14"
          :class="{
            'text-caption': $q.screen.lt.sm,
            'text-body2': $q.screen.gt.xs,
          }"
        >
          <a href="#" class="text-center">Forgot your password?</a>
        </p>

        <!-- AlertSafe -->
        <div
          class="security-notice q-mt-lg q-pa-sm fz-12"
          :class="{
            'text-caption': $q.screen.lt.sm,
            'text-body2': $q.screen.gt.xs,
          }"
        >
          <q-icon
            name="security"
            size="sm"
            class="q-mr-xs"
            :class="{
              'q-mr-xs': $q.screen.lt.sm,
              'q-mr-sm': $q.screen.gt.xs,
            }"
          />
          Your information is securely encrypted
        </div>
      </q-card>
    </div>
  </q-page>
</template>

<script>
import { ref, computed } from "vue";
import { useQuasar } from "quasar";

export default {
  name: "LoginPage",
  setup() {
    const $q = useQuasar();
    const userId = ref("");
    const password = ref("");
    const loading = ref(false);

    // computed برای سایز دکمه
    const buttonSize = computed(() => {
      return $q.screen.lt.sm ? "md" : "lg";
    });

    // computed برای قوانین پسورد
    const passwordRules = computed(() => [
      (val) => !!val || "Password is required",
      (val) => val.length >= 3 || "Must be at least 3 characters",
      (val) => /^[a-zA-Z0-9]+$/.test(val) || "Only letters and numbers allowed",
    ]);

    const handleLogin = async () => {
      if (!userId.value || !password.value) {
        $q.notify({
          type: "negative",
          message: "Please fill in all fields",
          position: "top",
        });
        return;
      }

      loading.value = true;
      await new Promise((resolve) => setTimeout(resolve, 2000));

      loading.value = false;
      $q.notify({
        type: "positive",
        message: "Login successful!",
        position: "top",
      });
    };

    return {
      userId,
      password,
      loading,
      buttonSize,
      passwordRules,
      handleLogin,
    };
  },
};
</script>

<style scoped>
/* Start */
.map-background {
  background-image: url("https://media.wired.com/photos/59269cd37034dc5f91bec0f1/191:100/w_1280,c_limit/GoogleMapTA.jpg?mbid=social_retweet");
  z-index: -1;
  opacity: 0.7;
  filter: blur(2px) grayscale(50%);
  animation: mapMove 30s linear infinite alternate;
}

@keyframes mapMove {
  0% {
    transform: scale(1) translate(0, 0);
  }
  100% {
    transform: scale(1.2) translate(-20px, 20px);
  }
}

.map-background::after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(78, 164, 255, 0.89);
  opacity: 0.6;
  z-index: 1;
}

/* animated-dots  */
.dot {
  position: absolute;
  background: #0095da;
  border-radius: 50%;
  animation: floatRandom 6s infinite ease-in-out;
  box-shadow: 2px 2px 5px rgba(252, 252, 91, 0.3);
}

.dot-1 {
  width: 6px;
  height: 6px;
  left: 10%;
  top: 20%;
  animation-delay: 0s;
  animation-duration: 8s;
}
.dot-2 {
  width: 8px;
  height: 8px;
  left: 25%;
  top: 60%;
  animation-delay: 1s;
  animation-duration: 7s;
}
.dot-3 {
  width: 4px;
  height: 4px;
  left: 40%;
  top: 30%;
  animation-delay: 2s;
  animation-duration: 9s;
}
.dot-4 {
  width: 7px;
  height: 7px;
  left: 60%;
  top: 80%;
  animation-delay: 0.5s;
  animation-duration: 6s;
}
.dot-5 {
  width: 5px;
  height: 5px;
  left: 80%;
  top: 40%;
  animation-delay: 1.5s;
  animation-duration: 8s;
}
.dot-6 {
  width: 9px;
  height: 9px;
  left: 15%;
  top: 70%;
  animation-delay: 3s;
  animation-duration: 7s;
}
.dot-7 {
  width: 6px;
  height: 6px;
  left: 35%;
  top: 10%;
  animation-delay: 2.5s;
  animation-duration: 10s;
}
.dot-8 {
  width: 8px;
  height: 8px;
  left: 70%;
  top: 20%;
  animation-delay: 1.2s;
  animation-duration: 8s;
}
.dot-9 {
  width: 5px;
  height: 5px;
  left: 85%;
  top: 60%;
  animation-delay: 0.8s;
  animation-duration: 9s;
}
.dot-10 {
  width: 7px;
  height: 7px;
  left: 20%;
  top: 85%;
  animation-delay: 2.2s;
  animation-duration: 7s;
}

@keyframes floatRandom {
  0%,
  100% {
    transform: translate(0, 0) scale(1);
    opacity: 0.7;
  }
  25% {
    transform: translate(20px, -15px) scale(1.1);
    opacity: 1;
  }
  50% {
    transform: translate(-15px, 10px) scale(0.9);
    opacity: 0.6;
    box-shadow: 2px 2px 10px rgba(252, 252, 91, 0.5);
  }
  75% {
    transform: translate(10px, 20px) scale(1.05);
    opacity: 0.7;
  }
}
/* Login */
.login-card {
  max-width: 450px;
  border-radius: 20px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.15);
  animation: fadeInScale 0.6s ease-out 0.2s both;
}

@keyframes fadeInScale {
  0% {
    opacity: 0;
    transform: scale(0.8);
  }
  100% {
    opacity: 1;
    transform: scale(1);
  }
}
.logo-area {
  width: 70px;
  height: 70px;
  background: linear-gradient(135deg, #0058c3ff, #0095da) !important;
  border-radius: 20% !important;
  margin: -35px auto 30px auto;
  box-shadow: 0 5px 15px rgba(51, 177, 255, 0.4);
}
/* Btn */
.login-btn {
  background: linear-gradient(135deg, #0058c3ff, #0095da) !important;
  color: white !important;
  border-radius: 8px;
  box-shadow: 0 5px 15px rgba(28, 149, 255, 0.22);
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.login-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(22, 146, 255, 0.22);
}

.login-btn:active {
  transform: translateY(0);
}
/* inputs */
.login-card :deep(.q-field--error .q-field__control),
.login-card :deep(.q-field--error .q-field__label),
.login-card :deep(.q-field--error .q-field__messages),
.login-card :deep(.q-field--error .q-icon) {
  color: #ff4f00 !important;
  border-color: #ff4f00 !important;
}

.custom-input :deep(.q-field__control) {
  border-radius: 8px;
  background-color: #f9f9f9;
  transition: all 0.3s ease;
}

.custom-input :deep(.q-field__control:hover) {
  transform: translateY(-2px);
}

.custom-input :deep(.q-field--focused .q-field__control) {
  border-color: #33a7ffff;
  box-shadow: 0 0 5px rgba(51, 105, 255, 0.5);
}

.input-icon {
  color: #aaa;
  font-size: 18px;
  margin-left: 10px;
}

/* Alert Safe*/
.security-notice {
  background: rgba(152, 205, 246, 0.1);
  border-radius: 8px !important;
  color: #0081eaff;
  animation: fadeIn 1s forwards 1s;
  opacity: 0;
}
@keyframes fadeIn {
  to {
    opacity: 1;
  }
}

/* forgotpassword */
.forgot-password a {
  color: #0081eaff;
  transition: color 0.3s;
}

.forgot-password a:hover {
  color: #555;
  text-decoration: underline;
}
</style>
