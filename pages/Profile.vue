<template>
  <div class="side-bar">
    <!-- Sidebar -->
    <Sidebar />

    <!-- Main content -->
    <main class="main">
         <div class="breadcrumb-header">
    <div class="breadcrumb-icon">
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16" fill="none">
  <path d="M6 2V14M3.33333 2H12.6667C13.403 2 14 2.59695 14 3.33333V12.6667C14 13.403 13.403 14 12.6667 14H3.33333C2.59695 14 2 13.403 2 12.6667V3.33333C2 2.59695 2.59695 2 3.33333 2Z" stroke="#18181B" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
</svg>
    </div>
    <svg xmlns="http://www.w3.org/2000/svg" width="2" height="16" viewBox="0 0 2 16" fill="none">
  <path d="M1 0.5V15.5" stroke="#E4E4E7"/>
</svg>
    <div class="breadcrumb-text">
      <span class="breadcrumb-link">Satıcı Paneli</span>
      <span class="breadcrumb-separator"><svg xmlns="http://www.w3.org/2000/svg" width="15" height="16" viewBox="0 0 15 16" fill="none">
  <path d="M5.625 11.75L9.375 8L5.625 4.25" stroke="#71717A" stroke-width="1.33" stroke-linecap="round" stroke-linejoin="round"/>
</svg></span>
      <span class="breadcrumb-current">Profil Ayarları</span>
    </div>
  </div>
        <ProfileForm />
  
    
    </main>
  </div>
</template>

<script setup>
import Sidebar from '~/components/sidebar.vue'
import ProfileForm from '~/components/Profile-form/ProfileForm.vue'

</script>
<style scoped>
.side-bar{
    display: flex;
    background-color: #fafafa;
}
.main{
    width: 100%;
    padding: 16px;
    margin: 12px;
    background: #fff;
    border-radius: 10px;
    box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.10), 0 1px 2px 0 rgba(0, 0, 0, 0.06);
}
.breadcrumb-header {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 9px 6px 25px;
}

.breadcrumb-icon img {
  width: 16px;
  height: 16px;
}
.breadcrumb-icon{
    display: flex;
}

.breadcrumb-text {
  display: flex;
  align-items: center;
  gap: 8px;
  font-family: 'Segoe UI', sans-serif;
  font-size: 14px;
  color: #6b7280; 
}

.breadcrumb-link {
color: #71717A;
font-family: Inter;
font-size: 14px;
font-style: normal;
font-weight:  400;
line-height:  20px;
  cursor: pointer;
}

.breadcrumb-current {
color:  #18181B;
font-family:  Inter;
font-size: 14px;
font-style: normal;
font-weight:  400;
line-height:  20px;
}

.breadcrumb-separator {
    
    display: flex;
}

</style>