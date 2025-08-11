<template>
    <div class="alert-box">
  <span class="alert-message">Veriler Başarıyla Kaydedildi</span>
  <button class="alert-close"><svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16" fill="none">
  <path d="M12 4L4 12M4 4L12 12" stroke="#F4F4F5" stroke-width="1.33" stroke-linecap="round" stroke-linejoin="round"/>
</svg></button>
</div>

  <div class="profile-form-container">
    <!-- Profile Image Upload -->
   <div class="profile-image-wrapper">
    <img :src="imagePreview" alt="Profile" class="profile-image" />

    <label for="profile-upload" class="upload-icon">
      <!-- Your SVG Icon -->
      <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 16 16" fill="none">
        <path d="M8 2.00016H3.33333C2.97971 2.00016 2.64057 2.14064 2.39052 2.39069C2.14048 2.64074 2 2.97987 2 3.3335V12.6668C2 13.0205 2.14048 13.3596 2.39052 13.6096C2.64057 13.8597 2.97971 14.0002 3.33333 14.0002H12.6667C13.0203 14.0002 13.3594 13.8597 13.6095 13.6096C13.8595 13.3596 14 13.0205 14 12.6668V8.00016M12.2501 1.75015C12.5153 1.48493 12.875 1.33594 13.2501 1.33594C13.6251 1.33594 13.9848 1.48493 14.2501 1.75015C14.5153 2.01537 14.6643 2.37508 14.6643 2.75015C14.6643 3.12522 14.5153 3.48493 14.2501 3.75015L8.2414 9.75948C8.0831 9.91765 7.88753 10.0334 7.67273 10.0962L5.7574 10.6562C5.70003 10.6729 5.63922 10.6739 5.58134 10.6591C5.52345 10.6442 5.47061 10.6141 5.42836 10.5719C5.38611 10.5296 5.35599 10.4768 5.34116 10.4189C5.32633 10.361 5.32733 10.3002 5.34406 10.2428L5.90406 8.32748C5.96708 8.11285 6.08309 7.91752 6.2414 7.75948L12.2501 1.75015Z" stroke="#3F3F46" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
      </svg>

      <input type="file" id="profile-upload" class="hidden" @change="handleImageUpload" />
    </label>
  </div>

    <!-- Form -->
    <form @submit.prevent="submitForm" class="form-section-profile">
      <!-- 1. Kişisel Bilgiler -->
      <div class="form-group">
        <h2>1. Kişisel Bilgiler</h2>
        <div class="form-row">
          <div class="form-field">
            <label>Adınız</label>
            <input type="text" placeholder="Adınızı girin" />
          </div>
          <div class="form-field">
            <label>Soyadınız</label>
            <input type="text" placeholder="Soyadınızı girin" />
          </div>
        </div>
        <div class="form-field">
          <label>National ID (TCID)</label>
          <input type="text" placeholder="Enter your national ID number (e.g., 12345678901)" />
        </div>
      </div>

      <!-- 2. Hesap Bilgileri -->
      <div class="form-group">
        <h2>2. Hesap Bilgileri</h2>
        <div class="form-field form-field1">
          <label>E-posta</label>
          <input type="email" placeholder="E-posta adresinizi girin (örn. john@example.com)" />
        </div>
        <div class="form-row">
          <div class="form-field">
            <label>Şifreyi Değiştir</label>
            <input type="password" placeholder="Yeni şifrenizi girin" />
          </div>
          <div class="form-field">
            <label>Şifreyi Onayla</label>
            <input type="password" placeholder="Yeni şifrenizi tekrar girin" />
          </div>
        </div>
      </div>

      <!-- 3. İletişim Bilgileri -->
      <div class="form-group">
        <h2>3. İletişim Bilgileri</h2>
        <div class="form-row">
          <div class="form-field">
            <label>Telefon Numarası</label>
            <input type="text" placeholder="Telefon numaranızı girin (örn. +90 532...)" />
          </div>
          <div class="form-field">
            <label>Şehir</label>
            <input type="text" placeholder="Şehrinizi girin (örn. İstanbul)" />
          </div>
        </div>
        <div class="form-field">
          <label>Adres</label>
          <input type="text" placeholder="Tam adresinizi girin (örn. 123 Sokak, Daire 4B)" />
        </div>
      </div>

      <!-- 4. İşletme Bilgileri -->
      <div class="form-group">
        <h2>4. İşletme Bilgileri</h2>
        <div class="form-row">
          <div class="form-field">
            <label>Vergi Numarası</label>
            <input type="text" placeholder="Varsa vergi numaranızı girin" />
          </div>
          <div class="form-field">
            <label>Banka Hesabı</label>
            <input type="text" placeholder="Banka hesap numaranızı veya IBAN girin" />
          </div>
        </div>
        <div class="form-field">
          <label>Vergi Detayları</label>
          <input type="text" placeholder="Vergi ile ilgili ek bilgileri girin" />
        </div>
      </div>

      <!-- Submit -->
      <div class="form-submit">
        <button type="submit">Değişiklikleri Kaydet</button>
      </div>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import './ProfileForm.css'

const imagePreview = ref('/Ellipse 1.svg')

function handleImageUpload(e) {
  const file = e.target.files[0]
  if (file) {
    imagePreview.value = URL.createObjectURL(file)
  }
}

function submitForm() {
  console.log('Form submitted')
}
</script>
