<template>
  <div class="min-h-screen bg-[#EDCFA9]">
    <Head title="Admin Dashboard" />

    <AuthenticatedLayout>
      <div class="min-h-screen bg-gradient-to-b from-[#EDCFA9] to-[#AA4A30] dark:from-[#D57149] dark:to-[#AA4A30] py-9">
        <h1 class="text-4xl font-bold text-white mb-6 px-4 sm:px-20">Admin Dashboard</h1>

        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-6 mx-4 sm:mx-20">
          <!-- Statistics -->
          <div class="bg-white p-6 rounded-lg shadow-md">
            <h2 class="text-xl font-semibold text-[#AA4A30] mb-4">Statistics</h2>
            <div class="grid grid-cols-2 gap-4">
              <div class="bg-[#EDCFA9] p-4 rounded-lg">
                <p class="text-sm text-[#AA4A30] mb-1">Total Ice Creams</p>
                <p class="text-2xl font-bold text-[#D57149]">{{ summary.total_icecream }}</p>
              </div>
              <div class="bg-[#EDCFA9] p-4 rounded-lg">
                <p class="text-sm text-[#AA4A30] mb-1">Total Users</p>
                <p class="text-2xl font-bold text-[#D57149]">{{ summary.total_users }}</p>
              </div>
            </div>
          </div>

          <!-- Activity Logs -->
          <div class="bg-white p-6 rounded-lg shadow-md relative lg:col-span-2">
            <h2 class="text-xl font-semibold text-[#AA4A30] mb-4">
              Activity Logs
            </h2>
            <button
              @click="refreshView"
              class="absolute top-4 right-4 px-4 py-2 bg-gradient-to-r from-[#D57149] to-[#AA4A30] text-white text-sm font-bold rounded-lg shadow-lg hover:from-[#AA4A30] hover:to-[#D57149] transition-transform transform hover:scale-105 flex items-center"
            >
              Refresh
            </button>

            <div class="max-h-48 overflow-y-auto">
              <ul class="space-y-2">
                <li
                  v-for="log in sortedLogs"
                  :key="log.user_id"
                  class="border-b border-[#AA4A30] pb-2 hover:bg-[#EDCFA9]"
                >
                  <span class="font-semibold text-[#AA4A30]">{{ sanitizeInput(log.username) }}</span>
                  <span class="text-[#D57149]">
                    has made an {{ sanitizeInput(log.action) }}
                  </span>
                  <span class="text-[#D57149]">
                    in {{ sanitizeInput(log.table_name) }} table
                  </span>
                  <span class="text-[#AA4A30] text-sm">
                    at {{ formatDate(log.time) }}
                  </span>
                </li>
              </ul>
            </div>
          </div>
        </div>

        <!-- User Management -->
        <div class="bg-white p-6 rounded-lg shadow-md mb-6 mx-4 sm:mx-20">
          <h2 class="text-xl font-semibold text-[#AA4A30] mb-4">
            User Management
          </h2>
          <div class="overflow-x-auto">
            <table class="w-full">
              <thead>
                <tr class="bg-[#EDCFA9]">
                  <th class="p-2 text-left text-[#AA4A30]">Name</th>
                  <th class="p-2 text-left text-[#AA4A30]">Email</th>
                  <th class="p-2 text-left text-[#AA4A30]">Role</th>
                  <th class="p-2 text-left text-[#AA4A30]">Actions</th>
                </tr>
              </thead>
              <tbody>
                <tr
                  v-for="user in users"
                  :key="user.id"
                  class="border-b border-[#AA4A30] hover:bg-[#EDCFA9]"
                >
                  <td class="p-2 text-[#D57149]">{{ sanitizeInput(user.name) }}</td>
                  <td class="p-2 text-[#D57149]">{{ sanitizeInput(user.email) }}</td>
                  <td class="p-2 text-[#D57149]">{{ getRole(user.role_id) }}</td>
                  <td class="p-2 flex flex-wrap gap-2">
                    <!-- Show buttons only if the user is not an admin -->
                    <div v-if="user.role_id !== 1">
                      <button
                        @click="editUserRole(user)"
                        class="bg-[#D57149] hover:bg-[#AA4A30] text-white px-2 py-1 rounded mr-2 transition duration-300 ease-in-out"
                      >
                        Edit Role
                      </button>
                      <button
                        @click="deleteUser(user.id)"
                        class="bg-[#AA4A30] hover:bg-[#D57149] text-white px-2 py-1 rounded transition duration-300 ease-in-out"
                      >
                        Delete
                      </button>
                    </div>
                    <!-- If admin, show no actions -->
                    <div v-else class="text-[#AA4A30] text-sm">
                      No actions available
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>

        <!-- Modal -->
        <div
          v-if="addModal"
          class="fixed inset-0 bg-[#AA4A30] bg-opacity-50 flex items-center justify-center p-4 sm:p-8"
        >
          <div class="bg-[#EDCFA9] p-6 sm:p-8 rounded-lg shadow-xl max-w-md w-full">
            <h3 class="text-lg sm:text-xl font-semibold mb-4 text-[#AA4A30]">
              Editing User: {{ sanitizeInput(editingUser?.name) || "Unknown" }}
            </h3>
            <form @submit.prevent="submitUser" class="space-y-4">
              <div>
                <label
                  for="name"
                  class="block text-sm font-medium text-[#AA4A30]"
                  >Name</label
                >
                <input
                  type="text"
                  id="name"
                  v-model="userForm.name"
                  disabled
                  class="mt-1 block w-full rounded-md border-[#D57149] shadow-sm bg-white text-[#AA4A30]"
                />
              </div>
              <div>
                <label
                  for="email"
                  class="block text-sm font-medium text-[#AA4A30]"
                  >Email</label
                >
                <input
                  type="email"
                  id="email"
                  v-model="userForm.email"
                  disabled
                  class="mt-1 block w-full rounded-md border-[#D57149] shadow-sm bg-white text-[#AA4A30]"
                />
              </div>
              <div>
                <label
                  for="role"
                  class="block text-sm font-medium text-[#AA4A30]"
                  >Role</label
                >
                <select
                  id="role"
                  v-model="userForm.role_id"
                  required
                  class="mt-1 block w-full rounded-md border-[#D57149] shadow-sm focus:border-[#AA4A30] focus:ring-[#D57149] bg-white text-[#AA4A30]"
                >
                  <option
                    v-for="role in props.roles"
                    :key="role.id"
                    :value="role.id"
                  >
                    {{ sanitizeInput(role.role_user) }}
                  </option>
                </select>
              </div>
              <div class="flex justify-between items-center mt-4">
                <button
                  @click="closeModal"
                  type="button"
                  class="text-[#AA4A30] hover:text-[#D57149]"
                >
                  Cancel
                </button>
                <button
                  type="submit"
                  class="bg-[#D57149] text-white hover:bg-[#AA4A30] px-4 py-2 rounded-lg"
                >
                  Submit
                </button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </AuthenticatedLayout>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import { Head, router } from "@inertiajs/vue3";
