<script setup lang="ts">
import { ref, computed } from "vue"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Card, CardContent } from "@/components/ui/card"
import { Checkbox } from "@/components/ui/checkbox"
import { Badge } from "@/components/ui/badge"
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
  SelectGroup
} from "@/components/ui/select"
import {
  Dialog,
  DialogTrigger,
  DialogContent,
  DialogHeader,
  DialogFooter,
  DialogTitle,
  DialogDescription
} from "@/components/ui/dialog"
import { Search, Merge } from "lucide-vue-next"

interface DataRow {
  id: number
  name: string
  amount: number
  status: string
  date: string
}

const data = ref<DataRow[]>([
  { id: 1, name: "JohnDoe", amount: 1200, status: "Paid", date: "Jul 30, 2025" },
  { id: 2, name: "JaneSmith", amount: 950, status: "Pending", date: "Jul 29, 2025" },
  { id: 3, name: "MarkJohnson", amount: 1500, status: "Paid", date: "Jul 25, 2025" },
  { id: 4, name: "SarahWilliams", amount: 1100, status: "Failed", date: "Jul 24, 2025" },
  { id: 5, name: "MichaelBrown", amount: 1050, status: "Pending", date: "Jul 20, 2025" },
])

const selected = ref<number[]>([])
const searchQuery = ref("")
const sortBy = ref("DateDesc")
const isMergeDisabled = computed(() => selected.value.length < 2)

// Dialog state
const isDialogOpen = ref(false)

// Alert/notification state
const alertVisible = ref(false)
const alertMessage = ref("")
const alertType = ref<'success' | 'error'>('success')

// Filtered & Sorted Data
const filteredData = computed(() => {
  let rows = data.value

  if (searchQuery.value.trim() !== "") {
    const q = searchQuery.value.toLowerCase()
    rows = rows.filter(
      row => row.id.toString().includes(q) || row.status.toLowerCase().includes(q)
    )
  }

  if (sortBy.value === "DateAsc") {
    rows = [...rows].sort((a, b) => new Date(a.date).getTime() - new Date(b.date).getTime())
  } else if (sortBy.value === "DateDesc") {
    rows = [...rows].sort((a, b) => new Date(b.date).getTime() - new Date(a.date).getTime())
  } else if (sortBy.value === "Amount") {
    rows = [...rows].sort((a, b) => b.amount - a.amount)
  }

  return rows
})

function toggleSelect(id: number, checked: boolean) {
  if (checked) {
    if (!selected.value.includes(id)) selected.value.push(id)
  } else {
    selected.value = selected.value.filter(x => x !== id)
  }
}

// Show alert
function showAlert(message: string, type: 'success' | 'error') {
  alertMessage.value = message
  alertType.value = type
  alertVisible.value = true
  setTimeout(() => alertVisible.value = false, 3000)
}

// Merge logic
function confirmMerge() {
  if (selected.value.length < 2) {
    showAlert("Please select at least 2 users to merge.", "error")
    isDialogOpen.value = false
    return
  }

  try {
    const selectedRows = data.value.filter(d => selected.value.includes(d.id))

    // Format merge date like "Aug 23, 2025"
    const mergeDate = new Date().toLocaleDateString("en-US", {
      month: "short",
      day: "numeric",
      year: "numeric"
    })

    const mergedRow: DataRow = {
      id: Math.max(...data.value.map(d => d.id)) + 1,
      name: selectedRows.map(r => r.name).join(" & "),
      amount: selectedRows.reduce((sum, r) => sum + r.amount, 0),
      status: "Merged",
      date: mergeDate
    }

    // Remove merged rows and add new merged row
    data.value = data.value.filter(d => !selected.value.includes(d.id))
    data.value.push(mergedRow)

    selected.value = []
    isDialogOpen.value = false

    showAlert("Selected users merged successfully!", "success")
  } catch {
    showAlert("Something went wrong during merge.", "error")
  }
}
</script>

