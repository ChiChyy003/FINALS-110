<script setup>
import Checkbox from '@/Components/Checkbox.vue';
import InputError from '@/Components/InputError.vue';
import InputLabel from '@/Components/InputLabel.vue';
import PrimaryButton from '@/Components/PrimaryButton.vue';
import TextInput from '@/Components/TextInput.vue';
import { Head, Link, useForm } from '@inertiajs/vue3';

defineProps({
    canResetPassword: {
        type: Boolean,
    },
    status: {
        type: String,
    },
});

const form = useForm({
    email: '',
    password: '',
    remember: false,
});

const sanitizeInput = (value, allowOnly) => {
    const regex = new RegExp(allowOnly, 'g');
    return value.replace(regex, '');
};

const allowedEmailChars = '[^a-zA-Z0-9@._-]'; // Allow alphanumeric, @, ., -, _
const allowedPasswordChars = '[^a-zA-Z0-9]';  // Allow alphanumeric only

const submit = () => {
    form.post(route('login'), {
        onFinish: () => form.reset('password'),
    });
};
</script>

<template>
    <div class="min-h-screen flex items-center justify-center bg-gradient-to-r from-[#EDCFA9] via-[#D57149] to-[#AA4A30] dark:from-[#D57149] dark:to-[#AA4A30] py-12 px-4 sm:px-6 lg:px-8">
        <div class="max-w-md w-full space-y-8 bg-white p-8 rounded-xl shadow-2xl">
            <Head title="Log in" />

            <!-- LOG IN Header -->
            <h1 class="text-4xl font-bold text-center text-[#85311A]">
                LOG IN
            </h1>

            <!-- Status Message -->
            <div v-if="status" class="mb-4 text-sm font-medium text-green-700 dark:text-green-400 bg-green-100 dark:bg-green-900 p-4 rounded-md">
                {{ status }}
            </div>

            <!-- Login Form -->
            <form @submit.prevent="submit" class="mt-6 space-y-6">
                <!-- Email Input -->
                <div>
                    <InputLabel for="email" value="Email" class="sr-only" />
                    <TextInput
                        id="email"
                        type="text"
                        maxlength="50"
                        class="appearance-none rounded-md relative block w-full px-3 py-2 border border-gray-300 dark:border-gray-600 placeholder-gray-600 dark:placeholder-gray-400 text-black dark:text-black focus:outline-none focus:ring-[#D57149] focus:border-[#D57149] sm:text-sm"
                        v-model="form.email"
                        required
                        autofocus
                        autocomplete="username"
                        placeholder="Email address"
                        @input="form.email = sanitizeInput(form.email, allowedEmailChars)"
                    />
                    <InputError class="mt-2" :message="form.errors.email" />
                </div>

                <!-- Password Input -->
                <div>
                    <InputLabel for="password" value="Password" class="sr-only" />
                    <TextInput
                        id="password"
                        type="password"
                        maxlength="50"
                        class="appearance-none rounded-md relative block w-full px-3 py-2 border border-gray-300 dark:border-gray-600 placeholder-gray-600 dark:placeholder-gray-400 text-gray-900 dark:text-black focus:outline-none focus:ring-[#D57149] focus:border-[#D57149] sm:text-sm"
                        v-model="form.password"
                        required
                        autocomplete="current-password"
                        placeholder="Password"
                        @input="form.password = sanitizeInput(form.password, allowedPasswordChars)"
                    />
                    <InputError class="mt-2" :message="form.errors.password" />
                </div>

                <!-- Remember Me & Forgot Password -->
                <div class="flex items-center justify-between">
                    <div class="flex items-center">
                        <Checkbox name="remember" v-model:checked="form.remember" class="h-4 w-4 text-[#85311A] focus:ring-[#85311A] border-gray-500 rounded" />
                        <span class="ml-2 block text-sm text-[#85311A]">Remember me</span>
                    </div>
                    <div class="text-sm">
                        <Link
                            v-if="canResetPassword"
                            :href="route('password.request')"
                            class="font-medium text-[#D57149] hover:text-[#AA4A30] dark:text-[#85311A] dark:hover:text-[#AA4A30]"
                        >
                            Forgot your password?
                        </Link>
                    </div>
                </div>

                <!-- Submit Button -->
                <div>
                    <PrimaryButton
                        class="group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-[#D57149] hover:bg-[#AA4A30] focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-[#D57149]"
                        :class="{ 'opacity-25': form.processing }"
                        :disabled="form.processing"
                    >
                        <span class="absolute left-0 inset-y-0 flex items-center pl-3">
                            <svg class="h-5 w-5 text-[#D57149] group-hover:text-[#AA4A30]" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                                <path fill-rule="evenodd" d="M5 9V7a5 5 0 0110 0v2a2 2 0 012 2v5a2 2 0 01-2 2H5a2 2 0 01-2-2v-5a2-2 0 012-2zm8-2v2H7V7a3 3 0 016 0z" clip-rule="evenodd" />
                            </svg>
                        </span>
                        Log in
                    </PrimaryButton>
                </div>
            </form>
        </div>
    </div>
</template>
