<template>
  <Head title="Dashboard" />

  <AuthenticatedLayout>
    <div class="min-h-screen bg-gradient-to-b from-[#EDCFA9] to-[#AA4A30] dark:from-[#D57149] dark:to-[#AA4A30] py-6">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <h3 class="text-3xl font-bold text-white pb-4 px-2 text-center">
          Ice Cream Inventory
        </h3>
        <div class="bg-white dark:bg-[#6F321F] rounded-lg shadow-xl overflow-hidden">
          <div class="overflow-x-auto">
            <table class="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
              <thead class="bg-gray-50 dark:bg-white">
                <tr>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium-bold text-[#6F321F] dark:text-[#6F321F] uppercase tracking-wider">
                    Icecream Name
                  </th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium-bold text-[#6F321F] dark:text-[#6F321F] uppercase tracking-wider">
                    Description
                  </th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium-bold text-[#6F321F] dark:text-[#6F321F] uppercase tracking-wider">
                    Manufactured Date
                  </th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium-bold text-[#6F321F] dark:text-[#6F321F] uppercase tracking-wider">
                    Price
                  </th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium-bold text-[#6F321F] dark:text-[#6F321F] uppercase tracking-wider">
                    Stock
                  </th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium-bold text-[#6F321F] dark:text-[#6F321F] uppercase tracking-wider">
                    Actions
                  </th>
                </tr>
              </thead>
              <tbody class="bg-white divide-y divide-gray-200 dark:divide-gray-700">
                <tr v-for="icecream in icecreams" :key="icecream.icecream_id" class="hover:bg-gray-50 dark:hover:bg-gray-300 transition-colors duration-200">
                  <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-[#6F321F] dark:text-[#6F321F]">
                    {{ icecream.icecream_name }}
                  </td>
                  <td class="px-6 py-4 text-sm text-gray-500 dark:text-[#6F321F]">
                    <div class="max-w-xs overflow-hidden overflow-ellipsis">
                      {{ icecream.description }}
                    </div>
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-[#6F321F]">
                    {{ formatDate(icecream.manufactured_date) }}
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-[#6F321F]">
                    ₱{{ formatPrice(icecream.price) }}
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-[#6F321F]">
                    {{ icecream.stock }}
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                    <button
                      @click="editIceCream(icecream)"
                      class="text-[#AA4A30] hover:text-[#D57149] dark:text-[#EB9D77] dark:hover:text-[#F2C6AD] mr-2 transition-colors duration-200"
                    >
                      Edit
                    </button>

                    <button
                      v-if="$page.props.auth.user.role_id === 1"
                      @click="editstocks(icecream)"
                      class="text- hover:text-[#361515] dark:text-[#EB9D77] dark:hover:text-[#361515] mr-2 transition-colors duration-200"
                    >
                      Admin-edit
                    </button>

                    <button
                      v-if="$page.props.auth.user.role_id === 1"
                      @click="deleteIceCream(icecream.icecream_id)"
                      class="text-red-600 hover:text-red-800 dark:text-red-400 dark:hover:text-red-300 transition-colors duration-200"
                    >
                      Delete
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
        <div class="mt-6 text-center mb-16">
          <button
            @click="showAddIceCreamModal = true"
            class="absolute -4 bottom-left-4 px-4 py-2 bg-gradient-to-r from-[#D57149] to-[#AA4A30] text-white text-sm font-bold rounded-lg shadow-lg hover:from-[#AA4A30] hover:to-[#D57149] transition-transform transform hover:scale-105 flex items-center"
          >
            Add New Ice Cream
          </button>
        </div>
      </div>
    </div>

    <!-- Add/Edit Ice Cream Modal -->
    <div
      v-if="showAddIceCreamModal"
      class="fixed z-10 inset-0 overflow-y-auto"
      aria-labelledby="modal-title"
      role="dialog"
      aria-modal="true"
    >
      <div
        class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0"
      >
        <div
          class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity"
          aria-hidden="true"
        ></div>
        <span
          class="hidden sm:inline-block sm:align-middle sm:h-screen"
          aria-hidden="true"
          >&#8203;</span
        >
        <div
          class="inline-block align-bottom bg-[#D57149] rounded-lg px-4 pt-5 pb-4 text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full sm:p-6"
        >
          <div>
            <div class="mt-3 text-center sm:mt-5">
              <h3
                class="text-lg leading-6 font-medium text-gray-900 dark:text-white"
                id="modal-title"
              >
                {{ editingIceCream ? "Edit Ice Cream" : "Add Ice Cream" }}
              </h3>
              <div class="mt-2">
                <input
                  v-model="currentIceCream.icecream_name"
                  type="text"
                  placeholder="Flavor"
                  @input="sanitizeInput($event, 'icecream_name', 50)"
                  class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-[#EB9D77] focus:border-[#EB9D77] sm:text-sm dark:bg-[#85311A] dark:border-gray-600 dark:text-white"
                />
                <textarea
                  v-model="currentIceCream.description"
                  placeholder="Description"
                  @input="sanitizeInput($event, 'description', 200)"
                  class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-[#EB9D77] focus:border-[#EB9D77] sm:text-sm dark:bg-[#85311A] dark:border-gray-600 dark:text-white"
                ></textarea>
                <input
                  v-model="currentIceCream.price"
                  type="number"
                  step="0.01"
                  placeholder="Price"
                  @input="sanitizeNumericInput($event, 'price', 10)"
                  class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-[#EB9D77] focus:border-[#EB9D77] sm:text-sm dark:bg-[#85311A] dark:border-gray-600 dark:text-white"
                />
                <input
                  v-model="currentIceCream.manufactured_date"
                  type="date"
                  placeholder="Manufactured Date"
                  class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-[#EB9D77] focus:border-[#EB9D77] sm:text-sm dark:bg-[#85311A] dark:border-gray-600 dark:text-white"
                />
                <div class="mt-4">
                  <label
                    for="image"
                    class="block text-sm font-medium text-gray-700 dark:text-gray-300"
                    >Upload Image</label
                  >
                  <input
                    type="file"
                    id="image"
                    @change="handleFileUpload"
                    accept="image/*"
                    class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-[#EB9D77] focus:border-[#EB9D77] sm:text-sm dark:bg-[#85311A] dark:border-gray-600 dark:text-white"
                  />
                  <p
                    v-if="currentIceCream.image_url"
                    class="mt-2 text-sm text-gray-500 dark:text-gray-400"
                  >
                    Current file: {{ currentIceCream.image_url }}
                  </p>
                </div>
              </div>
            </div>
          </div>
          <div
            class="mt-5 sm:mt-6 sm:grid sm:grid-cols-2 sm:gap-3 sm:grid-flow-row-dense"
          >
            <button
              @click="editingIceCream ? saveIceCream() : addIceCream()"
              type="button"
              class="w-full inline-flex justify-center rounded-md border border-transparent shadow-sm px-4 py-2 bg-[#AA4A30] text-base font-medium text-white hover:bg-[#D57149] focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-[#EB9D77] sm:col-start-2 sm:text-sm transition-colors duration-200"
            >
              {{ editingIceCream ? "Save" : "Add" }}
            </button>
            <button
              @click="cancelEdit"
              type="button"
              class="mt-3 w-full inline-flex justify-center rounded-md border border-gray-300 shadow-sm px-4 py-2 bg-white text-base font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-[#EB9D77] sm:mt-0 sm:col-start-1 sm:text-sm dark:bg-[#85311A] dark:text-white dark:hover:bg-[#AA4A30] transition-colors duration-200"
            >
              Cancel
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- AdminEdit Ice Cream Modal -->
    <div
      v-if="showAdminModal"
      class="fixed z-10 inset-0 overflow-y-auto"
      aria-labelledby="modal-title"
      role="dialog"
      aria-modal="true"
    >
      <div
        class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0"
      >
        <div
          class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity"
          aria-hidden="true"
        ></div>
        <span
          class="hidden sm:inline-block sm:align-middle sm:h-screen"
          aria-hidden="true"
          >&#8203;</span
        >
        <div
          class="inline-block align-bottom bg-[#D57149] rounded-lg px-4 pt-5 pb-4 text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full sm:p-6"
        >
          <div>
            <div class="mt-3 text-center sm:mt-5">
              <h3
                class="text-lg leading-6 font-medium text-gray-900 dark:text-white font-bold"
                id="modal-title"
              >
                Edit Stocks
              </h3>
              <div class="mt-2">
                <p class="text-md leading-6 font-medium text-gray-900 dark:text-white">
                  {{ currentIceCream.icecream_name }}
                </p>
                <input
                  v-model="currentIceCream.stock"
                  type="number"
                  step="1"
                  placeholder="Stocks"
                  @input="sanitizeNumericInput($event, 'stock', 5)"
                  class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-[#EB9D77] focus:border-[#EB9D77] sm:text-sm dark:bg-[#85311A] dark:border-gray-600 dark:text-white"
                />
              </div>
            </div>
          </div>
          <div
            class="mt-5 sm:mt-6 sm:grid sm:grid-cols-2 sm:gap-3 sm:grid-flow-row-dense"
          >
            <button
              @click="updatestocks()"
              type="button"
              class="w-full inline-flex justify-center rounded-md border border-transparent shadow-sm px-4 py-2 bg-[#AA4A30] text-base font-medium text-white hover:bg-[#D57149] focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-[#EB9D77] sm:col-start-2 sm:text-sm transition-colors duration-200"
            >
              Update
            </button>
            <button
              @click="showAdminModal = false"
              type="button"
              class="mt-3 w-full inline-flex justify-center rounded-md border border-gray-300 shadow-sm px-4 py-2 bg-white text-base font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-[#EB9D77] sm:mt-0 sm:col-start-1 sm:text-sm dark:bg-[#85311A] dark:text-white dark:hover:bg-[#AA4A30] transition-colors duration-200"
            >
              Cancel
            </button>
          </div>
        </div>
      </div>
    </div>
  </AuthenticatedLayout>
</template>

<script setup>
import { ref } from "vue";
import AuthenticatedLayout from "@/Layouts/AuthenticatedLayout.vue";
import { Head, router } from "@inertiajs/vue3";

const props = defineProps({
  icecreams: Array,
});

const showAddIceCreamModal = ref(false);
const showAdminModal = ref(false);
const editingIceCream = ref(null);
const currentIceCream = ref({
  icecream_name: "",
  description: "",
  price: 0,
  stock: 0,
  manufactured_date: "",
  image_url: null,
});

// Input sanitization functions
const sanitizeInput = (event, field, maxLength) => {
  let value = event.target.value;
  // Remove special characters except spaces and basic punctuation
  value = value.replace(/[^a-zA-Z0-9\s.,!?-]/g, '');
  // Trim and limit the length
  value = value.trim().slice(0, maxLength);
  currentIceCream.value[field] = value;
};

const sanitizeNumericInput = (event, field, maxLength) => {
  let value = event.target.value;
  // Remove non-numeric characters
  value = value.replace(/[^0-9.]/g, '');
  // Ensure only one decimal point
  const parts = value.split('.');
  if (parts.length > 2) {
    value = parts[0] + '.' + parts.slice(1).join('');
  }
  // Limit the length
  value = value.slice(0, maxLength);
  currentIceCream.value[field] = value;
};

const editIceCream = (iceCream) => {
  editingIceCream.value = iceCream;
  currentIceCream.value = { ...iceCream };
  showAddIceCreamModal.value = true;
};

const editstocks = (iceCream) => {
  editingIceCream.value = iceCream;
  currentIceCream.value = { ...iceCream };
  showAdminModal.value = true;
};

const handleFileUpload = (event) => {
  const file = event.target.files[0];
  if (file) {
    currentIceCream.value.image_url = file;
    console.log("File selected:", file);
  }
};

const saveIceCream = async () => {
  if (!currentIceCream.value.image_url) {
    alert("Please upload an image before saving.");
    return;
  }

  if (confirm("Are you sure you want to update this ice cream?")) {
    const formData = new FormData();
    formData.append("name", currentIceCream.value.icecream_name);
    formData.append("description", currentIceCream.value.description);
    formData.append("price", currentIceCream.value.price);
    formData.append("manufactured_date", currentIceCream.value.manufactured_date);
    formData.append("image_url", currentIceCream.value.image_url);

    try {
      await router.post(
        `/employee/update/${currentIceCream.value.icecream_id}`,
        formData,
        {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        }
      );
      alert("Ice cream updated successfully!");
    } catch (error) {
      console.error(
        "Failed to update ice cream:",
        error.response?.data || error.message
      );
      alert(
        "An error occurred while updating the ice cream. Please try again."
      );
    } finally {
      cancelEdit();
    }
  }
};

const addIceCream = async () => {
  if (confirm("Are you sure you want to add this ice cream?")) {
    const formData = new FormData();
    formData.append("name", currentIceCream.value.icecream_name);
    formData.append("description", currentIceCream.value.description);
    formData.append("price", currentIceCream.value.price);
    formData.append("manufactured_date", currentIceCream.value.manufactured_date);

    if (currentIceCream.value.image_url) {
      formData.append("image_url", currentIceCream.value.image_url);
    }

    try {
      await router.post(route("employee.add"), formData, {
        headers: {
          "Content-Type": "multipart/form-data",
        },
      });

      alert("Ice cream added successfully!");
    } catch (error) {
      console.error(
        "Failed to add ice cream:",
        error.response?.data || error.message
      );
      alert("An error occurred while adding the ice cream. Please try again.");
    } finally {
      cancelEdit();
    }
  }
};

const updatestocks = async () => {
  if (confirm("Are you sure you want to update this icecream stock?")) {
    try {
      await router.post(`/admin/update/${currentIceCream.value.icecream_id}`, {
        new_stock: currentIceCream.value.stock,
      });
      alert("Stocks updated successfully!");
    } catch (error) {
      console.error(
        "Failed to update stocks:",
        error.response?.data || error.message
      );
      alert("An error occurred while updating the stocks. Please try again.");
    } finally {
      cancelEdit();
    }
  }
};

const deleteIceCream = async (id) => {
  if (confirm("Are you sure you want to delete this icecream?")) {
    try {
      await router.post(route("employee.delete", { id }));
      alert("Deleted successfully!");
    } catch (error) {
      console.error("Failed to delete:", error.response?.data || error.message);
      alert("An error occurred while deleting. Please try again.");
    }
  }
};

const cancelEdit = () => {
  editingIceCream.value = null;
  currentIceCream.value = {
    icecream_name: "",
    description: "",
    price: 0,
    manufactured_date: "",
    imageFileName: "",
  };
  showAddIceCreamModal.value = false;
  showAdminModal.value = false;
};

const formatDate = (dateString) => {
  return new Date(dateString).toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  });
};

const formatPrice = (price) => {
  return parseFloat(price).toFixed(2);
};
</script>