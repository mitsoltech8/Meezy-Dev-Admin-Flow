<script setup lang="ts">
import { ref, computed, onMounted } from "vue"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { AspectRatio } from '@/components/ui/aspect-ratio'
import { Progress } from "@/components/ui/progress"
import { Alert } from "@/components/ui/alert"
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from "@/components/ui/table"
import { Badge } from "@/components/ui/badge"
import { Checkbox } from "@/components/ui/checkbox"
import { MoreHorizontal, CircleCheckBig, ArrowLeftRight, CircleX, Search, Edit, Trash } from 'lucide-vue-next'

import { Dialog, DialogTrigger, DialogContent, DialogHeader, DialogTitle, DialogDescription, DialogFooter, DialogClose } from "@/components/ui/dialog"
import { DropdownMenu, DropdownMenuTrigger, DropdownMenuContent, DropdownMenuItem } from "@/components/ui/dropdown-menu"
import { Select, SelectContent, SelectGroup, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select'

// ✅ Types
type StatusType = "Confirmed" | "Transferred" | "Cancelled" | "Pending"

interface Product {
  id: number
  name: string
  size: string
  amount: number
  date: string
  status: StatusType
  checked: boolean
}

// ✅ Dynamic data from API
const products = ref<Product[]>([])

// ✅ Fetch orders from API
const fetchOrders = async () => {
  try {
    const response = await fetch('http://localhost:4000/api/orders')
    const data = await response.json()
    
    // Map API data to our product structure
    const mappedProducts: Product[] = data.orders.flatMap((order: { line_items: { id: any; title: any; variant_title: any; price: string }[]; created_at: string | number | Date }) => 
      order.line_items.map((item: { id: any; title: any; variant_title: any; price: string }) => ({
        id: item.id,
        name: item.title,
        size: item.variant_title || "One Size",
        amount: parseFloat(item.price),
        date: new Date(order.created_at).toLocaleDateString('en-US', { 
          month: 'short', 
          day: 'numeric', 
          year: 'numeric' 
        }),
        status: mapOrderStatus(order),
        checked: false
      }))
    )
    
    products.value = mappedProducts
  } catch (error) {
    console.error('Error fetching orders:', error)
    showAlert('Failed to load orders from API', 'error')
  }
}

// ✅ Map Shopify order status to our status types
const mapOrderStatus = (order: any): StatusType => {
  if (order.cancelled_at) return "Cancelled"
  if (order.fulfillments && order.fulfillments.length > 0) return "Transferred"
  if (order.financial_status === "paid") return "Confirmed"
  return "Pending"
}

// ✅ Status filter with default "All"
const statusFilter = ref<string>("All")

// ✅ Computed filtered products based on status or date
const filteredProducts = computed(() => {
  let filtered = products.value
  if (statusFilter.value && !["All", "DateAsc", "DateDesc"].includes(statusFilter.value)) {
    filtered = filtered.filter(p => p.status === statusFilter.value)
  }
  if (statusFilter.value === "DateAsc") {
    filtered.sort((a, b) => new Date(a.date).getTime() - new Date(b.date).getTime())
  } else if (statusFilter.value === "DateDesc") {
    filtered.sort((a, b) => new Date(b.date).getTime() - new Date(a.date).getTime())
  }
  return filtered
})

// ✅ Computed values
const anyChecked = computed(() => products.value.some(p => p.checked))
const totalProducts = computed(() => products.value.length)
const selectedRows = computed(() => products.value.filter(p => p.checked))

// ✅ Action state
const selectedAction = ref<StatusType | null>(null)
const transferListing = ref<string>("")
const transferUser = ref<string>("")

// ✅ Dialog open/close states
const isConfirmDialogOpen = ref(false)
const isTransferDialogOpen = ref(false)
const isCancelDialogOpen = ref(false)

// ✅ Progress & Payment Dialogs
const isProgressDialogOpen = ref(false)
const isPaymentDialogOpen = ref(false)
const progress = ref(0)

// ✅ Alert system
const alertMessage = ref("")
const alertType = ref<"success" | "error">("success")
const alertVisible = ref(false)

function showAlert(message: string, type: "success" | "error") {
  alertMessage.value = message
  alertType.value = type
  alertVisible.value = true
  setTimeout(() => { alertVisible.value = false }, 5000)
}

// ✅ Status Update
function updateStatus() {
  if (!selectedAction.value) return
  try {
    const selectedCount = selectedRows.value.length
    products.value.forEach(p => {
      if (p.checked) {
        p.status = selectedAction.value!
        p.checked = false
      }
    })
    if (selectedAction.value === "Transferred") {
      transferListing.value = ""
      transferUser.value = ""
    }
    if (selectedAction.value === "Confirmed") {
      showAlert("Status Successfully Updated to Confirm", "success")
      isConfirmDialogOpen.value = false
    } else if (selectedAction.value === "Transferred") {
      showAlert("Status Successfully Updated to Transferred", "success")
      isTransferDialogOpen.value = false
    } else if (selectedAction.value === "Cancelled") {
      showAlert(`Successfully cancelled ${selectedCount} sale(s).`, "success")
      isCancelDialogOpen.value = false
    }
  } catch (e) {
    showAlert(`Action failed: ${selectedAction.value}`, "error")
  }
  selectedAction.value = null
}

// ✅ Edit & Delete Item
function editItem(item: Product) {
  console.log("Edit triggered for:", item.name)
}
function deleteItem(item: Product) {
  try {
    products.value = products.value.filter(p => p.id !== item.id)
    showAlert(`Data Removed Successfully!`, "success")
  } catch (e) {
    showAlert(`Data Deletion Failed!`, "error")
  }
}

// ✅ New Confirm → Progress → Payment Flow
function handleConfirmDialogFlow() {
  isConfirmDialogOpen.value = false
  isProgressDialogOpen.value = true
  progress.value = 0

  const interval = setInterval(() => {
    if (progress.value >= 100) {
      clearInterval(interval)
      isProgressDialogOpen.value = false
      isPaymentDialogOpen.value = true
    } else {
      progress.value += 2
    }
  }, 100)
}

// ✅ Fetch data on component mount
onMounted(() => {
  fetchOrders()
})
</script>

<template>
    <div class="p-4 sm:p-6 bg-white rounded-xl shadow flex flex-col gap-1">
    <h2 class="text-[#09090B] font-Inter text-xl sm:text-2xl font-semibold leading-7 sm:leading-8">
      Admin Dashboard
    </h2>
    <p class="text-gray-500 font-inter text-xs sm:text-sm font-normal leading-5">
      Ürünlerinizi yukarıdaki filtreleri kullanarak duruma göre filtreleyebilirsiniz.
    </p>

  
    <!-- Action buttons -->
  <div class="flex flex-col sm:flex-row gap-2 mb-4 sm:mb-6 mt-4 sm:mt-6 w-full">
    <div class="flex flex-wrap gap-2">

      <!-- Confirm -->
     <Dialog v-model:open="isConfirmDialogOpen">
  <DialogTrigger as-child>
    <Button
      :disabled="!anyChecked"
      class="bg-green-500 hover:bg-green-500 shadow-sm rounded-md text-white disabled:opacity-50"
      @click="selectedAction = 'Confirmed'"
    >
      <CircleCheckBig />
      Confirm
    </Button>
  </DialogTrigger>

  <DialogContent>
    <DialogHeader>
      <DialogTitle>Confirm Payments</DialogTitle>
      <DialogDescription>
        Are you sure you want to confirm the following payments?
      </DialogDescription>

      <!-- ✅ Selected Products / Variations -->
      <ul class="mt-2 space-y-1 list-disc mx-6">
        <li v-for="item in selectedRows" :key="item.id" class="text-[#18181B] font-inter text-sm font-normal leading-5">
          {{ item.name }} - ${{ item.amount }}
        </li>
      </ul>
    </DialogHeader>

    <DialogFooter>
      <DialogClose as-child>
        <Button variant="outline">Cancel</Button>
      </DialogClose>
      <Button
        class="bg-black hover:bg-black-600 text-white"
        @click="handleConfirmDialogFlow"
      >
        Confirm
      </Button>
    </DialogFooter>
  </DialogContent>
</Dialog>


      <Dialog v-model:open="isProgressDialogOpen">
  <DialogContent>
    <DialogHeader>
      <DialogTitle class="text-center">Processing Payments</DialogTitle>
      <DialogDescription class="text-center">
        Your payment is being processed
      </DialogDescription>
    </DialogHeader>

    <div class="mt-0">
      <Progress v-model="progress" class="w-full h-2 rounded-full" />
      <p class="text-sm text-gray-500 mt-2 text-center">{{ progress }}%</p>
    </div>
  </DialogContent>
</Dialog>


<Dialog v-model:open="isPaymentDialogOpen">
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Processing Payments</DialogTitle>
      <DialogDescription>
        Are you sure you want to confirm the following payments?
      </DialogDescription>
       <Alert variant="destructive" class="bg-[#FFE4E4]">
    <CircleX class="w-4 h-4" />
    <AlertTitle>
      <ul class="space-y-1 ">
        <li v-for="item in selectedRows" :key="item.id" class="text-[#18181B] font-inter text-sm font-normal leading-5">
          {{ item.name }} - ${{ item.amount }}
        </li>
      </ul>
    </AlertTitle>
    <AlertDescription>
      Payment failed. Please try again
    </AlertDescription>
  </Alert>
    </DialogHeader>
    <DialogFooter>
      <DialogClose as-child>
        <Button>Close</Button>
      </DialogClose>
    </DialogFooter>
  </DialogContent>
</Dialog>

      <!-- Transfer -->
     <Dialog v-model:open="isTransferDialogOpen">
  <DialogTrigger as-child>
    <Button
      :disabled="!anyChecked"
      class="bg-blue-500 hover:bg-blue-500 shadow-sm rounded-md text-white disabled:opacity-50"
      @click="selectedAction = 'Transferred'"
    >
      <ArrowLeftRight />
      Transfer
    </Button>
  </DialogTrigger>

  <DialogContent>
    <DialogHeader>
      <DialogTitle
        class="text-gray-900 text-center font-Inter text-lg font-semibold leading-none"
      >
        Transfer to Another User
      </DialogTitle>
    </DialogHeader>

    <!-- Extra Fields -->
    <div class="space-y-2 my-3">
      <div>
        <label
          class="text-gray-900 font-Inter text-sm font-medium leading-none"
        >
          Select Listing to Transfer
        </label>
        <div class="w-full mt-1 transfer-slect">
          <Select v-model="transferListing">
            <SelectTrigger>
              <SelectValue placeholder="Select Listing" />
            </SelectTrigger>
            <SelectContent>
              <SelectGroup>
                <SelectItem value="list-1"> List-1 </SelectItem>
                <SelectItem value="list-2"> List-2 </SelectItem>
              </SelectGroup>
            </SelectContent>
          </Select>
        </div>
      </div>
      <div>
        <label
          class="text-gray-900 font-Inter text-sm font-medium leading-none"
        >
          Select New User
        </label>
        <Input
          v-model="transferUser"
          placeholder="Search by username or email"
          class="w-full rounded-md border border-gray-300 bg-white shadow-sm 
                 focus:border-black focus:outline-none focus-visible:ring-0 
                 transition-all duration-200 mt-1"
        />
      </div>
    </div>

    <DialogFooter>
      <DialogClose as-child>
        <Button variant="outline" class="w-1/2">Cancel</Button>
      </DialogClose>

      <!-- ✅ Confirm Button (disable until all conditions true) -->
      <Button
        class="bg-black hover:bg-black text-white w-1/2 disabled:opacity-50"
        :disabled="!anyChecked || !transferListing || !transferUser"
        @click="updateStatus"
      >
        Transfer
      </Button>
    </DialogFooter>
  </DialogContent>
</Dialog>


      <!-- Cancel -->
      <Dialog v-model:open="isCancelDialogOpen">
        <DialogTrigger as-child>
          <Button
            :disabled="!anyChecked"
            class="bg-red-500 hover:bg-red-500 shadow-sm rounded-md text-white disabled:opacity-50"
            @click="selectedAction = 'Cancelled'"
          >
            <CircleX />
            Cancel
          </Button>
        </DialogTrigger>
        <DialogContent>
          <DialogHeader>
            <DialogTitle>Cancel Product</DialogTitle>
            <DialogDescription>
              Are you sure you want to <b>Cancel</b> the selected product/variation?
            </DialogDescription>
          </DialogHeader>
          <DialogFooter>
            <DialogClose as-child>
              <Button variant="outline" class="w-1/2">Cancle</Button>
            </DialogClose>
            <Button class="bg-black  hover:bg-black w-1/2 text-white" @click="updateStatus">
              Confirm
            </Button>
          </DialogFooter>
        </DialogContent>
      </Dialog>

    </div>

    <!-- Search + Sort -->
    <div class="flex flex-col sm:flex-row sm:items-center gap-2 sm:gap-4 sm:ml-auto w-full sm:w-auto">
      <div class="relative w-full sm:w-72">
        <Search class="absolute w-4 h-4 left-3 top-1/2 -translate-y-1/2 text-[#71717A]" />
        <Input
          placeholder="Filter sales by customer or status"
          class="w-full pl-10 rounded-md border border-gray-300 bg-white shadow-sm 
                 focus:border-black focus:outline-none focus-visible:ring-0 
                 transition-all duration-200"
        />
      </div>
<div class="flex items-center gap-2 product-filter">
  <span class="text-gray-500 font-medium text-xs leading-4 font-Inter">Sort by:</span>
    <Select v-model="statusFilter" class="rounded-md border border-gray-200 bg-white shadow-sm focus:border-black focus:outline-none focus-visible:ring-0 ">
  <SelectTrigger>
    <SelectValue placeholder="Select Filter" />
  </SelectTrigger>
  <SelectContent>
    <SelectGroup>
      <SelectItem value="All">All</SelectItem>
      <SelectItem value="Pending">Pending</SelectItem>
      <SelectItem value="Confirmed">Confirmed</SelectItem>
      <SelectItem value="Transferred">Transferred</SelectItem>
      <SelectItem value="Cancelled">Cancelled</SelectItem>
      <SelectItem value="DateAsc">Date - Oldest First</SelectItem>
      <SelectItem value="DateDesc">Date - Newest First</SelectItem>
    </SelectGroup>
  </SelectContent>
</Select>

  </div>
    </div>
  </div>


  <div class="overflow-x-auto -mx-2 sm:mx-0">
  <Table class="min-w-[640px] sm:min-w-0">
    <TableHeader>
      <TableRow>
        <TableHead></TableHead>
        <TableHead>Select</TableHead>
        <TableHead>Ürün</TableHead>
        <TableHead>Beden</TableHead>
        <TableHead>Amount</TableHead>
        <TableHead>Date</TableHead>
        <TableHead>Durum</TableHead>
        <TableHead></TableHead>
      </TableRow>
    </TableHeader>
    <TableBody>
      <template v-for="product in filteredProducts" :key="product.id">
        <TableRow class="cursor-pointer">
          <TableCell class="w-4"></TableCell>
          <TableCell @click.stop>
            <Checkbox v-model="product.checked" />
          </TableCell>
          <TableCell class="flex items-center gap-4">
            <div class="w-[64px]">
              <AspectRatio :ratio="1 / 1">
                <img src="/Aspect Ratio.png" alt="Image" class="rounded-md object-cover w-full h-full">
              </AspectRatio>
            </div>
            {{ product.name }}
          </TableCell>
          <TableCell>{{ product.size }}</TableCell>
          <TableCell>{{ product.amount }}</TableCell>
          <TableCell>{{ product.date }}</TableCell>
          <TableCell>
            <Badge
              :class="{
                'bg-green-500 text-white font-semibold': product.status === 'Confirmed',
                'bg-blue-500 text-white font-semibold': product.status === 'Transferred',
                'bg-red-500 text-white font-semibold': product.status === 'Cancelled',
                'bg-yellow-400 text-black font-semibold': product.status === 'Pending'
              }"
            >
              {{ product.status }}
            </Badge>
          </TableCell>
          <TableCell>
            <DropdownMenu>
              <DropdownMenuTrigger as-child>
                <MoreHorizontal class="w-5 h-5 text-gray-500 cursor-pointer" @click.stop />
              </DropdownMenuTrigger>
              <DropdownMenuContent>
                <!-- Edit -->
                <Dialog>
                  <DialogTrigger as-child>
                    <DropdownMenuItem @select.prevent>
                      <Edit class="w-4 h-4 " /> Düzenle
                    </DropdownMenuItem>
                  </DialogTrigger>
                  <DialogContent>
                    <DialogHeader>
                      <DialogTitle>Edit Item</DialogTitle>
                      <DialogDescription>
                        Are you sure you want to edit <b>{{ product.name }}</b>?
                      </DialogDescription>
                    </DialogHeader>
                    <DialogFooter>
                      <DialogClose as-child>
                        <Button variant="outline" class="focus:outline-none focus:ring-0 focus-visible:ring-0 w-1/2">Cancel</Button>
                      </DialogClose>
                      <Button @click="editItem(product)" class="w-1/2">Confirm</Button>
                    </DialogFooter>
                  </DialogContent>
                </Dialog>

                <!-- Delete -->
                <Dialog>
                  <DialogTrigger as-child>
                    <DropdownMenuItem @select.prevent class="text-red-600 bg-[#FEF2F2] hover:text-red-600 hover:bg-[#fbd0d0] focus:text-red-600 focus:bg-[#fbd0d0]">
                      <Trash class="w-4 h-4  text-red-500" /> Sil
                    </DropdownMenuItem>
                  </DialogTrigger>
                  <DialogContent>
                    <DialogHeader>
                      <DialogTitle class="text-center">Remove Listing?</DialogTitle>
                      <DialogDescription class="text-center">
                        Are you sure you want to remove listing?
                      </DialogDescription>
                    </DialogHeader>
                    <DialogFooter>
                      <Button class="bg-black hover:bg-red-600 text-white focus-visible:ring-0 w-full" @click="deleteItem(product)">
                        Remove Listing
                      </Button>
                    </DialogFooter>
                  </DialogContent>
                </Dialog>

              </DropdownMenuContent>
            </DropdownMenu>
          </TableCell>
        </TableRow>

        <!-- ⚡ Variations Removed Here ⚡ -->

      </template>
    </TableBody>
  </Table>
</div>
   <p class="pt-4 border-t border-gray-200 text-gray-500 font-Inter text-xs font-normal leading-4">
      Toplam {{ totalProducts }} Ürün
    </p>
  </div>

<transition name="slide-fade">
  <div
    v-if="alertVisible"
    :class="[
      'fixed top-12 right-12 px-4 py-4  rounded-md shadow-lg text-white font-medium z-50 flex items-center justify-between gap-4',
      alertType === 'success' ? 'bg-green-500' : 'bg-red-500'
    ]"
  >
    <span class="text-[#FEF2F2] font-inter text-sm not-italic font-semibold leading-5">{{ alertMessage }}</span>
    
    <!-- Cross Icon -->
    <button 
      @click="alertVisible = false" 
      class="ml-4 text-white hover:text-white focus:outline-none"
    >
      ✕
    </button>
  </div>
</transition>

</template>