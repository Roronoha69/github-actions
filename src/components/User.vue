<script>
import axios from 'axios';

export default {
  data() {
    return {
      userEmail: '',
      currentUser: {},
      workingTimes: [],
      users: [],
      acceptedStatus: [200, 201, 202, 203, 204]
    };
  },
  methods: {
    fetchUserByEmail() {
      axios
        .get(`http://localhost:4000/api/users?email=${this.userEmail}`)
        .then((response) => {
          if (response.data.data.length > 0) {
            this.currentUser = response.data.data[0];
            console.log('Utilisateur récupéré avec succès :', this.currentUser);
          } else {
            console.error('Utilisateur non trouvé');
          }
        })
        .catch((error) => {
          console.error('Erreur lors de la récupération de l\'utilisateur :', error.message);
        });
    },
    fetchUsers() {
      axios
        .get(`http://localhost:4000/api/users`)
        .then((response) => {
          if (response.data.data.length > 0) {
            this.users = response.data.data;
            console.log('Utilisateurs récupérés avec succès :', this.users);
          } else {
            console.error('Aucun utilisateur en base');
          }
        })
        .catch((error) => {
          console.error('Erreur lors de la récupération des utilisateurs :', error.message);
        });
    },
    deleteUser(userId) {
      if (confirm("Êtes-vous sûr de vouloir supprimer cet utilisateur ?")) {
        axios
          .delete(`http://localhost:4000/api/users/${userId}`)
          .then((response) => {
            if (this.acceptedStatus.includes(response.status)) {
              this.users = this.users.filter(user => user.id !== userId);
              console.log(`Utilisateur avec ID ${userId} supprimé avec succès.`);
            } else {
              console.error(`Échec de la suppression de l'utilisateur avec ID ${userId}. Statut: ${response.status}`);
            }
          })
          .catch((error) => {
            console.error('Erreur lors de la suppression de l\'utilisateur :', error.message);
          });
      }
    },
    update() {}
  },
  mounted() {
    this.fetchUsers();
  }
};
</script>

<template>
  <div class="search">
    <h2>Gestion des utilisateurs</h2>

    <form @submit.prevent="fetchUserByEmail">
      <label for="email">Entrer l'email de l'utilisateur :</label>
      <input type="email" v-model="userEmail" id="email" required />
      <button type="submit">Récupérer l'utilisateur</button>
    </form>

    <div v-if="currentUser.username">
      <h3>Utilisateur récupéré :</h3>
      <p>Nom : {{ currentUser.username }}</p>
      <p>Email : {{ currentUser.email }}</p>
    </div>

    <h3>Liste des utilisateurs</h3>
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Nom</th>
          <th>Email</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in users" :key="user.id">
          <td>{{ user.id }}</td>
          <td>{{ user.username }}</td>
          <td>{{ user.email }}</td>
          <td>
            <button @click="deleteUser(user.id)" class="delete-button">
              🗑️
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
.search {
  display: flex;
  align-items: center;
  flex-direction: column;
}

h2, h3 {
  color: #333;
}
form {
  margin: 20px 0;
}
input {
  display: block;
  margin: 10px 0;
  padding: 8px;
  border: 1px solid #ddd;
}
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}
th, td {
  border: 1px solid #ddd;
  padding: 12px;
  text-align: left;
}
th {
  background-color: #f4f4f4;
}
button {
  margin: 5px;
  padding: 8px 12px;
  background-color: #3498db;
  color: white;
  border: none;
  cursor: pointer;
}
button:hover {
  background-color: #2980b9;
}
.delete-button {
  background-color: #e74c3c;
}
.delete-button:hover {
  background-color: #c0392b;
}
</style>
