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
import axios from 'axios';

export default {
  data() {
    return {
      items: [],
      name: '',
      editing: false,
      currentItem: { id: null, name: '' },
      binId: '66595a50acd3cb34a8506ea0', 
      apiKey: '$2a$10$PWAbVgQGMZVH7nGI.4fe9e8wE.QGkVXzv0xZFjbw72MGksaXG3ax6' 
    };
  },
  mounted() {
    this.fetchItems();
  },
  methods: {
    async fetchItems() {
      try {
        const response = await axios.get(`https://api.jsonbin.io/v3/b/${this.binId}/latest`, {
          headers: {
            'X-Master-Key': this.apiKey,
          },
        });
        this.items = response.data.record.items;
      } catch (error) {
        console.error('Error fetching items:', error);
      }
    },
    async addItem() {
      try {
        const newItem = { id: Date.now(), name: this.name };
        this.items.push(newItem);
        await this.updateItems();
        this.name = '';
      } catch (error) {
        console.error('Error adding item:', error);
      }
    },
    async deleteItem(id) {
      try {
        this.items = this.items.filter(item => item.id !== id);
        await this.updateItems();
      } catch (error) {
        console.error('Error deleting item:', error);
      }
    },
    startEditing(item) {
      this.editing = true;
      this.currentItem = { ...item };
    },
    async editItem() {
      try {
        const index = this.items.findIndex(item => item.id === this.currentItem.id);
        if (index !== -1) {
          this.items.splice(index, 1, this.currentItem);
          await this.updateItems();
          this.editing = false;
          this.currentItem = { id: null, name: '' };
        }
      } catch (error) {
        console.error('Error editing item:', error);
      }
    },
    async updateItems() {
      try {
        await axios.put(`https://api.jsonbin.io/v3/b/${this.binId}`, { items: this.items }, {
          headers: {
            'Content-Type': 'application/json',
            'X-Master-Key': this.apiKey,
          },
        });
      } catch (error) {
        console.error('Error updating items:', error);
      }
    },
    updateName(value) {
      if (this.editing) {
        this.currentItem.name = value;
      } else {
        this.name = value;
      }
    },
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