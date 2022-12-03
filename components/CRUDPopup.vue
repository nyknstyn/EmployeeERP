<template>
  <form action="">
    <div class="modal-card" style="width: auto">
      <header class="modal-card-head">
        <p class="modal-card-title">{{ label }}</p>
        <button
          type="button"
          class="delete"
          @click="$emit('close')"/>
      </header>
      <section class="modal-card-body">
        <b-field label="Name">
          <b-input
            v-model="updatedName"
            type="name"
            :value="name"
            required
            placeholder="Department Name"
            required>
          </b-input>
        </b-field>

        <b-field label="Capacity">
          <b-input
            type="capacity"
            v-model="updatedCapacity"
            :value="capacity"
            required
            placeholder="Capacity"
            required>
          </b-input>
        </b-field>

      </section>
      <footer class="modal-card-foot">
<!--        <b-button-->
<!--          label="Close"-->
<!--          @click="$emit('close')" />-->
        <b-button
          :label="label"
          :loading="working"
          type="is-primary"
          :disabled="isDisabled"
          @click="update()"
        />
      </footer>
    </div>
  </form>
</template>

<script>
export default {
  name: 'CRUDPopup',
  props: ['id', 'name', 'capacity', 'label', 'working'],
  data() {
    return {
      updatedName: this.name,
      updatedCapacity: this.capacity
    }
  },
  methods: {
    update() {
      this.$nuxt.$emit('EVENT_'+ this.label, {"id": this.id, "name":this.updatedName, "capacity":this.updatedCapacity})
    }
  },
  computed: {
    isDisabled () {
      return !this.updatedName || !this.updatedCapacity
    }
  }
}
</script>

<style scoped>

</style>
