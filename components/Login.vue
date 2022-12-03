<template>
  <div class="login-container">
    <section class="box">
      <div class="is-size-2 has-text-centered">{{ heading }}</div>
<!--      <b-checkbox v-model="hasError">Show errors</b-checkbox>-->

      <b-field label="Username"
               :type="{ 'is-danger': hasError }"
               :message="{ 'Username is not available': hasError }">
        <b-input v-model="username" placeholder="Tom Cruise" maxlength="30"></b-input>
      </b-field>

      <b-field label="Password"
               :type="{ 'is-danger': hasError }"
               :message="[
                { 'Password is too short': hasError },
                { 'Password must have at least 8 characters': hasError }
            ]">
        <b-input v-model="password" placeholder="sample@123" type="password" maxlength="30"></b-input>
      </b-field>
<!--      <div style="position: relative;width:100%;height:20%">-->
        <b-button type="is-success" outlined class="center-button" @click="sign_in()">Sign In</b-button>
<!--      </div>-->
    </section>
  </div>
</template>

<script>
export default {
  name: 'LoginComponent',
  data () {
    return {
      heading: 'Sign In Please',
      valid_creds: {
        verma123: 'test@123',
        nico123: 'password@123'
      },
      username: null,
      password: null,
      hasError: false,
      usernameErrors: []
    }
  },
  methods: {
    sign_in () {
      // this.$buefy.toast.open('Sign In Initiated with Username: ' + this.username + ', Password: ' + this.password)
      if ((this.password === this.valid_creds[this.username])) {
        console.log('event Emitted')
        this.$nuxt.$emit('LOGIN_SUCCESS')
      } else {
        this.$buefy.toast.open('Invalid Password')
      }
    }
  },
  computed: {
  },
  watch: {
    username (value) {
    }
  }
}
</script>

<style scoped>
.login-container {
  width: 60%;
}

.center-button {
  width:100%;
  position: relative;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  /*margin: auto;*/

}
</style>
