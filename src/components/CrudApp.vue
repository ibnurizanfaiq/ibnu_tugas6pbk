<template>
  <div class="container">
    <h1>TUGAS 6</h1>
    <form @submit.prevent="editing ? editItem() : addItem()" class="form-container">
      <input
        type="text"
        placeholder="Enter name"
        :value="editing ? currentItem.name : name"
        @input="updateName($event.target.value)"
        class="input-field"
      />
      <button type="submit" class="submit-button">{{ editing ? 'Edit' : 'Add' }}</button>
    </form>
    <table class="data-table">
      <thead>
        <tr>
          <th>Nama</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in items" :key="item.id">
          <td>{{ item.name }}</td>
          <td>
            <button class="edit-button" @click="startEditing(item)">Edit</button>
            <button class="delete-button" @click="deleteItem(item.id)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from '../api/axios';

export default {
  data() {
    return {
      items: [],
      name: '',
      editing: false,
      currentItem: { id: null, name: '' },
    };
  },
  mounted() {
    this.fetchItems();
  },
  methods: {
    async fetchItems() {
      const response = await axios.get('/items');
      this.items = response.data;
    },
    async addItem() {
      const response = await axios.post('/items', { name: this.name });
      this.items.push(response.data);
      this.name = '';
    },
    async deleteItem(id) {
      await axios.delete(`/items/${id}`);
      this.items = this.items.filter(item => item.id !== id);
    },
    startEditing(item) {
      this.editing = true;
      this.currentItem = { ...item };
    },
    async editItem() {
      const response = await axios.put(`/items/${this.currentItem.id}`, this.currentItem);
      this.items = this.items.map(item => (item.id === this.currentItem.id ? response.data : item));
      this.editing = false;
      this.currentItem = { id: null, name: '' };
    },
    updateName(value) {
      if (this.editing) {
        this.currentItem.name = value;
      } else {
        this.name = value;
      }
    }
  },
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 50px;
}

h1 {
  margin-bottom: 20px;
}

.form-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 20px;
}

.input-field {
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  width: 200px;
}

.submit-button {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  background-color: #007bff;
  color: white;
  cursor: pointer;
}

.submit-button:hover {
  background-color: #0056b3;
}

.data-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

.data-table th,
.data-table td {
  padding: 10px;
  border: 1px solid #ddd;
  text-align: left;
}

.edit-button,
.delete-button {
  padding: 5px 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.edit-button {
  background-color: #ffc107;
  color: white;
}

.edit-button:hover {
  background-color: #e0a800;
}

.delete-button {
  background-color: #dc3545;
  color: white;
}

.delete-button:hover {
  background-color: #c82333;
}
</style>