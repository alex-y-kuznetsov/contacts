<template>
    <h1>Welcome!</h1>

    <UserContacts />
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

</style>
