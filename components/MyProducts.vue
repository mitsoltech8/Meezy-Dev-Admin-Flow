<script setup lang="ts">
import { ref, computed } from 'vue'
import { Badge } from '@/components/ui/badge'
import { Button } from '@/components/ui/button'
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow
} from '@/components/ui/table'
import {
  DropdownMenu,
  DropdownMenuTrigger,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuSeparator
} from '@/components/ui/dropdown-menu'
import { Cross, Filter, ListFilter, MoreHorizontal } from 'lucide-vue-next'

// Active Tab
const activeTab = ref<'satis' | 'onay'>('satis')

// Dummy Data
const satisProducts = [
  { id: 1, name: 'Yeezy Boost 350 V2 Steel Grey', size: '44 2/3', price: 300, sellerPrice: '12.000,00 TL', status: 'Satışta' },
  { id: 2, name: 'Yeezy Boost 350 V2 Steel Grey', size: '41 1/3', price: 250, sellerPrice: '12.000,00 TL', status: 'Satışta' }
]

const onayProducts = [
  { id: 3, name: 'Yeezy Boost 350 V2 Steel Grey', size: '44 2/3', price: 200, sellerPrice: '8.000,00 TL', status: 'Onay Bekleyen' },
  { id: 4, name: 'Yeezy Boost 350 V2 Steel Grey', size: '41 1/3', price: 220, sellerPrice: '9.500,00 TL', status: 'Onay Bekleyen' }
]

const products = computed(() => activeTab.value === 'satis' ? satisProducts : onayProducts)

const editProduct = (id: number) => {
  alert(`Düzenle clicked for product ID: ${id}`)
}

const deleteProduct = (id: number) => {
  alert(`Sil clicked for product ID: ${id}`)
}
</script>

<template>
  <div class="space-y-4">
    <!-- Header Actions -->
    <div class="flex justify-between items-center mb-2.5">
      <!-- Tab Switcher -->
      <div class="relative flex bg-gray-100 rounded-lg p-1 w-[260px] overflow-hidden">
        <!-- Slider -->
        <div
          class="absolute top-1 bottom-1 w-1/2  bg-white rounded-lg shadow transition-all duration-300"
          :class="activeTab === 'satis' ? 'left-1' : 'left-[48%]'"
        ></div>

        <!-- Satışta -->
        <button
          class="z-10 w-1/2 text-center py-2 font-medium transition-colors"
          :class="activeTab === 'satis' ? 'text-black' : 'text-gray-500'"
          @click="activeTab = 'satis'"
        >
          Satışta
        </button>

        <!-- Onay Bekleyen -->
        <button
          class="z-10 w-1/2 text-center py-2 font-medium transition-colors"
          :class="activeTab === 'onay' ? 'text-black' : 'text-gray-500'"
          @click="activeTab = 'onay'"
        >
          Onay Bekleyen
        </button>
      </div>

      <!-- Right Buttons -->
      <div class="flex gap-2">
        <!-- Filter -->
        <DropdownMenu>
          <DropdownMenuTrigger as-child>
            <Button variant="outline" class="flex items-center gap-2">
              <ListFilter class="w-4 h-4" />
              Filter
            </Button>
          </DropdownMenuTrigger>
          <DropdownMenuContent>
            <DropdownMenuItem>Fiyat (Azdan Çoğa)</DropdownMenuItem>
            <DropdownMenuItem>Fiyat (Çoktan Aza)</DropdownMenuItem>
            <DropdownMenuItem>Beden</DropdownMenuItem>
            <DropdownMenuItem>Durum</DropdownMenuItem>
          </DropdownMenuContent>
        </DropdownMenu>

        <!-- Ürün Ekle -->
        <Button class="flex items-center gap-2">
            <Cross class="w-4 h-4" />
            Ürün Ekle</Button>
      </div>
    </div>

    <!-- Table Section -->
    <div class="rounded-lg border border-[#E4E4E7] bg-white shadow-sm flex flex-col items-start  self-stretch p-[24px]">
        <div class="gap-6 flex flex-col items-start  self-stretch">
      <!-- Heading & Description -->
      <div>
        <h2 class="text-[#09090B] font-inter text-2xl font-semibold leading-8">Ürünlerim</h2>
        <p class="text-[#71717A] font-inter text-sm font-normal leading-5">
          Ürünlerinizi yukarıdaki filtreleri kullanarak duruma göre filtreleyebilirsiniz.
        </p>
      </div>

      <!-- Table -->
      <Table>
        <TableHeader>
          <TableRow>
            <TableHead>Ürün</TableHead>
            <TableHead>Beden</TableHead>
            <TableHead>Fiyat</TableHead>
            <TableHead>Satıcıya Kalan</TableHead>
            <TableHead>Durum</TableHead>
            <TableHead class="text-right">İşlem</TableHead>
          </TableRow>
        </TableHeader>
        <TableBody>
          <TableRow v-for="product in products" :key="product.id">
            <TableCell class="flex items-center gap-3">
              <div class="w-16 h-16 bg-gray-200 rounded "></div>
              <span>{{ product.name }}</span>
            </TableCell>
            <TableCell>{{ product.size }}</TableCell>
            <TableCell>{{ product.price }}</TableCell>
            <TableCell>{{ product.sellerPrice }}</TableCell>
            <TableCell>
              <Badge variant="outline" class="text-[#18181B] font-inter text-xs font-semibold leading-4 py-[2px] px-[10px]">{{ product.status }}</Badge>
            </TableCell>
            <TableCell class="text-right">
              <DropdownMenu>
                <DropdownMenuTrigger as-child>
                  <Button variant="ghost" class="h-8 w-8 p-0">
                    <span class="sr-only">Open menu</span>
                    <MoreHorizontal class="h-4 w-4" />
                  </Button>
                </DropdownMenuTrigger>
                <DropdownMenuContent align="end">
                  <DropdownMenuItem @click="editProduct(product.id)">
                    Düzenle
                  </DropdownMenuItem>
                  <DropdownMenuSeparator />
                  <DropdownMenuItem
                    @click="deleteProduct(product.id)"
                    class="text-red-600 focus:text-red-600"
                  >
                    Sil
                  </DropdownMenuItem>
                </DropdownMenuContent>
              </DropdownMenu>
            </TableCell>
          </TableRow>
        </TableBody>
      </Table>
</div>
      <!-- Bottom Left Product Count -->
      <div class="pt-4 text-sm text-gray-600 border-t border-[#E4E4E7] w-full m-0">
        Toplam {{ products.length }} Ürün
      </div>
    </div>
    
  </div>
</template>
