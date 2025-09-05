<script setup lang="ts">
import { ref, computed, onMounted } from "vue"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Select, SelectTrigger, SelectContent, SelectItem, SelectValue } from "@/components/ui/select"
import {
  Table, TableHeader, TableRow, TableHead, TableBody, TableCell,
} from "@/components/ui/table"
import { Checkbox } from "@/components/ui/checkbox"
import {
  DropdownMenu, DropdownMenuTrigger, DropdownMenuContent, DropdownMenuItem
} from "@/components/ui/dropdown-menu"
import { Dialog, DialogContent, DialogHeader, DialogFooter, DialogTitle } from "@/components/ui/dialog"
import { CircleCheckBig, MoreVertical, UserRoundCheck, UserRoundX, Search } from "lucide-vue-next"

interface User {
  _id: string
  name: string
  email: string
  dob: string
  status: "Active" | "Not Active"
  date: string
  checked: boolean
}

// Replace the static sellers data with dynamic users data
const users = ref<User[]>([])
const loading = ref(true)
const error = ref("")

// Fetch users from backend API
const fetchUsers = async () => {
  try {
    loading.value = true
    const response = await fetch('http://localhost:4000/api/user/users')
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`)
    }
    
    const data = await response.json()
    
    // Transform API data to match our interface
    users.value = data.users.map((user: any) => ({
      _id: user._id,
      name: user.name,
      email: user.email,
      dob: user.dob,
      status: Math.random() > 0.5 ? "Active" : "Not Active", // Random status for demo
      date: new Date(user.dob).toLocaleDateString('en-US', { 
        year: 'numeric', 
        month: 'short', 
        day: 'numeric' 
      }),
      checked: false
    }))
  } catch (err) {
    error.value = `Failed to fetch users: ${err.message}`
    console.error('Error fetching users:', err)
  } finally {
    loading.value = false
  }
}

// Fetch users when component mounts
onMounted(() => {
  fetchUsers()
})

// ✅ states
const isAnySelected = computed(() => users.value.some(u => u.checked))
const filterText = ref("")
const sortBy = ref("Date")

// ✅ alert state
const alertMessage = ref("")
const alertType = ref<"success" | "error">("success")
const showAlert = ref(false)

// ✅ dialog state
const showDialog = ref(false)
const selectedAction = ref<"Confirm" | "Active" | "Not Active" | "Delete" | null>(null)
const deleteTargetId = ref<string | null>(null)

// ✅ filtering
const filteredUsers = computed(() => {
  const query = filterText.value.toLowerCase().trim()

  let result = users.value.filter(u => {
    if (
      u._id.toLowerCase().includes(query) ||
      u.name.toLowerCase().includes(query) ||
      u.email.toLowerCase().includes(query)
    ) {
      return true
    }
    if (query === "active" && u.status === "Active") return true
    if (query === "not active" && u.status === "Not Active") return true
    return false
  })

  if (sortBy.value === "Date") {
    result = result.sort((a, b) => new Date(b.dob).getTime() - new Date(a.dob).getTime())
  } else if (sortBy.value === "Status") {
    result = result.sort((a, b) => a.status.localeCompare(b.status))
  }

  return result
})

// ✅ show notification helper
const showNotification = (message: string, type: "success" | "error") => {
  alertMessage.value = message
  alertType.value = type
  showAlert.value = true
  setTimeout(() => {
    showAlert.value = false
  }, 3000) // auto close in 3s
}

// ✅ action handler (Active/Not Active/Confirm)
const handleAction = (action: "Confirm" | "Active" | "Not Active") => {
  selectedAction.value = action
  showDialog.value = true
}

// ✅ delete action handler
const handleDelete = (id: string) => {
  selectedAction.value = "Delete"
  deleteTargetId.value = id
  showDialog.value = true
}

const confirmAction = () => {
  try {
    if (selectedAction.value === "Delete" && deleteTargetId.value !== null) {
      users.value = users.value.filter(u => u._id !== deleteTargetId.value)
      showNotification("User deleted successfully", "success")
    } else {
      users.value.forEach(u => {
        if (u.checked) {
          if (selectedAction.value === "Active") u.status = "Active"
          if (selectedAction.value === "Not Active") u.status = "Not Active"
          // confirm pe extra logic bhi add kar sakte ho
        }
      })
      showNotification(`Users updated as ${selectedAction.value}`, "success")
    }
  } catch (e) {
    showNotification("Failed to update users", "error")
  } finally {
    showDialog.value = false
    selectedAction.value = null
    deleteTargetId.value = null
  }
}
</script>

<template>
  <div class="space-y-3 relative">
    <!-- ✅ Custom Alert -->
    <transition name="slide-fade">
      <div
        v-if="showAlert"
        class="fixed top-4 right-4 flex items-center gap-6 px-6 py-6 rounded-lg shadow-lg text-white z-50"
        :class="alertType === 'success' ? 'bg-green-500' : 'bg-red-500'"
      >
        <span>{{ alertMessage }}</span>
        <button @click="showAlert = false" class="ml-2 font-bold">×</button>
      </div>
    </transition>

    <!-- Loading state -->
    <div v-if="loading" class="flex justify-center items-center py-12">
      <div class="animate-spin rounded-full h-12 w-12 border-b-2 border-gray-900"></div>
    </div>

    <!-- Error state -->
    <div v-else-if="error" class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative" role="alert">
      <strong class="font-bold">Error: </strong>
      <span class="block sm:inline">{{ error }}</span>
      <Button class="mt-2" @click="fetchUsers">Try Again</Button>
    </div>

    <!-- Content when data is loaded -->
    <template v-else>
      <!-- Card Actions -->
      <div class="flex flex-col gap-3 justify-between p-4 sm:flex-row sm:items-center sm:justify-between">
        <!-- ✅ Action Buttons -->
        <div class="flex flex-col gap-2 sm:flex-row">
          <Button
            :disabled="!isAnySelected"
            class="bg-green-500 hover:bg-green-600 flex items-center gap-1 justify-center"
            @click="handleAction('Confirm')"
          >
            <CircleCheckBig class="w-4 h-4" />
            Confirm
          </Button>
          <Button
            :disabled="!isAnySelected"
            class="bg-blue-500 hover:bg-blue-600 flex items-center gap-1 justify-center"
            @click="handleAction('Active')"
          >
            <UserRoundCheck class="w-4 h-4" />
            Active
          </Button>
          <Button
            :disabled="!isAnySelected"
            class="bg-red-500 hover:bg-red-600 flex items-center gap-1 justify-center"
            @click="handleAction('Not Active')"
          >
            <UserRoundX class="w-4 h-4" />
            Not Active
          </Button>
        </div>

        <!-- ✅ Filters -->
        <div class="flex  gap-2 sm:flex-row sm:items-center sm:gap-3 w-full sm:w-auto">
          <!-- Search -->
          <div class="relative w-full sm:w-72">
            <Search class="absolute w-4 h-4 left-3 top-1/2 -translate-y-1/2 text-[#71717A]" />
            <Input
              v-model="filterText"
              placeholder="Filter by ID, Name, Email or Status"
              class="w-full pl-10 rounded-md border border-gray-300 bg-white shadow-sm 
                    focus:border-black focus:outline-none focus-visible:ring-0 
                    transition-all duration-200"
            />
          </div>

          <!-- Sort -->
          <div class="flex items-center gap-2">
            <span class="text-gray-500 font-medium text-xs leading-4 w-max sort">Sort by:</span>
            <Select
              v-model="sortBy"
              class="rounded-md border border-gray-200 bg-white shadow-sm focus:border-black focus:outline-none focus-visible:ring-0"
            >
              <SelectTrigger class="w-[120px]">
                <SelectValue placeholder="Sort by" />
              </SelectTrigger>
              <SelectContent>
                <SelectItem value="Date">Date</SelectItem>
                <SelectItem value="Status">Status</SelectItem>
              </SelectContent>
            </Select>
          </div>
        </div>
      </div>

      <!-- ✅ Card -->
      <div class="border rounded-lg shadow-sm bg-white p-6 flex flex-col gap-6">
        <div >
          <h2 class="text-[24px] font-semibold leading-[32px] tracking-[-0.4px] text-[#09090B]">Users</h2>
          <p class="text-[#71717A] text-sm font-normal leading-5">Current list of users from backend</p>
        </div>

        <div >
          <Table class="border-b">
            <TableHeader>
              <TableRow>
                <TableHead>Select</TableHead>
                <TableHead>User</TableHead>
                <TableHead>Status</TableHead>
                <TableHead>Date</TableHead>
                <TableHead></TableHead>
              </TableRow>
            </TableHeader>
            <TableBody>
              <TableRow v-for="user in filteredUsers" :key="user._id">
                <TableCell>
                  <Checkbox v-model="user.checked" />
                </TableCell>
                <TableCell >
                  <div class="font-medium ">{{ user.name }}</div>
                  <div class="text-sm text-gray-500">{{ user.email }}</div>
                </TableCell>
                <TableCell>{{ user.status }}</TableCell>
                <TableCell>{{ user.date }}</TableCell>
                <TableCell>
                  <!-- ✅ Dots Menu -->
                  <DropdownMenu>
                    <DropdownMenuTrigger as-child>
                      <Button variant="ghost" size="icon">
                        <MoreVertical class="h-5 w-5" />
                      </Button>
                    </DropdownMenuTrigger>
                    <DropdownMenuContent align="end">
                      <DropdownMenuItem @click="handleDelete(user._id)">
                        Delete
                      </DropdownMenuItem>
                    </DropdownMenuContent>
                  </DropdownMenu>
                </TableCell>
              </TableRow>
            </TableBody>
          </Table>
        </div>
      </div>

      <!-- ✅ Footer -->
      <div class="text-sm text-gray-500 mt-6">
        Showing {{ filteredUsers.length }} of {{ users.length }} users
      </div>

      <!-- ✅ Confirmation Dialog -->
      <Dialog v-model:open="showDialog">
        <DialogContent>
          <DialogHeader>
            <DialogTitle class="text-center">
              Confirm {{ selectedAction }}
            </DialogTitle>
          </DialogHeader>
          <p v-if="selectedAction === 'Delete'" class="text-center">
            Are you sure you want to Delete this user?
          </p>
          <p v-else class="text-center">
            Are you sure you want to mark selected users as {{ selectedAction }}?
          </p>
          <DialogFooter class="mt-4">
            <Button class="bg-black  w-full" @click="confirmAction">Confirm</Button>
          </DialogFooter>
        </DialogContent>
      </Dialog>
    </template>
  </div>
</template>

<style scoped>
/* ✅ Slide animation for alert */
.slide-fade-enter-active {
  transition: all 0.3s ease;
}
.slide-fade-leave-active {
  transition: all 0.5s ease;
}
.slide-fade-enter-from,
.slide-fade-leave-to {
  transform: translateX(100%);
  opacity: 0;
}
tbody tr td{
    padding-top: 20px;
    padding-bottom: 20px;
}
.sort{
        width: max-content;
}
</style>