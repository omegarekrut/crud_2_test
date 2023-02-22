<template>
    <table class="table-auto">
        <thead>
            <tr>
                <th class="px-4 py-2">ID</th>
                <th class="px-4 py-2">ID_user</th>
                <th class="px-4 py-2">Amount</th>
                <th class="px-4 py-2">Description</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="payment in payments" :key="payment.id" class="bg-gray-100">
                <td class="border px-4 py-2">{{ payment.id }}</td>
                <td class="border px-4 py-2">{{ payment.user_id }}</td>
                <td class="border px-4 py-2">{{ payment.amount }}</td>
                <td class="border px-4 py-2">{{ payment.description }}</td>
            </tr>
        </tbody>
    </table>
</template>


<script>
export default {
    props: {
        payments: Array,
    },
};
</script>
