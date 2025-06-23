<template>
    <div class="container mx-auto px-4 py-8 max-w-6xl">
        <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-bold text-gray-800">Usuarios</h2>

            <button @click="showModal = true"
                class="px-4 py-2 bg-blue-600 text-white rounded-xl hover:bg-blue-700 cursor-pointer">
                Agregar Usuario
            </button>
        </div>

        <div v-if="loading" class="flex flex-col items-center justify-center py-12">
            <div class="w-12 h-12 border-4 border-blue-500 border-t-transparent rounded-full animate-spin"></div>
            <p class="mt-4 text-gray-600">Cargando usuarios...</p>
        </div>

        <div v-else class="bg-white shadow-md rounded-lg overflow-hidden mb-8">
            <div class="overflow-x-auto">
                <table class="min-w-full divide-y divide-gray-200">
                    <thead class="bg-gray-50">
                        <tr>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                Nombre</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                Usuario</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                Email</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                Teléfono</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                Acciones</th>
                        </tr>
                    </thead>
                    <tbody class="bg-white divide-y divide-gray-200">
                        <tr v-for="user in users" :key="user.id" class="hover:bg-gray-50 transition-colors">
                            <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">{{ user.name }}
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ user.username }}</td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ user.email }}</td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ user.phone }}</td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                                <button @click="editUser(user)"
                                    class="text-blue-500 hover:underline mr-2 cursor-pointer"> Editar </button>
                                <button @click="openDeleteModal(user)"
                                    class="text-red-500 hover:underline cursor-pointer">Eliminar</button>
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
};
</script>
