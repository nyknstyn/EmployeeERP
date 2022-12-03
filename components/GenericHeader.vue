<template>
  <b-navbar>
    <template #brand>
<!--      <b-navbar-item tag="router-link" :to="{ path: '/' }">-->
<!--        <img-->
<!--          src="~/assets/unknown.png"-->
<!--          style="height: 100%;width:100%"-->
<!--          alt="Election Tool"-->
<!--        >-->
<!--      </b-navbar-item>-->
      <b-navbar-item>
        Employee ERP
      </b-navbar-item>
    </template>
    <template #end>
      <b-navbar-item v-if="!logged_in" tag="p">
        <p>Does't have an account?</p>
      </b-navbar-item>
      <b-navbar-item>
        <b-button @click="log_out" v-if="logged_in" type="is-ghost">Log Out</b-button>
        <b-button v-else type="is-ghost">Sign Up</b-button>
      </b-navbar-item>
    </template>
    <!--    <template #start>-->
    <!--      <b-navbar-item href="#">-->
    <!--        Home-->
    <!--      </b-navbar-item>-->
    <!--      <b-navbar-item href="#">-->
    <!--        Documentation-->
    <!--      </b-navbar-item>-->
    <!--      <b-navbar-dropdown label="Info">-->
    <!--        <b-navbar-item href="#">-->
    <!--          About-->
    <!--        </b-navbar-item>-->
    <!--        <b-navbar-item href="#">-->
    <!--          Contact-->
    <!--        </b-navbar-item>-->
    <!--      </b-navbar-dropdown>-->
    <!--    </template>-->
  </b-navbar>
</template>

<script>
export default {
  name: 'GenericHeader',
  data () {
    return {
      logged_in: false
    }
  },
  methods: {
    log_out () {
      // this.$buefy.toast.open('Sign In Initiated with Username: ' + this.username + ', Password: ' + this.password)
      this.logged_in = false
      this.$buefy.toast.open('Logged Out')
      this.$nuxt.$emit('LOGOUT_SUCCESS')
    }
  },
  mounted () {
    this.$nuxt.$on('LOGIN_SUCCESS', () => {
      this.logged_in = true
    })
  }
}
</script>

<style scoped>

</style>
