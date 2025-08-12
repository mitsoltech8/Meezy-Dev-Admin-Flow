<template>
  <div class="flex justify-center">
    <div
      class="flex flex-col-reverse lg:flex-row h-auto lg:h-screen font-sans bg-white overflow-hidden max-w-[1392px] w-full"
    >
      <!-- Left side: Form -->
      <div
        class="w-full lg:w-1/2 py-8 px-6 lg:py-16 lg:px-[100px] flex flex-col justify-center"
      >
        <h1
          class="text-[#232323] font-roboto text-[28px] lg:text-[36px] font-medium my-3"
        >
          Giriş Yap
        </h1>
        <p
          class="text-[#969696] font-inter text-sm lg:text-base font-normal mb-8"
        >
          Hesabınıza erişin ve Meezy’nin tüm özelliklerinin keyfini çıkarın
        </p>

        <!-- Alert -->
       <div class="space-y-4 mb-[32px]">
    <!-- Success Alert -->
    <Alert v-if="showSuccess" class=" bg-green-100 border border-[#178C3A] rounded-lg py-3 px-4 mb-8 max-w-full lg:max-w-[500px] font-sans text-[#178C3A] ">
      <CheckCircle2 class="h-5 w-5 text-[#178C3A]" />
      <AlertTitle class="font-bold">Giriş Başarılı!</AlertTitle>
      <AlertDescription class="text-[#178C3A] ">Hesabınıza başarıyla giriş yaptınız.</AlertDescription>
    </Alert>

    <!-- Error Alert -->
    <Alert v-if="showError" class="bg-red-100 border border-red-300 text-red-800 rounded-lg">
      <XCircle class="h-5 w-5 text-red-600" />
      <AlertTitle class="font-bold">Giriş Başarısız!</AlertTitle>
      <AlertDescription class="text-red-800">Lütfen e-posta ve şifrenizi kontrol edin.</AlertDescription>
    </Alert>
  </div>

       <form @submit.prevent="handleLogin" class="space-y-6">
  <!-- Email -->
  <div>
    <label
      for="email"
      class="block text-[#18181B] font-inter text-sm font-medium mb-2"
    >
      E-posta Adresi
    </label>
    <input
      id="email"
      v-model="form.email"
      type="email"
      placeholder="E-posta adresinizi girin"
      required
      class="w-full px-3 py-4 rounded-md border border-[#E4E4E7] bg-white text-[#18181B] font-inter text-base font-normal shadow-sm placeholder-[#71717A] focus:outline-none"
    />
  </div>

  <!-- Password -->
  <div>
    <label
      for="password"
      class="block text-[#18181B] font-inter text-sm font-medium mb-2"
    >
      Şifre
    </label>
    <div class="relative flex items-center">
      <input
        :type="showPassword ? 'text' : 'password'"
        id="password"
        v-model="form.password"
        placeholder="Şifrenizi girin"
        required
        class="pl-3 pr-11 py-4 w-full rounded-md border border-[#E4E4E7] bg-white text-[#18181B] font-inter text-base font-normal shadow-sm placeholder-[#71717A] focus:outline-none"
      />
      <button
        type="button"
        class="absolute right-3 bg-transparent border-none cursor-pointer flex items-center justify-center w-7 h-7"
        @click="togglePassword"
      >
        <svg
          v-if="!showPassword"
          xmlns="http://www.w3.org/2000/svg"
          width="16"
          height="16"
          fill="none"
        >
          <g clip-path="url(#clip0)">
            <path
              d="M6.587 6.587A2.667 2.667 0 0 0 9.413 9.413M7.153 3.387c.281-.035.564-.053.847-.054 4.667 0 6.667 4.667 6.667 4.667a10.664 10.664 0 0 1-1.113 1.787M4.407 4.407A10.665 10.665 0 0 0 1.333 8s2 4.667 6.667 4.667c1.277.004 2.527-.37 3.593-1.073M1.333 1.333l13.334 13.334"
              stroke="#71717A"
              stroke-width="1.33"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </g>
        </svg>
        <svg
          v-else
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke-width="1.5"
          stroke="currentColor"
          width="16"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M15 12a3 3 0 1 1-6 0 3 3 0 0 1 6 0z"
          />
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M2.458 12C3.732 7.943 7.523 5.25 12 5.25s8.268 2.693 9.542 6.75c-1.274 4.057-5.065 6.75-9.542 6.75S3.732 16.057 2.458 12z"
          />
        </svg>
      </button>
    </div>
  </div>

  <!-- Submit Button -->
  <button
    class="text-[#FAFAFA] font-inter text-base font-semibold leading-[140%] rounded-md bg-[#18181B] py-[11px] px-[38px] shadow-md w-full cursor-pointer"
    type="submit"
  >
    Giriş Yap
  </button>
</form>

      </div>

      <!-- Right side: Image -->
    <div class="w-full lg:w-3/5 flex items-center justify-center overflow-hidden p-3 sm:h-full md:h-full lg:h-full">
  <img
    src="/reg.png"
    alt="Happy user"
    class="h-full w-full object-cover rounded-[24px]"
  />
</div>

    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import FlatPickr from "vue-flatpickr-component";
import "flatpickr/dist/flatpickr.css";
import { Alert, AlertDescription, AlertTitle } from "@/components/ui/alert"
import { CheckCircle2, XCircle } from "lucide-vue-next"

const showSuccess = ref(true) // Sirf design ke liye abhi true rakha hai
const showError = ref(true)  // Error alert dikhana ho to isko true kar do
const form = ref({
  name: "",
  dob: "",
  email: "",
  password: "",
});

const showPassword = ref(false);

function togglePassword() {
  showPassword.value = !showPassword.value;
}

function handleRegister() {
  console.log("Form Submitted:", form.value);
  alert(`Kayıt başarılı: ${form.value.name}`);
}

const dateConfig = {
  dateFormat: "d-m-y",
  allowInput: true,
};
</script>
