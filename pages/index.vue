<template>
  <div class="container">
    <div>
      <Logo v-if="userProfile.pictureUrl == ''" />
      <b-avatar
        v-else
        variant="info"
        size="6rem"
        :src="userProfile.pictureUrl"
      ></b-avatar>
      <h1 v-if="userProfile.displayName == ''" class="title">Antifake LIFF</h1>
      <h1 v-else class="title">{{ userProfile.displayName }}</h1>

      <b-card title="userId">
        <b-card-text>
          {{ userProfile.userId }}
        </b-card-text>
      </b-card>

      <b-card title="statusMessage">
        <b-card-text>
          {{ userProfile.statusMessage }}
        </b-card-text>
      </b-card>

      <b-card title="Email">
        <b-card-text>
          {{ email }}
        </b-card-text>
      </b-card>
      <!-- <button @click="locateMe">Get location</button> -->
      <div v-if="location">
        Your location data is {{ location.coords.latitude }},
        {{ location.coords.longitude }}
      </div>
      <div v-if="errorStr">
        {{ errorStr }}
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { LIFFErrorObject } from 'liff-type'

export default Vue.extend({
  data() {
    return {
      userProfile: {},
      email: '',
      location: null,
      gettingLocation: false,
      errorStr: '',
    }
  },
  created() {},
  mounted() {
    window.liff
      .init({
        liffId: '1654910665-6jAGm1AG',
      })
      .then(() => {
        if (!window.liff.isLoggedIn()) {
          window.liff.login()
        } else {
          window.liff.getProfile().then((profile: any) => {
            this.userProfile = profile
            window.liff.getDecodedIDToken().then((o: any) => {
              this.email = o.email
            })
          })
        }
      })
      .catch((err: LIFFErrorObject) => {
        // eslint-disable-next-line no-console
        console.log(err)
      })

    setTimeout(async () => await this.locateMe(), 2000)
  },
  methods: {
    // eslint-disable-next-line require-await
    async getLocation() {
      return new Promise((resolve: any, reject: any) => {
        if (!('geolocation' in window.navigator)) {
          this.errorStr = 'Geolocation is not available.'
          reject(new Error('Geolocation is not available.'))
        }

        const options = {
          enableHighAccuracy: false,
          timeout: 5000,
          maximumAge: 0,
        } as any

        navigator.geolocation.getCurrentPosition(
          (pos) => {
            resolve(pos)
          },
          (err) => {
            reject(err)
          },
          options
        )
      })
    },
    async locateMe() {
      this.gettingLocation = true
      try {
        this.gettingLocation = false
        this.location = (await this.getLocation()) as any
      } catch (e) {
        this.gettingLocation = false
        this.errorStr = e.message
      }
    },
  },
})
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
