<template>
  <div>
    <Head title="Dashboard" />

    <AuthenticatedLayout>
      <template #header>
        <h2 class="font-semibold text-xl text-gray-800 leading-tight">Dashboard</h2>
      </template>

      <div class="py-12">
        <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
          <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
            <div class="p-6 text-gray-900" v-if="usersLoaded">
              <user-table :users="users" @refresh="fetchUsers" />
            </div>
            <div v-else>
              Loading...
            </div>
          </div>
        </div>
      </div>
    </AuthenticatedLayout>
  </div>
</template>
<script>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head, usePage } from '@inertiajs/vue3';
import UserTable from '@/Components/UserTable.vue';
import { ref } from 'vue';

export default {
    components: {
        Head,
        AuthenticatedLayout,
        UserTable,
    },
    data() {
        return {
            users: [],
            usersLoaded: false,
        }
    },
    created() {
        this.fetchUsers();
    },
    methods: {
        async fetchUsers() {
            this.usersLoaded = false;
            const response = await fetch('/api/users', {
                headers: {
                    'Accept': 'application/json'
                },
            });

            if (response.ok) {
                this.users = await response.json();
            }

            this.usersLoaded = true;
        }

    },
}
</script>
