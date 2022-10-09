<template>
    <div>You have {{ contactsServer.length }} contacts</div>
    <div class="contacts-controls" ref="contactsControls">
        <button 
            class="contacts-all"
            @click="toggleAllContacts"
        >{{ isAllShown ? 'hide' : 'show' }} all</button>
        <div class="search-divider">or</div>
        <div class="search-block">
            <input 
                type="text" 
                class="search-input" 
                placeholder="search by name, username, phone or email"
                v-model="textFilter"
                @input="findContact"
            >
        </div>
        <div class="alphanet-block">
            <button class="alphabet-item" 
                v-for="item, itemKey in computedAlphabet" 
                :key="itemKey"
                @click="goToLetter(item)"
            >{{ item }}</button>
        </div>
    </div>
    <div class="contacts-cover">
        <article 
            class="contacts-item"
            v-for="item, itemKey in contactsShown"
            :key="itemKey"
            :ref="item.name[0].toLowerCase()"
            :style="{ scrollMarginTop: controlsHeight + 'px' }"
        >
            <div class="contacts-primary">
                <div class="contacts-inner-cover">
                    <h1 class="contacts-name">{{ item.name }}</h1>
                    <h2 class="contacts-username"><i>aka </i>{{ item.username }}</h2>
                    <b class="contacts-company">{{ item.company.name }}</b>
                </div>
            </div>
            
            <div class="contacts-secondary">
                <a class="contacts-reach contacts-phone" 
                    :href="`tel:${item.phone}`"
                >{{ item.phone }}</a>
                <a class="contacts-reach contacts-email" 
                    :href="`mailto:${item.email}`"
                >{{ item.email }}</a>
                <a class="contacts-reach contacts-website"
                    :href="`https://${item.website}`"
                    target="_blank" 
                >{{ item.website }}</a>
            </div>
            <div class="contacts-misc">
                <div class="contacts-inner-cover">
                    <div class="contacts-misc-item">{{ item.address.suite }}</div>
                    <div class="contacts-misc-item">{{ item.address.street }}</div>
                    <div class="contacts-misc-item">{{ item.address.city }}</div>
                    <div class="contacts-misc-item">{{ item.address.zipcode }}</div>
                </div>
            </div>
        </article>
        <div v-if="textFilter && !contactsShown.length">No contacts matching search query</div>
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
            isAlphabet: false,
            contactsServer: [],
            contactsShown: [],
            textFilter: '',
            controlsHeight: null
        }
    },
    methods: {
        init() {
            fetch(`${constants.API_URL}/users`)
            .then((response) => (response.json()))
            .then((json) => { 
                this.contactsServer = json.sort((a, b) => a.name.localeCompare(b.name)) 
            })
            .finally(this.isInit = true);
        },
        getAlphabet(array) {
            this.isAlphabet = false;
            let alphabet = [];
            array.forEach(item => {
                let letter = item.name[0].toLowerCase();
                if(alphabet.indexOf(letter) === -1) {
                    alphabet.push(letter);
                }
            })
            this.isAlphabet = true;
            return alphabet;
        },
        goToLetter(letter) {
            const [el] = this.$refs[letter];
            if (el) {
                el.scrollIntoView({ behavior: "smooth" });
            }
        },
        toggleAllContacts() {
            this.textFilter = '';
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
        },
        getControlsHeight() {
            this.controlsHeight = this.$refs.contactsControls.offsetHeight;
        }
    },
    computed: {
        computedAlphabet() {
            return this.contactsShown.length ? this.getAlphabet(this.contactsShown) : []
        }
    },
    watch: {
        isAlphabet() {
            this.getControlsHeight();
        }
    },
    mounted() {
        window.addEventListener('resize', this.getControlsHeight)
        this.init();
    },
    beforeUnmount() {
        window.removeEventListener('resize', this.getControlsHeight)
    }
}

</script>

<styles scoped lang="less">
    .contacts-controls {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        align-items: center;
        gap: 20px;
        margin: 20px -1px 0 -1px;
        position: sticky;
        top: -1px;
        padding: 20px;
        background-color: var(--color-bg);
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

    .search-input {
        padding: 8px 12px;
        border-radius: 5px;
        border: none;
        min-width: 290px;
        box-sizing: border-box;
    }

    .alphanet-block {
        width: 100%;
        display: flex;
        flex-wrap: wrap;
        gap: 4px;
        justify-content: center;
    }

    .alphabet-item {
        border: 1px solid var(--color-green);
        border-radius: 2px;
        background-color: transparent;
        color: var(--text-color);
        cursor: pointer;
        padding: 2px;
        width:24px;
        height: 24px;

        &:hover {
            background-color: var(--color-green);
            color: var(--color-white);
            transition: all var(--main-transition);
        }
    }

    .contacts-cover {
        margin-top: 20px;
    }

    .contacts-item {
        border: 1px solid var(--color-yellow);
        background-color: var(--color-dark-green);
        border-radius: 5px;
        text-align: left;
        padding: 12px 8px;
        display: grid;
        gap: 8px;
        grid-template-columns: 1fr 1fr 1fr;

        & + & {
            margin-top: 12px;
        }
    }

    .contacts-primary,
    .contacts-misc {
        display: flex;
        gap: 6px;
        
        &::before {
            width: 24px;
            height: 24px;
            display: block;
            content: '';
            background-repeat: no-repeat;
            background-size: contain;
        }
    }

    .contacts-primary {
        &::before {
            background-image: url('@/assets/icons/user.png');
        }
    }

    .contacts-name {
        margin: 0;
        font-size: 16px;
    }

    .contacts-username,
    .contacts-company {
        margin: 0;
        font-size: 14px;
        color: var(--color-text-light);
    }

    .contacts-secondary {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
    }

    .contacts-reach {
        color: var(--color-text);
        text-decoration: none;
        font-size: 12px;
        display: flex;
        gap: 6px;
        align-items: center;

        &::before {
            width: 16px;
            height: 16px;
            display: block;
            content: '';
            background-repeat: no-repeat;
            background-size: contain;
        }
        
        &:hover {
            color: var(--color-yellow);
        }
    }

    .contacts-phone {
        &::before {
            background-image: url('@/assets/icons/phone.png');
        }
    }

    .contacts-email {
        &::before {
            background-image: url('@/assets/icons/email.png');
        }
    }

    .contacts-website {
        &::before {
            background-image: url('@/assets/icons/website.png');
        }
    }

    .contacts-misc {
        &::before {
            background-image: url('@/assets/icons/location.png');
        }
    }

    .contacts-misc-item {
        font-size: 12px;
    }

    @media (max-width: 576px) {
        .contacts-controls {
            flex-direction: column;
            gap: 12px;
        }

        .contacts-all {
            min-width: 290px;
        }

        .search-divider {
            display: none;
        }

        .contacts-item {
            grid-template-columns: 2fr 1fr;
            grid-template-rows: 1fr 1fr;
        }

        .contacts-primary {
            grid-column: span 2;
        }

        .contacts-secondary {
            justify-content: start;
            gap: 4px;
        }

        .contacts-reach {
            font-size: 14px;
        }

        .contacts-primary,
        .contacts-misc {
            &::before {
                display: none;
            }
        }
    }
</styles>