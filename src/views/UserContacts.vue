<template>
    <div>You have {{ contactsServer.length }} contacts</div>
    <div class="contacts-controls">
        <button 
            class="contacts-all"
            @click="toggleAllContacts"
        >{{ isAllShown ? 'hide' : 'show' }} all</button>
        <div>or find one</div>
        <div class="search-block">
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
            isAllShown: false,
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
        toggleAllContacts() {
            if (!this.isAllShown) {
                this.contactsShown = this.contactsServer;
                this.isAllShown = true;
            } else {
                this.contactsShown = [];
                this.isAllShown = false;
            }
            
        },
        findContact() {
            if (!this.textFilter) {
                this.contactsShown = this.isAllShown ? this.contactsServer : [];
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
        justify-content: center;
        align-items: center;
        gap: 30px;
        margin-top: 40px;
    }

    .contacts-all {
        min-width: 74px;
        border: none;
        background-color: var(--color-green);
        border-radius: 5px;
        color: var(--color-white);
        padding: 8px 12px;
        cursor: pointer;
        transition: box-shadow var(--main-transition);

        &:hover {
            box-shadow: 0px 5px 6px 2px var(--color-grey);
            transition: box-shadow var(--main-transition);
        }

        &:active {
            box-shadow: none;
            transition: box-shadow var(--main-transition);
        }
    }

    .contacts-name {
        margin: 0;
        font-size: 16px;
    }

    .search-input {
        padding: 8px 12px;
        border-radius: 5px;
        border: none;
        min-width: 264px;
    }

    .contacts-cover {
        margin-top: 40px;
    }

</styles>