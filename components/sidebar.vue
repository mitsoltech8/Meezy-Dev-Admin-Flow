<template>
  <aside class="sidebar">
    <!-- Logo -->
    <div class="logo">
        <span class="logo-svg"><svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 32 32" fill="none">
  <path d="M20.1424 0.843087L16.9853 0L14.3248 9.89565L11.9228 0.961791L8.76555 1.80488L11.3608 11.4573L4.8967 5.01518L2.58549 7.31854L9.67576 14.3848L0.845959 12.0269L0 15.1733L9.64767 17.7496C9.53721 17.2748 9.47877 16.7801 9.47877 16.2717C9.47877 12.6737 12.4055 9.75685 16.0159 9.75685C19.6262 9.75685 22.5529 12.6737 22.5529 16.2717C22.5529 16.7768 22.4952 17.2685 22.3861 17.7405L31.1541 20.0818L32 16.9354L22.314 14.3489L31.1444 11.9908L30.2984 8.84437L20.6128 11.4308L27.0768 4.98873L24.7656 2.68538L17.7737 9.65357L20.1424 0.843087Z" fill="#747474"/>
  <path d="M22.3777 17.7769C22.107 18.9173 21.5356 19.9419 20.7515 20.7628L27.1034 27.0933L29.4147 24.7899L22.3777 17.7769Z" fill="#747474"/>
  <path d="M20.6872 20.8291C19.8936 21.6369 18.8907 22.2397 17.7661 22.5503L20.0775 31.1471L23.2346 30.304L20.6872 20.8291Z" fill="#747474"/>
  <path d="M17.6483 22.5818C17.1265 22.7155 16.5796 22.7866 16.016 22.7866C15.4122 22.7866 14.8275 22.705 14.2724 22.5522L11.959 31.1569L15.1161 31.9999L17.6483 22.5818Z" fill="#747474"/>
  <path d="M14.1608 22.5206C13.0533 22.1945 12.0683 21.584 11.291 20.7739L4.92334 27.1199L7.23454 29.4233L14.1608 22.5206Z" fill="#747474"/>
  <path d="M11.238 20.718C10.474 19.9028 9.91743 18.8919 9.65253 17.769L0.855957 20.1181L1.70191 23.2645L11.238 20.718Z" fill="#747474"/>
</svg></span>
        <span class="logo-text">Meezy Archive</span>
    </div>
<div class="dashboard">Satıcı Paneli</div>
    <!-- Navigation -->
    <nav class="nav">
      <ul class="nav-list">
        <li v-for="(item, index) in menu" :key="index" class="nav-item group">
          <!-- Vertical line for parent + children -->
         

          <!-- Parent Menu Item -->
          <div @click="toggleMenu(index)" class="menu-item">
            <div class="menu">
              <div class="menu-icon" v-html="item.icon"></div>
              <span class="menu-title">{{ item.title }}</span>
            </div>

            <!-- Arrow -->
            <span v-if="item.children.length">
              <svg
                :class="['arrow-icon', item.open ? 'open' : 'closed']"
                xmlns="http://www.w3.org/2000/svg"
                width="16"
                height="16"
                viewBox="0 0 16 16"
                fill="none"
              >
                <path
                  d="M4 6.00049L8 10.0005L12 6.00049"
                  stroke="currentColor"
                  stroke-width="1.5"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                />
              </svg>
            </span>
          </div>

          <!-- Children -->
          <transition name="fade">
            <ul v-if="item.open && item.children.length" class="submenu">
              <li
                v-for="(sub, i) in item.children"
                :key="i"
                class="submenu-item"
              >
                {{ sub }}
              </li>
            </ul>
          </transition>
        </li>
      </ul>
    </nav>

    <!-- Footer -->
    <div class="footer">
      <div class="user-info">
        <div class="user-prof">
        <img src="/Avatar.png" class="avatar" />
        <div>
          <div class="user-name">Arda Gökçek</div>
          <div class="user-level">Seviye 1 • 12.95%</div>
        </div>
        </div>
        <div>
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16" fill="none">
  <path d="M4.6665 10.0003L7.99984 13.3337L11.3332 10.0003M4.6665 6.00033L7.99984 2.66699L11.3332 6.00033" stroke="#3F3F46" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
</svg>
        </div>
      </div>
    </div>
  </aside>
</template>


<script setup>
import { reactive } from 'vue'

