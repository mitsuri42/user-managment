<template>
  <div>
    <h1>Список користувачів</h1>

    <div class="search-container">
      <input type="text" v-model="searchText" placeholder="Введіть ім'я або параметри для пошуку" />
    </div>

    <form @submit.prevent="addUser" class="user-form">
      <label for="name">Ім'я:</label>
      <input type="text" id="name" v-model="newUser.name" placeholder="Ім'я Фамілія" required />

      <label for="email">Електронна пошта:</label>
      <input type="email" id="email" v-model="newUser.email" placeholder="example@gmail.com" required />

      <button type="submit">Додати користувача</button>
    </form>

    <div class="container">
      <ul class="user-list">
        <li v-for="user in filteredUsers" :key="user.id" class="user-item" @click="handleUserClick(user, $event)">
          <img :src="user.avatar" :alt="`${user.first_name} ${user.last_name}`" class="user-avatar" />
          <div class="user-info">
            <h3>{{ user.first_name }} {{ user.last_name }}</h3>
            <p>{{ user.email }}</p>
          </div>
          <button @click="deleteUser(user.id)" class="delete-button">Видалити</button>
        </li>
      </ul>
    </div>

    <modal :show="showModal" :title="selectedUserDetails.first_name" @close="closeModal">
      <div class="larger-modal-content">
        <img :src="selectedUserDetails.avatar" :alt="`${selectedUserDetails.first_name} ${selectedUserDetails.last_name}`"
          class="user-avatar" />

        <div class="modal-details">
          <p><strong>Email:</strong> {{ selectedUserDetails.email }}</p>
          <label for="address"><strong>Адреса проживання:</strong></label>
          <input type="text" id="address" class="modal-input" v-model="selectedUserDetails.address" placeholder="Kingston, New York 12401" />

          <label for="phone"><strong>Номер телефону:</strong></label>
          <input type="text" id="phone" class="modal-input" v-model="selectedUserDetails.phone" placeholder="(800) 555‑0175" />

          <button @click="saveDetails" class="save-button">Зберегти</button>
        </div>
      </div>
    </modal>
  </div>
</template>

<script>
import Modal from './components/Modal.vue';

export default {
  data() {
    return {
      users: [],
      newUser: {
        name: '',
        email: '',
      },
      selectedUserDetails: {},
      showModal: false,
      searchText: '',
    };
  },
  components: {
    Modal,
  },
  mounted() {
    this.fetchUsers();
  },
  computed: {
    filteredUsers() {
      return this.users.filter(user => {
        const searchTerms = this.searchText.toLowerCase();
        return (
          (user.first_name && user.first_name.toLowerCase().includes(searchTerms)) ||
          (user.last_name && user.last_name.toLowerCase().includes(searchTerms)) ||
          (user.email && user.email.toLowerCase().includes(searchTerms))

        );
      });
    },
  },
  methods: {
    async fetchUsers() {
      try {
        const response = await this.$axios.get('https://reqres.in/api/users');
        this.users = response.data.data;
      } catch (error) {
        console.error('Error fetching users:', error);
      }
    },
    async addUser() {
      try {
        const response = await this.$axios.post('https://reqres.in/api/users', {
          first_name: this.newUser.name,
          email: this.newUser.email,
        });

        this.users.push(response.data);
        this.newUser.name = '';
        this.newUser.email = '';
      } catch (error) {
        console.error('Error adding user:', error);
      }
    },
    async deleteUser(userId) {
      try {
        await this.$axios.delete(`https://reqres.in/api/users/${userId}`);
        this.users = this.users.filter(user => user.id !== userId);
      } catch (error) {
        console.error('Error deleting user:', error);
      }
    },
    showDetails(user) {
      this.selectedUserDetails = { ...user };
      this.showModal = true;
    },
    saveDetails() {
      console.log('Saved details:', this.selectedUserDetails);
      this.showModal = false;
    },
    closeModal() {
      this.showModal = false;
    },
    handleUserClick(user, event) {
    if (!event.target.classList.contains('delete-button')) {
      this.showDetails(user);
    }
  },
  },
};
</script>

<style>

.container {
  display: flex;
  align-items: top; 
  justify-content: center; 
  height: 100vh; 
}

.user-list {
  display: flex;
  flex-direction: column; 
  align-items: center; 
  list-style: none;
  padding: 0;
  width: 440px;
}

.user-item {
  width: 100%;
  box-sizing: border-box;
  margin-bottom: 10px;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 5px;
  height: 64px;
}

.user-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  margin-right: 10px;
}

.user-info {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: top;
  margin-right: 10px; 
}

.user-info h3 {
  margin: 0px; 
}

.user-info p {
  margin: 5px;
}

h1 {
  text-align: center;
}

form {
  margin-top: 20px;
  display: flex;
  flex-direction: column;
  max-width: 300px;
  margin-left: auto;
  margin-right: auto;
}

label {
  margin-bottom: 5px;
}

input {
  margin-bottom: 10px;
  padding: 8px;
}

button {
  padding: 10px;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background-color: #45a049;
}

.delete-button {
  background: #e74c3c;
  color: #fff;
  border: none;
  padding: 8px 12px;
  cursor: pointer;
  border-radius: 4px;
}

.delete-button:hover {
  background: #c0392b;
}

.user-item {
  cursor: pointer;
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.user-item:hover {
  background-color: #f5f5f5;
}

.save-button {
  background: #2ecc71;
  color: #fff;
  border: none;
  padding: 8px 12px;
  cursor: pointer;
  border-radius: 4px;
}

.save-button:hover {
  background: #27ae60;
}

.larger-modal-content {
  width: 500px;
  max-height: 80vh;
  overflow-y: auto;
  display: flex; 
  justify-content: center;
  align-items: center;
}

.modal-details {
  display: flex;
  flex-direction: column;
  margin-left: 5%;
  margin-right: 5%;
  width: 80%; 
  text-align: left;
}

.modal-input {
  width: 70%;
  box-sizing: border-box;
  margin-bottom: 10px;;
}

.save-button {
  width: 80px;
  margin-top: 10px;
}

.search-container {
  text-align: center;
  margin-bottom: 0px;
}
</style>
