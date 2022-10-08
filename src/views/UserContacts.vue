<template>
    <div>You have {{ contactsServer.length }} contacts</div>
    <div class="contacts-controls">
        <button 
            class="contacts-all"
            @click="showAllContacts"
        >show all</button>
        <div>or</div>
        <div class="search-block">
            <span>find one</span>
            <input 
                type="text" 
                class="search-input" 
                placeholder="by name, username, phone number or email"
                v-model="textFilter"
                @input="findContact"
            >
        </div>
    </div>
    <div class="contacts-cover">
        <article 
            class="contacts-item"
            v-for="item in contactsShown"
            :key="item.id"
        >
            <h1 class="contacts-name">{{item.name}}</h1>
        </article>
    </div>
</template>

<script>
import constants from '@/static/constants.js';

export default {
    name: 'UserContacts',
    data() {
        return {
            isInit: false,
            contactsServer: [],
            contactsShown: [],
            textFilter: ''
        }
    },
    methods: {
        init() {
            fetch(`${constants.API_URL}/users`)
            .then((response) => (response.json()))
            .then((json) => this.contactsServer = json)
            .finally(this.isInit = true);
        },
        showAllContacts() {
            this.contactsShown = this.contactsServer;
        },
        findContact() {
            if (!this.textFilter) {
                this.contactsShown = [];
                return;
            }

            this.contactsShown = this.contactsServer.filter((item) =>
                item.name.toLowerCase().startsWith(this.textFilter.toLowerCase()) ||
                item.username.toLowerCase().startsWith(this.textFilter.toLowerCase()) ||
                item.phone.toLowerCase().startsWith(this.textFilter.toLowerCase()) ||
                item.email.toLowerCase().startsWith(this.textFilter.toLowerCase())
            );  
        }
    },
    mounted() {
        this.init();
    }
}

</script>

<styles scoped lang="less">
    .contacts-controls {
        display: flex;
    }
</styles>