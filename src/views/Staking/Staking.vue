<template>
    <div class="view-staking">
        <template v-if="!currentAccount">
            <f-message type="error" with-icon>Bad wallet</f-message>
        </template>
        <template v-else>
            <address-info-box />

            <main class="main hide">
                <account-stake />
                <!--                <router-view></router-view>-->
            </main>
            <v-row justify="center"> Staking will be available soon </v-row>
        </template>
    </div>
</template>

<script>
import FMessage from '@/components/core/FMessage/FMessage.vue';
import AddressInfoBox from '@/components/AddressInfoBox/AddressInfoBox.vue';
import { mapGetters } from 'vuex';
import {
    DEACTIVATE_ACTIVE_ACCOUNT,
    SET_ACTIVE_ACCOUNT_ADDRESS,
    SET_ACTIVE_ACCOUNT_BY_ADDRESS,
} from '@/store/mutations.type.js';
import AccountStake from '@/views/AccountStake.vue';
import { eventBusMixin } from '@/mixins/event-bus.js';

export default {
    name: 'Staking',

    components: { AccountStake, AddressInfoBox, FMessage },

    mixins: [eventBusMixin],

    computed: {
        ...mapGetters(['currentAccount']),
    },

    watch: {
        $route(_value) {
            const { address } = _value.params;

            if (address && address.toLowerCase() !== this.currentAccount.address.toLowerCase()) {
                this.setActiveAccount(address);
                this._eventBus.emit('account-picked', address);
            }
        },
    },

    created() {
        this.setActiveAccount(this.$route.params.address);
    },

    methods: {
        /**
         * @param {string} _address
         */
        setActiveAccount(_address) {
            this.$store.commit(DEACTIVATE_ACTIVE_ACCOUNT);
            this.$store.commit(SET_ACTIVE_ACCOUNT_BY_ADDRESS, _address);
            this.$store.commit(SET_ACTIVE_ACCOUNT_ADDRESS, _address);
        },
    },
};
</script>
