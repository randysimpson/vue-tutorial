<template>
  <div id="employee-form">
    <form @submit.prevent="handleSubmit" class="clr-form">
      <div class="clr-form-control">
        <label class="clr-control-label">Employee name</label>
        <div class="clr-control-container" :class="{ 'clr-error': submitting && invalidName }">
          <div class="clr-input-wrapper">
            <input
              ref="first"
              v-model="employee.name"
              type="text"
              class="clr-input"
              @focus="clearStatus"
              @keypress="clearStatus" />
          </div>
        </div>
      </div>
      <div class="clr-form-control">
        <label class="clr-control-label">Employee Email</label>
        <div class="clr-control-container" :class="{ 'clr-error': submitting && invalidEmail }">
          <div class="clr-input-wrapper">
            <input
              v-model="employee.email"
              type="text"
              class="clr-input"
              @focus="clearStatus" />
          </div>
        </div>
      </div>
      <button
        :disabled="isLoading"
        class="btn btn-outline">Add Employee</button>
      <div v-if="error && submitting" class="alert alert-danger" role="alert">
        <div class="alert-items">
          <div class="alert-item static">
            <div class="alert-icon-wrapper">
              <clr-icon class="alert-icon" shape="exclamation-circle"></clr-icon>
            </div>
            <span class="alert-text">❗Please fill out all required fields</span>
          </div>
        </div>
      </div>
      <div v-if="success && !isLoading" class="alert alert-success" role="alert">
        <div class="alert-items">
          <div class="alert-item static">
            <div class="alert-icon-wrapper">
              <clr-icon class="alert-icon" shape="check-circle"></clr-icon>
            </div>
            <span class="alert-text">✅ Employee successfully added</span>
          </div>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
  export default {
    name: 'employee-form',
    props: {
      isLoading: Boolean
    },
    data() {
      return {
        submitting: false,
        error: false,
        success: false,
        employee: {
          name: '',
          email: '',
        },
      }
    },
    methods: {
      handleSubmit() {
        this.submitting = true
        this.clearStatus()

        if (this.invalidName || this.invalidEmail) {
          this.error = true
          return
        }
        
        this.$emit('add:employee', this.employee);
        this.$refs.first.focus();
        this.employee = {
          name: '',
          email: '',
        }
        this.error = false
        this.success = true
        this.submitting = false
      },
      clearStatus() {
        this.success = false
        this.error = false
      }
    },
    computed: {
      invalidName() {
        return this.employee.name === ''
      },

      invalidEmail() {
        return this.employee.email === ''
      },
    },
  }
</script>

<style scoped>
  form {
    margin-bottom: 2rem;
  }
</style>