<template>
  <Card class="p-6 w-full relative">
    <!-- Slide-fade Alert -->
    <transition name="slide-fade">
      <div
        v-if="alertVisible"
        :class="[
          'fixed top-12 right-12 px-4 py-4 rounded-md shadow-lg text-white font-medium z-50 flex items-center justify-between gap-4',
          alertType === 'success' ? 'bg-green-500' : 'bg-red-500'
        ]"
      >
        <span class="text-[#FEF2F2] font-inter text-sm font-semibold leading-5">{{ alertMessage }}</span>
        <button 
          @click="alertVisible = false" 
          class="ml-4 text-white hover:text-white focus:outline-none"
        >
          ✕
        </button>
      </div>
    </transition>

    <div class="flex flex-col gap-1">
      <h2 class="text-[#09090B] font-Inter text-xl sm:text-2xl font-semibold leading-7 sm:leading-8">Admin Dashboard</h2>
      <p class="text-gray-500 font-inter text-xs sm:text-sm font-normal leading-5">
        Ürünlerinizi yukarıdaki filtreleri kullanarak duruma göre filtreleyebilirsiniz.
      </p>
    </div>

    <!-- Filters -->
    <div class="flex flex-wrap justify-between items-center mt-4 gap-3">
      <!-- Merge Button with Dialog -->
      <Dialog v-model:open="isDialogOpen">
        <DialogTrigger asChild>
          <Button
            :disabled="isMergeDisabled"
            :class="isMergeDisabled ? 'bg-purple-300 text-white' : 'bg-purple-500 text-white hover:bg-purple-600'"
          >
          <Merge />
            Merge Selected
          </Button>
        </DialogTrigger>
        <DialogContent class="sm:max-w-[425px]">
          <DialogHeader>
            <DialogTitle>Confirm Merge</DialogTitle>
            <DialogDescription>
              Are you sure you want to merge the selected users? This action cannot be undone.
            </DialogDescription>
          </DialogHeader>
          <DialogFooter>
            <Button variant="outline" @click="isDialogOpen = false">Cancel</Button>
            <Button @click="confirmMerge">Confirm Merge</Button>
          </DialogFooter>
        </DialogContent>
      </Dialog>

      <!-- Search + Sort -->
      <div class="flex flex-col sm:flex-row sm:items-center gap-2 sm:gap-4 sm:ml-auto w-full sm:w-auto">
        <div class="relative w-full sm:w-72">
          <Search class="absolute w-4 h-4 left-3 top-1/2 -translate-y-1/2 text-[#71717A]" />
          <Input v-model="searchQuery" placeholder="Filter payment by ID or status"
                 class="w-full pl-10 rounded-md border border-gray-300 bg-white shadow-sm focus:border-black focus:outline-none focus-visible:ring-0 transition-all duration-200"/>
        </div>

        <div class="flex items-center gap-2">
          <span class="text-gray-500 font-medium text-xs leading-4 font-Inter">Sort by:</span>
          <Select v-model="sortBy">
            <SelectTrigger class="w-[160px]">
              <SelectValue placeholder="Select Filter" />
            </SelectTrigger>
            <SelectContent>
              <SelectGroup>
                <SelectItem value="DateAsc">Date - Oldest First</SelectItem>
                <SelectItem value="DateDesc">Date - Newest First</SelectItem>
                <SelectItem value="Amount">Amount</SelectItem>
              </SelectGroup>
            </SelectContent>
          </Select>
        </div>
      </div>
    </div>

    <!-- Table -->
    <CardContent class="mt-6 p-0">
      <table class="w-full border-collapse">
        <thead>
          <tr class="text-left text-sm text-muted-foreground border-b">
            <th class="py-2 px-3 text-[#71717A] text-[14px] leading-[20px] font-medium font-inter">Select</th>
            <th class="py-2 px-3  text-[#71717A] text-[14px] leading-[20px] font-medium font-inter">User Name</th>
            <th class="py-2 px-3 text-right  text-[#71717A] text-[14px] leading-[20px] font-medium font-inter">Amount</th>
            <th class="py-2 px-3 text-right  text-[#71717A] text-[14px] leading-[20px] font-medium font-inter">Date</th>
            <th class="py-2 px-3 text-right  text-[#71717A] text-[14px] leading-[20px] font-medium font-inter"></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="row in filteredData" :key="row.id" class="border-b last:border-0 hover:bg-muted/30">
            <td class="py-6 px-3 overflow-hidden text-ellipsis text-[14px] leading-[20px] font-normal text-[#18181B]">
              <Checkbox
                :checked="selected.includes(row.id)"
                @click="toggleSelect(row.id, !selected.includes(row.id))"
              />
            </td>
            <td class="py-6 px-3 overflow-hidden text-ellipsis text-[14px] leading-[20px] font-normal text-[#18181B]">{{ row.name }}</td>
            <td class="py-6 px-3 text-right overflow-hidden text-ellipsis text-[14px] leading-[20px] font-normal text-[#18181B]">{{ row.amount }} TL</td>
            <td class="py-6 px-3 text-right overflow-hidden text-ellipsis text-[14px] leading-[20px] font-normal text-[#18181B]">{{ row.date }}</td>
            <td class="py-6 px-3 text-right">
               <Badge class="bg-blue-500 text-white px-2 py-1 text-[12px]">
    See Detail
  </Badge>
            </td>
          </tr>
        </tbody>
      </table>

      <div class="text-sm text-muted-foreground mt-4">
        Toplam {{ filteredData.length }} Ürün
      </div>
    </CardContent>
  </Card>
</template>

<style>
.slide-fade-enter-active, .slide-fade-leave-active {
  transition: all 0.3s ease;
}
.slide-fade-enter-from, .slide-fade-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}
.slide-fade-enter-to, .slide-fade-leave-from {
  opacity: 1;
  transform: translateY(0);
}
</style>
