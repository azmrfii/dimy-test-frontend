<template>
  <div class="container mt-4">
    <div class="col-3 offset-9 text-right my-3">
      <button v-show="!editIndex" @click="add" class="btn btn-sm btn-secondary">Add item</button>
    </div>
    <table class="table table-bordered mt-4">
      <thead class="thead-light">
        <tr>
          <th width="10%">Product Name</th>
          <th width="10%">Price</th>
          <th width="10%">Qty</th>
          <th width="10%">Total</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in items" :key="index">
          <td>{{ index + 1 }}</td>
          <td>
            <span v-if="editIndex !== index">{{ item.name }}</span>
            <span v-if="editIndex === index">
              <input class="form-control form-control-sm" v-model="item.name">
            </span>
          </td>
          <td>
            <span v-if="editIndex !== index">{{ item.price }}</span>
            <span v-if="editIndex === index">
              <input class="form-control form-control-sm" type="number" v-model.number="item.price">
            </span>
          </td>
          <td>
            <span v-if="editIndex !== index">{{ item.qty }}</span>
            <span v-if="editIndex === index">
              <input class="form-control form-control-sm" type="number" v-model.number="item.qty">
            </span>
          </td>
          <td><div class="text-right">{{ subtotal(item) || money }}</div></td>
          <td>
            <span v-if="editIndex !== index">
              <button @click="edit(item, index)" class="btn btn-sm btn-outline-secondary mr-2">Edit</button>
              <button @click="remove(item, index)" class="btn btn-sm btn-outline-secondary mr-2">Remove</button>
            </span>
            <span v-else>
              <button class="btn btn-sm btn-outline-secondary mr-2" @click="cancel(item)">Cancel</button>
              <button class="btn btn-sm btn-outline-secondary mr-2" @click="save(item)">Save</button>
            </span>
          </td>
        </tr>
      </tbody>
    </table>

    <div class="col-3 offset-9">

      <div class="input-group input-group-sm mb-3">
        <div class="input-group-prepend">
          <span class="input-group-text">Sub total</span>
        </div>
        <input class="form-control form-control-sm text-right" disabled :value="this.allSubTotal || money">
      </div>

    </div>

  </div>
</template>

<script>
export default {

  name: 'app',

  filters: {
    money: (value) => new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR' }).format(value)
  },

  data() {
    return {
      editIndex: null,
      originalData: null,
      items: [
        { name: 'Nabati', price: 2500, qty: 1},
      ]
    }
  },

  methods: {

    add() {
      this.originalData = null
      this.items.push({ name: '', price: 0, qty: 1 })
      this.editIndex = this.items.length - 1
    },

    edit(item, index) {
      this.originalData = Object.assign({}, item)
      this.editIndex = index
    },

    cancel(item) {
      this.editIndex = null

      if (!this.originalData) {
        this.items.splice(this.items.indexOf(item), 1)
        return
      }

      Object.assign(item, this.originalData)
      this.originalData = null
    },

    remove(item, index) {
      this.items.splice(index, 1)
    },

    save(item) {
      this.originalData = null
      this.editIndex = null
    },

    subtotal(item) {
      return (item.qty * item.price)
    }

  },

  computed: {

    allSubTotal() {
      return this.items
        .map(item => this.subtotal(item))
        .reduce((a, b) => a + b, 0)
    },

  },

}
</script>

<style>
  input[type="number"] {
    text-align: right;
  }
</style>