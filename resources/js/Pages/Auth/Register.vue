<script setup>
// import GuestLayout from '@/Layouts/GuestLayout.vue';
import InputError from '@/Components/InputError.vue';
import InputLabel from '@/Components/InputLabel.vue';
import PrimaryButton from '@/Components/PrimaryButton.vue';
import TextInput from '@/Components/TextInput.vue';
import { Head, Link, useForm } from '@inertiajs/vue3';
import { UserPlus } from 'lucide-vue-next';

const form = useForm({
    name: '',
    email: '',
    password: '',
    password_confirmation: '',
});

const submit = () => {
    form.post(route('register'), {
        onFinish: () => form.reset('password', 'password_confirmation'),
    });
};
</script>

<template>
    <GuestLayout>
        <Head title="Register" />

        <div class="min-h-screen flex items-center justify-center bg-gradient-to-r from-[#EDCFA9] via-[#D57149] to-[#AA4A30] dark:from-[#D57149] dark:to-[#AA4A30] py-12 px-4 sm:px-6 lg:px-8">
            <div class="max-w-md w-full space-y-8 bg-white dark:bg-white p-8 rounded-xl shadow-2xl">
                <div>
                    <h2 class="mt-6 text-center text-3xl font-extrabold text-[#85311A]">
                        Create your account
                    </h2>
                    <p class="mt-2 text-center text-sm text-gray-600 dark:text-[#85311A]">
                        Join us and start your journey
                    </p>
                </div>

                <form @submit.prevent="submit" class="mt-8 space-y-6">
                    <div class="rounded-md shadow-sm -space-y-px">
                        <div>
                            <InputLabel for="name" value="Name" class="sr-only" />
                            <TextInput
                                id="name"
                                type="text"
                                class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 dark:border-gray-600 placeholder-gray-500 text-black dark:text-black rounded-t-md focus:outline-none focus:ring-[#D57149] focus:border-[#D57149] focus:z-10 sm:text-sm"
                                v-model="form.name"
                                required
                                autofocus
                                autocomplete="name"
                                placeholder="Your Name"
                            />
                            <InputError class="mt-2" :message="form.errors.name" />
                        </div>
                        <br>
                        <div>
                            <InputLabel for="email" value="Email" class="sr-only" />
                            <TextInput
                                id="email"
                                type="email"
                                class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 dark:border-gray-600 placeholder-gray-500 text-black dark:text-black focus:outline-none focus:ring-[#D57149] focus:border-[#D57149] focus:z-10 sm:text-sm"
                                v-model="form.email"
                                required
                                autocomplete="username"
                                placeholder="Your Email"
                            />
                            <InputError class="mt-2" :message="form.errors.email" />
                        </div>
                        <br>
                        <div>
                            <InputLabel for="password" value="Password" class="sr-only" />
                            <TextInput
                                id="password"
                                type="password"
                                class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 dark:border-gray-600 placeholder-gray-500 text-black dark:text-black focus:outline-none focus:ring-[#D57149] focus:border-[#D57149] focus:z-10 sm:text-sm"
                                v-model="form.password"
                                required
                                autocomplete="new-password"
                                placeholder="Your Password"
                            />
                            <InputError class="mt-2" :message="form.errors.password" />
                        </div>
                        <br>
                        <div>
                            <InputLabel for="password_confirmation" value="Confirm Password" class="sr-only" />
                            <TextInput
                                id="password_confirmation"
                                type="password"
                                class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 dark:border-gray-600 placeholder-gray-500 text-black dark:text-black rounded-b-md focus:outline-none focus:ring-[#D57149] focus:border-[#D57149] focus:z-10 sm:text-sm"
                                v-model="form.password_confirmation"
                                required
                                autocomplete="new-password"
                                placeholder="Confirm Password"
                            />
                            <InputError class="mt-2" :message="form.errors.password_confirmation" />
                        </div>
                    </div>

                    <div>
                        <PrimaryButton
                            class="group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-[#D57149] hover:bg-[#AA4A30] focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-[#D57149]"
                            :class="{ 'opacity-25': form.processing }"
                            :disabled="form.processing"
                        >
                            <span class="absolute left-0 inset-y-0 flex items-center pl-3">
                                <UserPlus class="h-5 w-5 text-[#D57149] group-hover:text-[#AA4A30]" aria-hidden="true" />
                            </span>
                            Register
                        </PrimaryButton>
                    </div>
                </form>

                <div class="text-sm text-center">
                    <Link
                        :href="route('login')"
                        class="font-medium text-[#D57149] hover:text-[#AA4A30] dark:text-[#85311A] dark:hover:text-[#AA4A30]"
                    >
                        Already have an account? Log in
                    </Link>
                </div>
            </div>
        </div>
    </GuestLayout>
</template>
