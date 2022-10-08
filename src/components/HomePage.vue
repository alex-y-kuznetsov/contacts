<template>
    <main class="page-main">
        <h1>Your Contacts</h1>

        <UserContacts />
    </main>
</template>

<script>
import UserContacts from '@/views/UserContacts.vue';

export default {
    name: 'HomePage',
    components: {
        UserContacts
    }
}
</script>

<style scoped>
    .page-main {
        padding: 40px 0;
    }
</style>
