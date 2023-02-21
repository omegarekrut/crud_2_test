<template>
    <table class="min-w-full divide-y divide-gray-200">
        <thead class="bg-gray-50">
            <tr>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                    Name
                </th>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                    Email
                </th>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                    Actions
                </th>
            </tr>
        </thead>
        <tbody class="bg-white divide-y divide-gray-200">
            <tr v-for="user in users" :key="user.id">
                <td class="px-6 py-4 whitespace-nowrap">
                    <div class="text-sm font-medium text-gray-900">{{ user.name }}</div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                    <div class="text-sm text-gray-500">{{ user.email }}</div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                    <button type="button" @click="showPayments(user.id)"
                        class="inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                        Payments
                    </button>
                    <button type="button" @click="deleteUser(user.id)"
                        class="inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-red-600 hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500">
                        Delete
                    </button>
                    <button type="button" @click="showEditModal(user.id)"
                        class="inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-blue-600 hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500">
                        Edit
                    </button>
                </td>
            </tr>
            <tr v-if="selectedUserId !== null">
                <td colspan="3">
                    <payment-table :payments="payments" />
                </td>
            </tr>
        </tbody>
    </table>
    <div v-if="isEditModalOpen && editUser"
        class="fixed z-50 inset-0 flex items-center justify-center overflow-auto bg-gray-500 bg-opacity-50">
        <div class="bg-white rounded-lg max-w-lg mx-auto p-6">
            <div class="mb-4">
                <label for="edit-name" class="block text-gray-700 font-bold mb-2">Name:</label>
                <input id="edit-name" name="name" v-model="editUser.name" type="text">
            </div>
            <div class="mb-4">
                <label for="edit-email" class="block text-gray-700 font-bold mb-2">Email:</label>
                <input id="edit-email" name="email" v-model="editUser.email" type="email">
            </div>
            <div class="flex justify-end">
                <button
                    class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
                    @click="editUserDetails">Save</button>
            </div>
        </div>
    </div>
</template>


<script>
import { ref, reactive } from 'vue';
import PaymentTable from '@/Components/PaymentTable.vue';

export default {

    components: {
        PaymentTable,
    },
    props: {
        users: {
            type: Array,
            required: true,
        },
    },
    emits: [
        'refresh',
    ],
    data() {
        return {
            selectedUserId: null,
            payments: [],
            isEditModalOpen: false,
            editUser: null,
        };
    },
    methods: {
        refreshList() {
            this.$emit('refresh');
        },
        showPayments(userId) {
            if (userId === this.selectedUserId) {
                this.selectedUserId = null;
                this.payments = [];
            } else {
                fetch(`/api/users/${userId}/payments`)
                    .then((response) => response.json())
                    .then((data) => {
                        this.selectedUserId = userId;
                        this.payments = data;
                    });
            }
        },
        showEditModal(userId) {
            this.editUser = { id: userId };
            this.isEditModalOpen = true;
        },
        async editUserDetails() {
            const { id, name, email } = this.editUser;

            const token = document.head.querySelector('meta[name="csrf-token"]').content;

            await fetch(`/api/users/${id}`, {
                method: 'PUT',
                headers: {
                    'Content-Type': 'application/json',
                    'X-CSRF-TOKEN': token,
                },
                body: JSON.stringify({
                    name,
                    email
                }),
            });

            this.isEditModalOpen = false;
            this.refreshList();
        },
        async deleteUser(userId) {
            const token = document.querySelector('meta[name="csrf-token"]').getAttribute('content');
            await fetch(`/api/users/${userId}`, {
                method: 'DELETE',
                headers: {
                    'X-CSRF-TOKEN': token,
                },
            });
            this.refreshList();
        }
    }
};
</script>


