<template>
  <div>
    <v-app-bar
      app
      flat
      color="blue"
      dark
      v-bind="$attrs"
    >
      <!-- drawer trigger -->
      <v-app-bar-nav-icon
        v-if="$vuetify.breakpoint.xsOnly"
        @click.stop="drawer = !drawer"
      />

      <!-- nav branding -->
      <v-toolbar-title class="pl-0">
        <h3 class="font-weight-bold">
          KnakYak <v-icon class="pl-1 pb-2">
            mdi-chat
          </v-icon>
        </h3>
      </v-toolbar-title>

      <v-spacer />

      <!-- user display name -->
      <v-toolbar-title>
        <h4
          class="font-weight-bold pb-1"
          v-text="userData.displayName"
        />
      </v-toolbar-title>
    </v-app-bar>

    <!-- application side navigation -->
    <v-navigation-drawer
      v-model="drawer"
      app
      :permanent="!$vuetify.breakpoint.xsOnly"
    >
      <!-- channel panel -->
      <v-list>
        <v-list-item>
          <h3 class="pt-sm-3 pb-sm-7">
            Channels
          </h3>
        </v-list-item>

        <!-- channels -->
        <v-list-item
          v-for="(data, index) in $store.state.channels"
          :key="index"
          link
          @click="activeChannelHandler(data)"
        >
          <v-list-item-title class="py-sm-4">
            <v-icon
              small
              color="blue"
              class="pr-3 pb-1"
            >
              mdi-pound
            </v-icon> {{ data.name }}
          </v-list-item-title>
        </v-list-item>
      </v-list>

      <!-- side drawer action buttons -->
      <template v-slot:append>
        <div class="pa-2">
          <!-- new channel field -->
          <v-text-field
            v-model="newChannelName"
            clearable
            label="new channel name"
            required
          />

          <!-- add new channel button -->
          <Btn
            color="primary"
            class="mb-5"
            block
            @click="newChannelHandler"
          >
            Add Channel +
          </Btn>

          <!-- logout button -->
          <Btn
            color="red"
            outlined
            block
            @click="logoutUser"
          >
            Logout
          </Btn>
        </div>
      </template>
    </v-navigation-drawer>

    <slot />
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import ChannelService from '@/api/services/ChannelService'
import Btn from '@/components/elements/Btn'

export default {
  name: 'AppScreen',

  components: {
    Btn
  },

  data () {
    return {
      drawer: null,
      newChannelName: ''
    }
  },

  computed: {
    ...mapGetters(['userData'])
  },

  methods: {
    ...mapActions(['logoutUser']),

    /**
     * Push a new channel to the Firestore DB.
     *
     * @returns {void}
     */
    newChannelHandler () {
      ChannelService.newChannel(this.newChannelName)
      this.newChannelName = ''
    },

    /**
     * Close nav drawer and sets the active channel by name.
     *
     * @returns {void}
     */
    activeChannelHandler (activeChannel) {
      this.drawer = !this.drawer
      ChannelService.setActiveChannel(activeChannel)
    }
  }
}
</script>
