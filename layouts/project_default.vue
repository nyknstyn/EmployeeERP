<template>
  <div>
    <GenericHeader></GenericHeader>
<!--    <GenericHeader></GenericHeader>-->
<!--    If logged in -->
    <section>
      <div class="columns">
        <div class="column">
        </div>
        <div class="column">
          <LoginComponent v-if="!logged_in"></LoginComponent>
        </div>
      </div>
      <DataList v-if="logged_in"></DataList>
    </section>

  </div>

</template>
<script>
import GenericHeader from '@/components/GenericHeader'
import LoginComponent from '@/components/Login'
import DataList from '@/components/DataList'
export default {
  name: 'project_default',
  components: { GenericHeader, LoginComponent, DataList },
  data () {
    return {
      logged_in: false
    }
  },
  methods: {
    success_login () {
      this.logged_in = true
      this.$buefy.toast.open('Logged In')
    },
    success_logout () {
      this.logged_in = false
      this.$buefy.toast.open('Logged Out')
    }
  },
  mounted () {
    this.$nuxt.$on('LOGIN_SUCCESS', this.success_login)
    this.$nuxt.$on('LOGOUT_SUCCESS', this.success_logout)
  }
}
</script>

<style scoped>

</style>
