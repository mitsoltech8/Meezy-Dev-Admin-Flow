<script setup lang="ts">
import { ref, computed } from "vue"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { AspectRatio } from '@/components/ui/aspect-ratio'
import {
  Table, TableBody, TableCell, TableHead, TableHeader, TableRow
} from "@/components/ui/table"
import { Badge } from "@/components/ui/badge"
import { Checkbox } from "@/components/ui/checkbox"
import { MoreHorizontal, ChevronRight, ChevronDown, Edit, Trash, CircleCheckBig, ArrowLeftRight,CornerRightDown, CircleX, Search } from 'lucide-vue-next'
import {
  AlertDialog, AlertDialogTrigger, AlertDialogContent,
  AlertDialogHeader, AlertDialogTitle, AlertDialogDescription,
  AlertDialogFooter, AlertDialogCancel, AlertDialogAction
} from "@/components/ui/alert-dialog"
import {
  DropdownMenu, DropdownMenuTrigger, DropdownMenuContent, DropdownMenuItem
} from "@/components/ui/dropdown-menu"

import {
  Select,
  SelectContent,
  SelectGroup,
  SelectItem,
  SelectLabel,
  SelectTrigger,
  SelectValue,
} from '@/components/ui/select'

type StatusType = "Confirmed" | "Transferred" | "Cancelled" | "Pending"

interface Variation {
  id: number
  name: string
  size: string
  amount: number
  date: string
  status: StatusType
  checked: boolean
}

interface Product {
  id: number
  name: string
  size: string
  amount: number
  date: string
  status: StatusType
  checked: boolean
  variations: Variation[]
}

const products = ref<Product[]>([
  {
    id: 1,
    name: "Yeezy Boost 350 V2 Steel Grey",
    size: "44 2/3",
    amount: 300,
    date: "Jul 30, 2025",
    status: "Pending",
    checked: false,
    variations: [
      { id: 101, name: "Small", size: "40", amount: 280, date: "Jul 28, 2025", status: "Pending", checked: false },
      { id: 102, name: "Medium", size: "41", amount: 285, date: "Jul 27, 2025", status: "Pending", checked: false },
    ]
  },
   {
    id: 2,
    name: "Adidas Superstar",
    size: "One Size",
    amount: 340,
    date: "Jul 24, 2025",
    status: "Transferred",
    checked: false,
    variations: [
      { id: 201, name: "Large", size: "43", amount: 310, date: "Jul 23, 2025", status: "Transferred", checked: false }
    ]
  },
  {
    id: 3,
    name: "Nike Air Jordan 1",
    size: "42",
    amount: 400,
    date: "Jul 26, 2025",
    status: "Cancelled",
    checked: false,
    variations: []
  },
 
  {
    id: 4,
    name: "Yeezy Boost 350 V2 Steel Grey",
    size: "XL",
    amount: 400,
    date: "Jul 26, 2025",
    status: "Confirmed",
    checked: false,
    variations: []
  }
  
])

// expand row state
const expanded = ref<number | null>(null)
function toggleExpand(id: number) {
  expanded.value = expanded.value === id ? null : id
}

// check if anything selected
const anyChecked = computed(() =>
  products.value.some(p => p.checked || p.variations.some(v => v.checked))
)

// total products count
const totalProducts = computed(() => products.value.length)

// selected action (Confirm / Transfer / Cancel)
const selectedAction = ref<StatusType | null>(null)

// transfer extra fields
const transferListing = ref<string>("")
const transferUser = ref<string>("")

// update product/variation status
function updateStatus() {
  if (!selectedAction.value) return

  products.value.forEach(p => {
    if (p.checked) {
      p.status = selectedAction.value!
      p.checked = false
      p.variations.forEach(v => {
        v.status = selectedAction.value!
        v.checked = false
      })
    } else {
      p.variations.forEach(v => {
        if (v.checked) {
          v.status = selectedAction.value!
          v.checked = false
        }
      })
    }
  })

  if (selectedAction.value === "Transferred") {
    console.log("Transfer Screen:", transferListing.value)
    console.log("Transfer Note:", transferUser.value)
    transferListing.value = ""
    transferUser.value = ""
  }

  selectedAction.value = null
}

