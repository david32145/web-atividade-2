<template>
  <div class="container mt-4">
    <div class="text-center">
      <img src="./assets/fastCloud.png" alt="Logo" />
    </div>

    <form class="mt-3" v-on:submit="handleSubmit">
      <div class="form-group">
        <label for="name">Name</label>
        <input v-model="name" type="text" class="form-control" id="name" aria-describedby="nameHelp" />
        <small id="nameHelp" class="form-text text-muted">Enter your name.</small>
      </div>
      <div class="form-group">
        <label for="email">Email</label>
        <input v-model="email" type="email" class="form-control" id="email" aria-describedby="emailHelp" />
        <small id="emailHelp" class="form-text text-muted">Enter your email</small>
      </div>
      <div class="form-group">
        <label for="dateOfBirth">Date of birth</label>
        <input v-model="birthOfDate" type="date" class="form-control" id="dateOfBirth" />
      </div>
      <div class="form-group">
        <div class="row">
          <div class="col-2">
            <label>Services</label>
          </div>
          <div class="col-10">
            <div class="row">
              <div class="col-6" v-for="service in services" :key="service.id">
                <div class="form-group form-check">
                  <input :value="service.id" v-model="checkeboxIds" type="checkbox" class="form-check-input" :id="'checkId' + service.id" />
                  <label class="form-check-label" :for="'checkId' + service.id">{{service.title}}</label>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="row">
        <div class="col-2"></div>
        <div class="col-3">
          <button type="reset" class="btn btn-block btn-secondary">Clear</button>
        </div>
        <div class="col-3">
          <button type="submit" class="btn btn-block btn-primary">Add</button>
        </div>
      </div>
    </form>
    <table class="table table-striped mt-5">
      <thead>
        <tr>
          <th scope="col">Name</th>
          <th scope="col">Email</th>
          <th scope="col">Date of Birth</th>
          <th scope="col">Services</th>
          <th sorted="col">Total</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(data, index) in tableData" :key="index">
          <th style="width: 14%">{{data.name}}</th>
          <td style="width: 16.66%">{{data.email}}</td>
          <td style="width: 12%">{{data.birthOfDate}}</td>
          <td v-html="data.servicesBody"></td>
          <td style="width: 16.66%">{{data.total}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import services from "./services";

const LOCAL_STORAGE_KEY = "@app/data"

export default {
  name: "App",
  data: function () {
    return {
      services: services,
      checkeboxIds: [],
      name: '',
      email: '',
      birthOfDate: '',
      tableData: []
    };
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault()
      if(!this.name || this.name < 3){
        alert('Name is invalid')
        return
      }
      if(!this.email || this.email < 3){
        alert('Email is invalid')
        return
      }
      const currentYear = new Date().getFullYear()
      const inputYear = this.birthOfDate && this.birthOfDate.split("-")[0]

      if(!this.birthOfDate || parseInt(inputYear, 10) + 18 > currentYear) {
        alert('Birth date is invalid')
        return
      }

      const selectedServices = services.filter(service => this.checkeboxIds.includes(service.id));
      if(!selectedServices.length) {
        alert('Select at least service')
      }

      const totalValue = selectedServices.reduce((acc, s) => acc + s.pricePerHour, 0);
      const data = {
        name: this.name,
        email: this.email,
        birthOfDate: this.birthOfDate,
        servicesBody: selectedServices.map(s => s.title).join('<br>'),
        total: `$ ${totalValue.toFixed(2)} per hour`
      }

      this.name = ''
      this.email = ''
      this.birthOfDate = ''
      this.checkeboxIds = []

      this.tableData.push(data)
    }
  },
  watch: {
    tableData(newTableData) {
      localStorage.setItem(LOCAL_STORAGE_KEY, JSON.stringify(newTableData));
    }
  },
  mounted() {
    const tableDataAsJson = localStorage.getItem(LOCAL_STORAGE_KEY)
    if(tableDataAsJson) {
      this.tableData = JSON.parse(tableDataAsJson)
    }
  }
};
</script>

<style>

</style>