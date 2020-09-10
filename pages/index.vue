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
      <h1 class="title" v-if="userProfile.displayName == ''">Antifake LIFF</h1>
      <h1 class="title" v-else>{{ userProfile.displayName }}</h1>

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

      <div v-if="location">
        Your location data is {{ location.coords.latitude }},
        {{ location.coords.longitude }}
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
      location: '',
      gettingLocation: false,
      errorStr: '',
    }
  },
  created() {
    if (!('geolocation' in window.navigator)) {
      this.errorStr = 'Geolocation is not available.'
      return
    }

    this.gettingLocation = true

    window.navigator.geolocation.getCurrentPosition(
      (pos: any) => {
        this.gettingLocation = false
        this.location = pos
      },
      (err) => {
        this.gettingLocation = false
        this.errorStr = err.message
      }
    )
  },
  mounted() {
    liff
      .init({
        liffId: '1654910665-6jAGm1AG',
      })
      .then(() => {
        if (!liff.isLoggedIn()) {
          liff.login()
        } else {
          liff.getProfile().then((profile) => {
            console.log(profile)
            this.userProfile = profile
            liff.getDecodedIDToken().then((o) => {
              this.email = o.email
            })
          })
        }
      })
      .catch((err: LIFFErrorObject) => {
        console.log(err)
      })
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
