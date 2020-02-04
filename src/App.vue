<template>
  <div id="app" class="main-container">
    <div v-if="hasError" class="alert alert-app-level alert-danger" role="alert">
      <div class="alert-items">
        <div class="alert-item static">
          <div class="alert-icon-wrapper">
            <clr-icon class="alert-icon" shape="exclamation-circle"></clr-icon>
          </div>
          <div class="alert-text">
            {{ error }}
          </div>
          <div class="alert-actions">
            <button class="btn alert-action">Action</button>
          </div>
        </div>
      </div>
      <button type="button" class="close" aria-label="Close">
        <clr-icon aria-hidden="true" shape="close"></clr-icon>
      </button>
    </div>
    <header class="header-6">
      <div class="branding">
        <span class="title">Vue Tutorial</span>
      </div>
    </header>
    <div class="content-container">
      <div class="content-area">
        <div v-if="isLoading" class="progress loop"><progress></progress></div>
        <h1>Employees</h1>

        <employee-form
          @add:employee="addEmployee"
          v-bind:isLoading="isLoading" />
        <employee-table
          v-bind:employees="employees"
          v-bind:isLoading="isLoading"
          @delete:employee="deleteEmployee"
          @edit:employee="editEmployee" />
      </div>
      <nav class="sidenav">
        <section class="sidenav-content">
          <section class="nav-group collapsible">
            <input id="tabexample1" type="checkbox">
            <label for="tabexample1">Vue Tutorials</label>
            <ul class="nav-list">
              <li><a href="https://www.taniarascia.com/getting-started-with-vue/" target="_blank" rel="noopener">Getting Started with Vue - An Overview and Walkthrough Tutorial</a></li>
              <li><a href="https://vuex.vuejs.org/" target="_blank" rel="noopener">What is Vuex?</a></li>
            </ul>
          </section>
          <section class="nav-group collapsible">
            <input id="tabexample2" type="checkbox">
            <label for="tabexample2">CSS</label>
            <ul class="nav-list">
              <li><a href="https://clarity.design/" target="_blank" rel="noopener">Clarity Design</a></li>
            </ul>
          </section>
        </section>
      </nav>
    </div>
  </div>
</template>

<script>
import EmployeeTable from '@/components/EmployeeTable.vue';
import EmployeeForm from '@/components/EmployeeForm.vue';

export default {
  name: 'app',
  components: {
    EmployeeTable,
    EmployeeForm
  },
  data() {
    return {
      employees: [],
      error: '',
      isLoading: false
    }
  },
  mounted() {
    this.getEmployees()
  },
  computed: {
    hasError() {
      return this.error !== ''
    }
  },
  methods: {
    async getEmployees() {
      try {
        this.isLoading = true;
        const response = await fetch('https://jsonplaceholder.typicode.com/users')
        const data = await response.json()
        this.employees = data
        this.isLoading = false;
      } catch (error) {
        this.error = error;
        this.isLoading = false;
      }
    },
    async addEmployee(employee) {
      try {
        this.isLoading = true;
        const response = await fetch('https://jsonplaceholder.typicode.com/users', {
          method: 'POST',
          body: JSON.stringify(employee),
          headers: { 'Content-type': 'application/json; charset=UTF-8' },
        })
        const data = await response.json()
        this.employees = [...this.employees, data]
        this.isLoading = false;
      } catch (error) {
        this.error = 'Failed to create employee.';
        this.isLoading = false;
      }
    },
    async deleteEmployee(id) {
      try {
        this.isLoading = true;
        await fetch(`https://jsonplaceholder.typicode.com/users/${id}`, {
          method: "DELETE"
        });
        this.employees = this.employees.filter(employee => employee.id !== id);
        this.isLoading = false;
      } catch (error) {
        this.error = 'Failed to delete employee.';
        this.isLoading = false;
      }
    },
    async editEmployee(id, updatedEmployee) {
      try {
        this.isLoading = true;
        const response = await fetch(`https://jsonplaceholder.typicode.com/users/${id}`, {
          method: 'PUT',
          body: JSON.stringify(updatedEmployee),
          headers: { 'Content-type': 'application/json; charset=UTF-8' },
        })
        const data = await response.json()
        this.employees = this.employees.map(employee => (employee.id === id ? data : employee))
        this.isLoading = false;
      } catch (error) {
        this.error = 'Failed to update employee.';
        this.isLoading = false;
      }
    }
  }
}
</script>

<style>
</style>
