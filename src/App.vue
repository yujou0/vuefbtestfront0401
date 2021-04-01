<template>
  <div id="app">
    <div id="nav">
      <facebook-login class="button"
      appId="420905468863679"
      @login="onLogin"
      @logout="onLogout"
      @get-initial-status="getUserData"
      @sdk-loaded="sdkLoaded">
      </facebook-login>
      <div v-if="isConnected" class="information">
        <h1>My Facebook Information</h1>
        <div class="well">
          <div class="list-item">
            <img :src="picture">
          </div>
          <div class="list-item">
            <i>{{name}}</i>
          </div>
          <div class="list-item">
            <i>{{email}}</i>
          </div>
          <div class="list-item">
            <i>{{personalID}}</i>
          </div>
        </div>
      </div>
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
    <router-view/>
  </div>
</template>
<style lang="stylus">
#app
  display flex
  flex-direction column
  margin auto
  width 100vh
.information
  margin-top 100px
  margin auto
  display flex
  flex-direction column
.well
  background-color rgb(191, 238, 229)
  margin auto
  padding 50px 50px
  border-radius 20px
.login
  width 200px
  margin auto
.list-item:first-child
  margin 0

.list-item
  display flex
  align-items center
  margin-top 20px
.button
  margin auto
</style>
<script>
import facebookLogin from 'facebook-login-vuejs'
export default {
  name: 'App',
  computed: {
    user () {
      return this.$store.state.user
    }
  },
  data () {
    return {
      status: {
        fileUploading: false
      },
      // screenWidth: document.body.clientWidth,
      isShow: true,
      isConnected: false,
      name: '',
      email: '',
      personalID: '',
      picture: '',
      FB: undefined
    }
  },
  components: {
    facebookLogin
  },
  methods: {
    getUserData () {
      this.FB.api('/me', 'GET', { fields: 'id,name,email,picture' },
        user => {
          this.personalID = user.id
          this.email = user.email
          this.name = user.name
          this.picture = user.picture.data.url
        }
      )
    },
    sdkLoaded (payload) {
      this.isConnected = payload.isConnected
      this.FB = payload.FB
      if (this.isConnected) this.getUserData()
    },
    onLogin () {
      this.isConnected = true
      this.getUserData()
    },
    onLogout () {
      this.isConnected = false
    }
  }
}
</script>
