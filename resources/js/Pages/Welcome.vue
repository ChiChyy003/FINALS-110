<script setup>
import { ref } from 'vue';
import { Head, Link } from '@inertiajs/vue3';
import { IceCream, ChartBar, Users, ArrowRight } from 'lucide-vue-next';

const props = defineProps({
    canLogin: Boolean,
    canRegister: Boolean,
    laravelVersion: String,
    phpVersion: String,
});

const features = ref([ 
    { 
        title: 'Flavor Management', 
        description: 'Easily add, edit, and track your delicious ice cream flavors.',
        icon: IceCream 
    },
    { 
        title: 'Real-time Analytics', 
        description: 'Get instant insights into your most popular flavors and stock levels.',
        icon: ChartBar 
    },
    { 
        title: 'User Roles', 
        description: 'Manage permissions for admins, staff, and customers effortlessly.',
        icon: Users 
    },
]);

function handleImageError() {
    document.getElementById('hero-image')?.classList.add('hidden');
}
</script>

<template>
    <div class="min-h-screen bg-gradient-to-b from-[#EDCFA9] to-[#AA4A30] dark:from-[#D57149] dark:to-[#AA4A30]">
        <Head title="Welcome to ScoopTrack" />

        <div class="flex flex-col min-h-screen bg-orange">
            <header class="py-6 px-8">
                <nav class="flex justify-between items-center">
                    <div class="text-3xl font-extrabold text-white">🍦 ScoopTrack</div>
                    <div v-if="canLogin" class="space-x-6">
                        <Link
                            v-if="$page.props.auth.user"
                            :href="route('customer.users')"
                            class="text-white hover:text-gray-200"
                        >
                            Dashboard
                        </Link>
                        <template v-else>
                            <Link
                                :href="route('login')"
                                class="text-white hover:text-gray-200"
                            >
                                Log in
                            </Link>
                            <Link
                                v-if="canRegister"
                                :href="route('register')"
                                class="text-white hover:text-gray-200"
                            >
                                Register
                            </Link>
                        </template>
                    </div>
                </nav>
            </header>

            <main class="flex-grow flex flex-col items-center justify-center p-6 text-center">
                <h1 class="text-5xl font-extrabold text-white mb-8">
                    Welcome to ScoopTrack
                </h1>
                <p class="text-xl text-white mb-12 max-w-3xl">
                    The ultimate Ice Cream Inventory Management System. Streamline your ice cream business and keep your customers coming back for more!
                </p>

                <img
                    id="hero-image"
                    src="/placeholder.svg?height=400&width=600"
                    alt="Ice Cream Shop"
                    class="w-full max-w-2xl rounded-xl shadow-2xl mb-12"
                    @error="handleImageError"
                />

                <div class="grid md:grid-cols-3 gap-12 mb-12">
                    <div
                        v-for="feature in features"
                        :key="feature.title"
                        class="bg-white dark:bg-[#AA4A30] rounded-lg p-8 shadow-xl hover:shadow-2xl transition-all ease-in-out duration-300"
                    >
                        <component :is="feature.icon" class="w-14 h-14 text-[#D57149] mb-4 mx-auto" />
                        <h2 class="text-2xl font-semibold text-[#AA4A30] dark:text-[#AA4A30] mb-4">
                            {{ feature.title }}
                        </h2>
                        <p class="text-[#85311A] dark:text-[#85311A]">{{ feature.description }}</p>
                    </div>
                </div>

                <Link
                    :href="route('register')"
                    class="inline-flex items-center px-8 py-4 text-lg font-medium text-white bg-[#D57149] rounded-full shadow-lg hover:bg-[#AA4A30] focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-[#D57149]"
                >
                    Get Started
                    <ArrowRight class="ml-2 -mr-1 w-6 h-6" />
                </Link>
            </main>

            <footer class="py-6 text-center text-sm text-white opacity-75">
                ScoopTrack v1.0 | Laravel v{{ laravelVersion }} (PHP v{{ phpVersion }})
            </footer>
        </div>
    </div>
</template>

<style scoped>
/* Additional custom styles for better appearance */
body {
    background-color: #fff;
}

.text-white {
    color: #ffffff !important;
}

.bg-white {
    background-color: #fff !important;
}

</style>
