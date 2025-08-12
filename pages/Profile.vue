<template>
  <div class="side-bar">
    <!-- Sidebar -->
      <SidebarProvider>
    <AppSidebar />
    <main class="main">
      <SidebarTrigger />
     <ProfileForm />
      <slot />
    </main>
  </SidebarProvider>

   
  </div>
</template>

<script setup>
import { SidebarProvider, SidebarTrigger } from "@/components/ui/sidebar";
import AppSidebar from "@/components/AppSidebar.vue";
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