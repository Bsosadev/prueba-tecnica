<template>
    <div class="container mx-auto px-4 py-8 max-w-6xl">
        <div class="flex flex-col md:flex-row justify-between items-center gap-4 mb-6">
            <h2 class="text-3xl font-semibold text-gray-800">Gestión de Usuarios</h2>
            <button @click="showModal = true"
                class="flex items-center gap-2 px-4 py-2 bg-blue-600 text-white rounded-xl shadow hover:bg-blue-700 transition-all duration-300 cursor-pointer">
                <span class="material-icons">person_add</span>
                <span>Agregar Usuario</span>
            </button>
        </div>

        <div class="mb-6 flex justify-center">
            <div class="relative w-full md:w-1/2">
                <span class="absolute inset-y-0 left-3 flex items-center text-gray-400">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                            d="M21 21l-4.35-4.35M17 11a6 6 0 11-12 0 6 6 0 0112 0z" />
                    </svg>
                </span>
                <input v-model="searchQuery" type="text" placeholder="Buscar por nombre, usuario o email"
                    class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500" />
            </div>
        </div>

        <div v-if="loading" class="flex flex-col items-center justify-center py-12">
            <div class="w-12 h-12 border-4 border-blue-500 border-t-transparent rounded-full animate-spin"></div>
            <p class="mt-4 text-gray-600">Cargando usuarios...</p>
        </div>

        <div v-else class="bg-white shadow rounded-xl overflow-hidden mb-8">
            <div class="overflow-x-auto">
                <table class="min-w-full divide-y divide-gray-200 text-sm">
                    <thead class="bg-gray-50">
                        <tr>
                            <th class="px-6 py-3 text-left text-xs font-semibold text-gray-600 uppercase tracking-wide">
                                Nombre</th>
                            <th class="px-6 py-3 text-left text-xs font-semibold text-gray-600 uppercase tracking-wide">
                                Usuario</th>
                            <th class="px-6 py-3 text-left text-xs font-semibold text-gray-600 uppercase tracking-wide">
                                Email</th>
                            <th class="px-6 py-3 text-left text-xs font-semibold text-gray-600 uppercase tracking-wide">
                                Teléfono</th>
                            <th class="px-6 py-3 text-left text-xs font-semibold text-gray-600 uppercase tracking-wide">
                                Acciones</th>
                        </tr>
                    </thead>
                    <tbody class="divide-y divide-gray-100 bg-white">
                        <tr v-for="user in filteredUsers" :key="user.id" class="hover:bg-gray-50 transition-colors">
                            <td class="px-6 py-4 font-medium text-gray-900">{{ user.name }}</td>
                            <td class="px-6 py-4 text-gray-600">{{ user.username }}</td>
                            <td class="px-6 py-4 text-gray-600">{{ user.email }}</td>
                            <td class="px-6 py-4 text-gray-600">{{ user.phone }}</td>
                            <td class="px-6 py-4 flex gap-2">
                                <button @click="editUser(user)"
                                    class="px-2 py-1 bg-yellow-100 text-yellow-800 rounded hover:bg-yellow-200 text-xs font-semibold">
                                    ✏️ Editar
                                </button>
                                <button @click="openDeleteModal(user)"
                                    class="px-2 py-1 bg-red-100 text-red-800 rounded hover:bg-red-200 text-xs font-semibold">
                                    🗑️ Eliminar
                                </button>
                            </td>
                        </tr>

                        <tr v-if="filteredUsers.length === 0">
                            <td colspan="5" class="text-center py-6 text-gray-500 italic">
                                😕 No se encontraron usuarios con esos datos.
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>

        <CreateUserModal :visible="showModal" @close="showModal = false" @add-user="CreateUserFromModal"
            :users="users" />
        <EditUserModal :visible="showEditModal" :user="userToEdit" @close="closeEditModal" @update-user="updateUser"
            :users="users" />
        <DeleteUserModal :visible="showDeleteModal" :user="userToDelete" @close="closeDeleteModal"
            @confirm-delete="deleteUser" v-if="userToDelete" />
    </div>
</template>

<script>
import CreateUserModal from "./CreateUserModal.vue";
import EditUserModal from "./EditUserModal.vue";
import DeleteUserModal from "./DeleteUserModal.vue";

export default {
    components: { CreateUserModal, EditUserModal, DeleteUserModal },
    name: "UserList",
    data() {
        return {
            users: [],
            loading: false,
            showModal: false,
            showEditModal: false,
            userToEdit: null,
            showDeleteModal: false,
            userToDelete: null,
            searchQuery: "",
        };
    },
    methods: {
        fetchUsers() {
            this.loading = true;

            const savedUsers = localStorage.getItem("users");
            if (savedUsers) {
                this.users = JSON.parse(savedUsers);
                this.loading = false;
            } else {

                fetch("https://jsonplaceholder.typicode.com/users")
                    .then((response) => response.json())
                    .then((data) => {
                        this.users = data;

                        localStorage.setItem("users", JSON.stringify(this.users));
                    })
                    .catch((error) => {
                        console.error("Error al obtener usuarios:", error);
                    })
                    .finally(() => {
                        this.loading = false;
                    });
            }
        },
        CreateUserFromModal(newUserData) {
            const maxId = this.users.length
                ? Math.max(...this.users.map((u) => u.id))
                : 0;
            const id = maxId + 1;

            const userToAdd = {
                id,
                name: newUserData.name.trim(),
                username: newUserData.username.trim(),
                email: newUserData.email.trim(),
                phone: newUserData.phone.trim(),
            };

            this.users.unshift(userToAdd);
            localStorage.setItem("users", JSON.stringify(this.users));
        },
        editUser(user) {
            this.userToEdit = { ...user };
            this.showEditModal = true;
        },
        closeEditModal() {
            this.showEditModal = false;
            this.userToEdit = null;
        },
        updateUser(updatedUser) {
            const index = this.users.findIndex((u) => u.id === updatedUser.id);
            if (index !== -1) {
                this.users[index] = updatedUser;
                localStorage.setItem("users", JSON.stringify(this.users));
            }
        },
        openDeleteModal(user) {
            this.userToDelete = { ...user };
            this.showDeleteModal = true;
        },
        closeDeleteModal() {
            this.showDeleteModal = false;
            this.userToDelete = null;
        },
        deleteUser(userId) {
            this.users = this.users.filter((u) => u.id !== userId);
            localStorage.setItem("users", JSON.stringify(this.users));
            this.closeDeleteModal();
        },
    },
    mounted() {
        this.fetchUsers();
    },
    computed: {
        filteredUsers() {
            if (!this.searchQuery.trim()) return this.users;

            const query = this.searchQuery.toLowerCase();

            return this.users.filter((user) =>
                user.name.toLowerCase().includes(query) ||
                user.username.toLowerCase().includes(query) ||
                user.email.toLowerCase().includes(query)
            );
        },
    }
};
</script>