import AuthenticatedLayout from "@/Layouts/AuthenticatedLayout.vue";

const props = defineProps({
  users: Array,
  roles: Array,
  logs: Array,
  summary: {
    type: Object,
    required: true,
  },
});

// User Form for Editing Role
const users = ref([...props.users]);
const addModal = ref(false);
const editingUser = ref(null); 

// Sort logs in descending order
const sortedLogs = computed(() => {
  return [...props.logs].sort((a, b) => new Date(b.time) - new Date(a.time));
});

// To get the role based on user id
const getRole = (roleId) => {
  const role = props.roles.find((role) => role.id === roleId);
  return role ? sanitizeInput(role.role_user) : "Unknown";
};

const userForm = ref({
  name: "",
  email: "",
  role_id: null,
});

const editUserRole = (user) => {
  // Open the modal and set the form and editing user data
  if (getRole(user.role_id) === "admin") {
    alert("Admin roles cannot be edited or demoted.");
    return;
  }
  
  addModal.value = true;
  editingUser.value = user;
  userForm.value = {
    id: user.id,
    name: sanitizeInput(user.name),
    email: sanitizeInput(user.email),
    role_id: user.role_id,
  };
};

const submitUser = () => {
  if (
    editingUser.value &&
    getRole(editingUser.value.role_id) === "admin" &&
    userForm.value.role_id !== editingUser.value.role_id
  ) {
    alert("Once promoted to Admin, users cannot be demoted.");
    return;
  }

  // Proceed with the update
  router.put(
    route("admin.updateUserRole", { userId: userForm.value.id }),
    { role_id: userForm.value.role_id },
    {
      onSuccess: () => {
        const userIndex = users.value.findIndex((u) => u.id === userForm.value.id);
        if (userIndex !== -1) {
          users.value[userIndex].role_id = userForm.value.role_id;
        }
        closeModal();
        alert("User role updated successfully!");
      },
      onError: (error) => {
        console.error("Error updating user role:", error);
        alert("There was an error updating the user role.");
      },
    }
  );
};

const refreshView = async () => {
  try {
    // Send POST request via Inertia
    router.post(route("admin.refresh"));
  } catch (err) {
    // Handle error response
    error.value = "Failed to refresh the materialized view.";
    console.error(err);
  }
};

const formatDate = (dateString) => {
  const options = {
    year: "numeric",
    month: "short",
    day: "numeric",
    hour: "2-digit",
    minute: "2-digit",
  };
  return new Date(dateString).toLocaleDateString(undefined, options);
};

const deleteUser = (userId) => {
  const user = users.value.find((u) => u.id === userId);
  if (user && user.role_id === 1) {
    alert("You cannot delete another admin.");
    return;
  }

  if (confirm("Are you sure you want to delete this user?")) {
    router.delete(route("admin.deleteUser", { user: userId }), {
      onSuccess: () => {
        users.value = users.value.filter((user) => user.id !== userId);
        alert("User deleted successfully.");
      },
      onError: (error) => {
        alert(
          error.response?.data?.message ||
            "There was an error deleting the user."
        );
      },
    });
  }
};

const closeModal = () => {
  addModal.value = false;
  editingUser.value = null; // Reset the editing user
  userForm.value = { id: null, name: "", email: "", role_id: null }; // Reset form
};

// Input sanitization function
const sanitizeInput = (input) => {
  if (typeof input !== 'string') return input;
  return input
    .replace(/[<>&"']/g, (match) => {
      switch (match) {
        case '<': return '&lt;';
        case '>': return '&gt;';
        case '&': return '&amp;';
        case '"': return '&quot;';
        case "'": return '&#x27;';
        default: return match;
      }
    })
    .slice(0, 100); // Limit to 100 characters
};
</script>