// edit product/variation
function editItem(item: Product | Variation) {
  alert(`Edit confirmed: ${item.name}`)
}

// delete product/variation
function deleteItem(item: Product | Variation) {
  if ((item as Product).variations !== undefined) {
    products.value = products.value.filter(p => p.id !== (item as Product).id)
  } else {
    products.value.forEach(p => {
      p.variations = p.variations.filter(v => v.id !== (item as Variation).id)
    })
  }
  alert(`Deleted: ${item.name}`)
}
</script>

<template >
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
        <AlertDialog>
          <AlertDialogTrigger as-child>
            <Button
              :disabled="!anyChecked"
              class="bg-green-500 hover:bg-green-500 shadow-sm rounded-md text-white disabled:opacity-50"
              @click="selectedAction = 'Confirmed'"
            >
              <CircleCheckBig />
              Confirm
            </Button>
          </AlertDialogTrigger>
          <AlertDialogContent>
            <AlertDialogHeader>
              <AlertDialogTitle>Confirm Products</AlertDialogTitle>
              <AlertDialogDescription>
                Are you sure you want to <b>Confirm</b> the selected products/variations?
              </AlertDialogDescription>
            </AlertDialogHeader>
            <AlertDialogFooter>
              <AlertDialogCancel>Cancel</AlertDialogCancel>
              <AlertDialogAction class="bg-green-500 hover:bg-green-500 text-white" @click="updateStatus">
                Confirm
              </AlertDialogAction>
            </AlertDialogFooter>
          </AlertDialogContent>
        </AlertDialog>

        <!-- Transfer -->
        <AlertDialog>
          <AlertDialogTrigger as-child>
            <Button
              :disabled="!anyChecked"
              class="bg-blue-500 hover:bg-blue-500 shadow-sm rounded-md text-white disabled:opacity-50"
              @click="selectedAction = 'Transferred'"
            >
              <ArrowLeftRight />
              Transfer
            </Button>
          </AlertDialogTrigger>
          <AlertDialogContent>
            <AlertDialogHeader>
              <AlertDialogTitle class="text-gray-900 text-center font-Inter text-lg font-semibold leading-none">
                Transfer to Another User
              </AlertDialogTitle>
            </AlertDialogHeader>

            <!-- Extra Fields -->
            <div class="space-y-2 my-3">
              <div>
                <label class="text-gray-900 font-Inter text-sm font-medium leading-none">Select Listing to Transfer</label>
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
                <label class="text-gray-900 font-Inter text-sm font-medium leading-none">Select New User</label>
                <Input
                  v-model="transferUser"
                  placeholder="Search by username or email"
                  class="w-full rounded-md border border-gray-300 bg-white shadow-sm focus:border-black focus:outline-none focus-visible:ring-0 transition-all duration-200 mt-1"
                />
              </div>
            </div>

            <AlertDialogFooter>
              <AlertDialogCancel>Cancel</AlertDialogCancel>
              <AlertDialogAction class="text-white" @click="updateStatus">
                Transfer
              </AlertDialogAction>
            </AlertDialogFooter>
          </AlertDialogContent>
        </AlertDialog>

        <!-- Cancel -->
        <AlertDialog>
          <AlertDialogTrigger as-child>
            <Button
              :disabled="!anyChecked"
              class="bg-red-500 hover:bg-red-500 shadow-sm rounded-md text-white disabled:opacity-50"
              @click="selectedAction = 'Cancelled'"
            >
              <CircleX />
              Cancel
            </Button>
          </AlertDialogTrigger>
          <AlertDialogContent>
            <AlertDialogHeader>
              <AlertDialogTitle>Cancel Products</AlertDialogTitle>
              <AlertDialogDescription>
                Are you sure you want to <b>Cancel</b> the selected products/variations?
              </AlertDialogDescription>
            </AlertDialogHeader>
            <AlertDialogFooter>
              <AlertDialogCancel>Back</AlertDialogCancel>
              <AlertDialogAction class="bg-red-500 hover:bg-red-600 text-white" @click="updateStatus">
                Cancel
              </AlertDialogAction>
            </AlertDialogFooter>
          </AlertDialogContent>
        </AlertDialog>
      </div>

      <!-- Search + Sort -->
      <div class="flex flex-col sm:flex-row sm:items-center gap-2 sm:gap-4 sm:ml-auto w-full sm:w-auto">
        <div class="relative w-full sm:w-72">
          <Search class="absolute w-4 h-4 left-3 top-1/2 -translate-y-1/2 text-[#71717A]" />
          <Input
            placeholder="Filter sales by customer or status"
            class="w-full pl-10 rounded-md border border-gray-300 bg-white shadow-sm focus:border-black focus:outline-none focus-visible:ring-0 transition-all duration-200"
          />
        </div>

        <div class="flex items-center gap-2">
          <span class="text-gray-500 font-medium text-xs leading-4 font-Inter">Sort by:</span>
          <Select class="rounded-md border border-gray-200 bg-white shadow-sm">
            <SelectTrigger>
              <SelectValue placeholder="Select" />
            </SelectTrigger>
            <SelectContent>
              <SelectGroup>
                <SelectItem value="Date"> Date </SelectItem>
                <SelectItem value="Prize"> Prize </SelectItem>
              </SelectGroup>
            </SelectContent>
          </Select>
        </div>
      </div>
    </div>

    <!-- Table -->
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
        <template v-for="product in products" :key="product.id">
          <TableRow 
            class="cursor-pointer"
            @click="product.variations.length && toggleExpand(product.id)"
          >
            <TableCell class="w-4">
              <span class="inline-block w-4 ">
                <ChevronRight v-if="product.variations.length && expanded !== product.id" />
                <ChevronDown v-else-if="product.variations.length && expanded === product.id" />
              </span>
            </TableCell>
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
                  <AlertDialog>
                    <AlertDialogTrigger as-child>
                      <DropdownMenuItem @select.prevent>
                        <Edit class="w-4 h-4 mr-2" /> Düzenle
                      </DropdownMenuItem>
                    </AlertDialogTrigger>
                    <AlertDialogContent>
                      <AlertDialogHeader>
                        <AlertDialogTitle>Edit Item</AlertDialogTitle>
                        <AlertDialogDescription>
                          Are you sure you want to edit <b>{{ product.name }}</b>?
                        </AlertDialogDescription>
                      </AlertDialogHeader>
                      <AlertDialogFooter>
                        <AlertDialogCancel>Cancel</AlertDialogCancel>
                        <AlertDialogAction @click="editItem(product)">Confirm</AlertDialogAction>
                      </AlertDialogFooter>
                    </AlertDialogContent>
                  </AlertDialog>

                  <!-- Delete -->
                  <AlertDialog>
                    <AlertDialogTrigger as-child class="bg-[#FEF2F2] hover:bg-[#FECACA] text-red-500">
                      <DropdownMenuItem @select.prevent>
                        <Trash class="w-4 h-4 mr-2 text-red-500" /> Sil
                      </DropdownMenuItem>
                    </AlertDialogTrigger>
                    <AlertDialogContent>
                      <AlertDialogHeader>
                        <AlertDialogTitle>Delete Item</AlertDialogTitle>
                        <AlertDialogDescription>
                          This action cannot be undone. Delete <b>{{ product.name }}</b>?
                        </AlertDialogDescription>
                      </AlertDialogHeader>
                      <AlertDialogFooter>
                        <AlertDialogCancel>Cancel</AlertDialogCancel>
                        <AlertDialogAction class="bg-red-500 hover:bg-red-600 text-white" @click="deleteItem(product)">
                          Delete
                        </AlertDialogAction>
                      </AlertDialogFooter>
                    </AlertDialogContent>
                  </AlertDialog>
                </DropdownMenuContent>
              </DropdownMenu>
            </TableCell>
          </TableRow>

          <!-- Variations -->
          <template v-if="expanded === product.id">
            <TableRow>
              <TableCell></TableCell>
              <TableCell colspan="7" class="font-semibold text-gray-600 flex items-center gap-2">
                Varyasyonlar
                <CornerRightDown class="w-4 h-4 text-gray-600" />
              </TableCell>
            </TableRow>
            <TableRow class="bg-[#fafafa]" v-for="variation in product.variations" :key="variation.id">
              <TableCell></TableCell>
              <TableCell><Checkbox v-model="variation.checked" /></TableCell>
              <TableCell class="flex items-center gap-4">
                <div class="w-[64px]">
                  <AspectRatio :ratio="1 / 1">
                    <img src="/Aspect Ratio.png" alt="Image" class="rounded-md object-cover w-full h-full">
                  </AspectRatio>
                </div>
                {{ product.name }} - {{ variation.name }}
              </TableCell>
              <TableCell>{{ variation.size }}</TableCell>
              <TableCell>{{ variation.amount }}</TableCell>
              <TableCell>{{ variation.date }}</TableCell>
              <TableCell>
                <Badge
                  :class="{
                    'bg-green-500 text-white font-semibold': variation.status === 'Confirmed',
                    'bg-blue-500 text-white font-semibold': variation.status === 'Transferred',
                    'bg-red-500 text-white font-semibold': variation.status === 'Cancelled',
                    'bg-yellow-400 text-black font-semibold': variation.status === 'Pending'
                  }"
                >
                  {{ variation.status }}
                </Badge>
              </TableCell>
              <TableCell>
                <DropdownMenu>
                  <DropdownMenuTrigger as-child>
                    <MoreHorizontal class="w-5 h-5 text-gray-500 cursor-pointer" @click.stop />
                  </DropdownMenuTrigger>
                  <DropdownMenuContent>
                    <!-- Edit Variation -->
                    <AlertDialog>
                      <AlertDialogTrigger as-child>
                        <DropdownMenuItem @select.prevent>
                          <Edit class="w-4 h-4 mr-2" /> Düzenle
                        </DropdownMenuItem>
                      </AlertDialogTrigger>
                      <AlertDialogContent>
                        <AlertDialogHeader>
                          <AlertDialogTitle>Edit Variation</AlertDialogTitle>
                          <AlertDialogDescription>
                            Are you sure you want to edit <b>{{ product.name }} - {{ variation.name }}</b>?
                          </AlertDialogDescription>
                        </AlertDialogHeader>
                        <AlertDialogFooter>
                          <AlertDialogCancel>Cancel</AlertDialogCancel>
                          <AlertDialogAction @click="editItem(variation)">Confirm</AlertDialogAction>
                        </AlertDialogFooter>
                      </AlertDialogContent>
                    </AlertDialog>

                    <!-- Delete Variation -->
                    <AlertDialog>
                      <AlertDialogTrigger as-child class="bg-[#FEF2F2] hover:bg-[#FECACA] text-red-500">
                        <DropdownMenuItem @select.prevent >
                          <Trash class="w-4 h-4 mr-2 text-red-500 " /> Sil
                        </DropdownMenuItem>
                      </AlertDialogTrigger>
                      <AlertDialogContent>
                        <AlertDialogHeader>
                          <AlertDialogTitle>Delete Variation</AlertDialogTitle>
                          <AlertDialogDescription>
                            This action cannot be undone. Delete <b>{{ product.name }} - {{ variation.name }}</b>?
                          </AlertDialogDescription>
                        </AlertDialogHeader>
                        <AlertDialogFooter>
                          <AlertDialogCancel>Cancel</AlertDialogCancel>
                          <AlertDialogAction class="bg-red-500 hover:bg-red-600 text-white" @click="deleteItem(variation)">
                            Delete
                          </AlertDialogAction>
                        </AlertDialogFooter>
                      </AlertDialogContent>
                    </AlertDialog>
                  </DropdownMenuContent>
                </DropdownMenu>
              </TableCell>
            </TableRow>
          </template>
        </template>
      </TableBody>
    </Table>
</div>
   <p class="pt-4 border-t border-gray-200 text-gray-500 font-Inter text-xs font-normal leading-4">
      Toplam {{ totalProducts }} Ürün
    </p>
  </div>
</template>