const menu = reactive([
 
{
    title: 'Ana Sayfa',
    icon: `<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16" fill="none">
  <g clip-path="url(#clip0_222_9577)">
    <path d="M6 14.6673V8.00065H10V14.6673M2 6.00065L8 1.33398L14 6.00065V13.334C14 13.6876 13.8595 14.0267 13.6095 14.2768C13.3594 14.5268 13.0203 14.6673 12.6667 14.6673H3.33333C2.97971 14.6673 2.64057 14.5268 2.39052 14.2768C2.14048 14.0267 2 13.6876 2 13.334V6.00065Z" stroke="#3F3F46" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
  </g>
  <defs>
    <clipPath id="clip0_222_9577">
      <rect width="16" height="16" fill="white" transform="translate(0 0.000488281)"/>
    </clipPath>
  </defs>
</svg>`,
    open: false,
    children: []
  },
  {
    title: 'Satışlarım',
    icon: `<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16" fill="none">
  <g clip-path="url(#clip0_222_9583)">
    <path d="M1.3667 1.36719H2.70003L4.47337 9.64719C4.53842 9.95043 4.70715 10.2215 4.95051 10.4138C5.19387 10.606 5.49664 10.7074 5.8067 10.7005H12.3267C12.6301 10.7 12.9244 10.596 13.1607 10.4057C13.3971 10.2154 13.5615 9.95021 13.6267 9.65385L14.7267 4.70052H3.41337M6.00016 14.0005C6.00016 14.3687 5.70169 14.6672 5.3335 14.6672C4.96531 14.6672 4.66683 14.3687 4.66683 14.0005C4.66683 13.6323 4.96531 13.3338 5.3335 13.3338C5.70169 13.3338 6.00016 13.6323 6.00016 14.0005ZM13.3335 14.0005C13.3335 14.3687 13.035 14.6672 12.6668 14.6672C12.2986 14.6672 12.0002 14.3687 12.0002 14.0005C12.0002 13.6323 12.2986 13.3338 12.6668 13.3338C13.035 13.3338 13.3335 13.6323 13.3335 14.0005Z" stroke="#3F3F46" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
  </g>
  <defs>
    <clipPath id="clip0_222_9583">
      <rect width="16" height="16" fill="white" transform="translate(0 0.000488281)"/>
    </clipPath>
  </defs>
</svg>`,
    open: true,
    children: ['Aktif', 'Tamamlanan', 'İptal Edilen']
  },
  {
    title: 'Ürünlerim',
    icon: `<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16" fill="none">
  <g clip-path="url(#clip0_222_9615)">
    <path d="M5 2.84701L11 6.28034M2.20003 4.66699L8.00003 8.00033M8.00003 8.00033L13.8 4.66699M8.00003 8.00033L8 14.667M14 5.33359C13.9998 5.09978 13.938 4.87013 13.821 4.6677C13.704 4.46527 13.5358 4.29717 13.3333 4.18026L8.66667 1.51359C8.46397 1.39657 8.23405 1.33496 8 1.33496C7.76595 1.33496 7.53603 1.39657 7.33333 1.51359L2.66667 4.18026C2.46418 4.29717 2.29599 4.46527 2.17897 4.6677C2.06196 4.87013 2.00024 5.09978 2 5.33359V10.6669C2.00024 10.9007 2.06196 11.1304 2.17897 11.3328C2.29599 11.5353 2.46418 11.7034 2.66667 11.8203L7.33333 14.4869C7.53603 14.604 7.76595 14.6656 8 14.6656C8.23405 14.6656 8.46397 14.604 8.66667 14.4869L13.3333 11.8203C13.5358 11.7034 13.704 11.5353 13.821 11.3328C13.938 11.1304 13.9998 10.9007 14 10.6669V5.33359Z" stroke="#3F3F46" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
  </g>
  <defs>
    <clipPath id="clip0_222_9615">
      <rect width="16" height="16" fill="white" transform="translate(0 0.000488281)"/>
    </clipPath>
  </defs>
</svg>`,
    open: false,
    children: ['Satışta', 'Onay Bekleyen', 'Askıda']
  },
  {
    title: 'Ayarlarım',
    icon: `<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16" fill="none">
  <path d="M13.3332 4.66699H7.33317M9.33317 11.3337H3.33317M9.33317 11.3337C9.33317 12.4382 10.2286 13.3337 11.3332 13.3337C12.4377 13.3337 13.3332 12.4382 13.3332 11.3337C13.3332 10.2291 12.4377 9.33366 11.3332 9.33366C10.2286 9.33366 9.33317 10.2291 9.33317 11.3337ZM6.6665 4.66699C6.6665 5.77156 5.77107 6.66699 4.6665 6.66699C3.56193 6.66699 2.6665 5.77156 2.6665 4.66699C2.6665 3.56242 3.56193 2.66699 4.6665 2.66699C5.77107 2.66699 6.6665 3.56242 6.6665 4.66699Z" stroke="#3F3F46" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
</svg>`,
    open: false,
    children: ['Bildirimler', 'Tatil Modu']
  },
  {
    title: 'Satıcı Bilgilerim',
    icon: `<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16" fill="none">
  <path d="M6.66683 10.0005H4.00016C3.29292 10.0005 2.61464 10.2814 2.11454 10.7815C1.61445 11.2816 1.3335 11.9599 1.3335 12.6672V14.0005M14.4669 10.9339L13.8669 10.7339M10.1335 9.26722L9.53353 9.06722M11.0669 12.4672L11.2669 11.8672M12.7336 8.13385L12.9336 7.53385M13.0668 12.4671L12.8001 11.8005M11.2001 8.20052L10.9334 7.53385M9.53353 11.0671L10.2002 10.8005M13.8001 9.20042L14.4668 8.93376M14.0002 10.0005C14.0002 11.1051 13.1047 12.0005 12.0002 12.0005C10.8956 12.0005 10.0002 11.1051 10.0002 10.0005C10.0002 8.89592 10.8956 8.00049 12.0002 8.00049C13.1047 8.00049 14.0002 8.89592 14.0002 10.0005ZM8.66683 4.66716C8.66683 6.13991 7.47292 7.33382 6.00016 7.33382C4.5274 7.33382 3.3335 6.13991 3.3335 4.66716C3.3335 3.1944 4.5274 2.00049 6.00016 2.00049C7.47292 2.00049 8.66683 3.1944 8.66683 4.66716Z" stroke="#3F3F46" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
</svg>`,
    open: false,
    children: []
  }
])

const toggleMenu = (index) => {
  menu[index].open = !menu[index].open
}
</script>

<style scoped>

.logo-text{
    color: #232323;
text-align: center;
font-feature-settings: 'liga' off, 'clig' off;
font-family: Inter;
font-size: 24px;
font-style: normal;
font-weight: 600;
line-height: 110%; 
letter-spacing: -0.96px;
}
.fade-enter-active, .fade-leave-active {
  transition: all 0.2s ease;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
  transform: translateY(-4px);
}
.dashboard{
    overflow: hidden;
color:  #3F3F46;
text-overflow: ellipsis;

font-family:  Inter;
font-size:  12px;
font-style: normal;
font-weight:  500;
line-height:  16px; 
    padding: 8px;
    padding-left: 25px;
}
li{
  list-style-type: none;  
}

.menu{
    gap: 10px;
}
.sidebar {
  max-width: 260px;
  width: 100%;
  height: 100vh;
  background-color: #fafafa;
  display: flex;
  flex-direction: column;
  font-size: 14px;
  position: sticky;
    top: 0px;
}

.logo {
  padding: 20px;

  font-weight: 600;
    display: flex;
    gap: 12px;
    align-items: center;
    margin-bottom: 16px;
}

.nav {
  flex: 1;
  overflow-y: auto;
}

.nav-list {
  position: relative;
  padding-left: 16px;
  margin: 0;
}

.nav-item {
  position: relative;
}


.menu-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 16px 8px 8px;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

.menu-item:hover {
  background-color: #f3f4f6;
}

.menu {
  display: flex;
  align-items: center;
  gap: 10px;
}

.menu-icon {
  width: 20px;
  height: 20px;
  color: #4b5563;
}

.menu-title {
overflow: hidden;
color:  #3F3F46;
text-overflow: ellipsis;

font-family: Inter;
font-size: 14px;
font-style: normal;
font-weight:  400;
line-height: 100%;
}

.arrow-icon {
  width: 16px;
  height: 16px;
  transition: transform 0.2s ease;
  color: #3F3F46;
}

.arrow-icon.closed {
  transform: rotate(-90deg);
}

.arrow-icon.open {
  transform: rotate(0deg);
}

.submenu {
padding: 0px 0 0px 22px;
    border-left: 1px solid #E5E7EB;
    margin-left: 16px;
}

.submenu-item {
  padding: 9px 16px 9px 0;

  overflow: hidden;
color:  #3F3F46;
text-overflow: ellipsis;

font-family: Inter;
font-size: 14px;
font-style: normal;
font-weight: 400;
line-height: 100%; 
}

.submenu-item:hover {
  color: #000;
}

.footer {

  padding: 24px 16px;
  font-size: 12px;
  color: #4b5563;
}

.user-info {
 display: flex
;
    align-items: center;
    gap: 8px;
    justify-content: space-between;
}
.user-prof{
    display: flex;
    align-items: center;
    gap: 8px;
}

.avatar {
  width: 32px;
  height: 32px;
  border-radius: 8px;
}

.user-name {

  overflow: hidden;
color: #3F3F46;
text-overflow: ellipsis;

font-family: Inter;
font-size: 14px;
font-style: normal;
font-weight: 600;
line-height: 16px; /* 14px */
}

.user-level {
  overflow: hidden;
color:  #3F3F46;
text-overflow: ellipsis;
font-family: Inter;
font-size:  12px;
font-style: normal;
font-weight: 400;
line-height: 16px; 
}

</style>
