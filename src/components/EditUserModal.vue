<template>
  <div v-if="visible" class="fixed inset-0 z-50 flex items-center justify-center bg-black/50 p-2">
    <div class="bg-white rounded-lg shadow-lg w-full max-w-lg p-6 relative">
      <button
        @click="$emit('close')"
        class="absolute top-3 right-3 text-gray-500 hover:text-gray-700 focus:outline-none"
        aria-label="Cerrar modal"
      >
        ✕
      </button>

      <h3 class="text-lg font-semibold text-gray-800 mb-4">Editar Usuario</h3>
      <form @submit.prevent="handleSubmit" class="space-y-4">
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div>
            <label for="name" class="block text-sm font-medium text-gray-700 mb-1">Nombre:</label>
            <input
              type="text"
              id="name"
              v-model="localUser.name"
              required
              class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"
            />
          </div>
          <div>
            <label for="username" class="block text-sm font-medium text-gray-700 mb-1">Usuario:</label>
            <input
              type="text"
              id="username"
              v-model="localUser.username"
              required
              class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"
            />
          </div>
          <div>
            <label for="email" class="block text-sm font-medium text-gray-700 mb-1">Correo electrónico:</label>
            <input
              type="email"
              id="email"
              v-model="localUser.email"
              required
              class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"
            />
          </div>
          <div>
            <label for="phone" class="block text-sm font-medium text-gray-700 mb-1">Teléfono:</label>
            <input
              type="text"
              id="phone"
              v-model="localUser.phone"
              class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"
            />
          </div>
        </div>
        <div class="flex justify-end">
          <button
            type="submit"
            class="px-4 py-2 bg-blue-600 text-white rounded-xl hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition-colors"
          >
            Guardar Cambios
          </button>
        </div>
      </form>

      <p v-if="errorMessage" class="mt-4 text-sm text-red-600">{{ errorMessage }}</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "EditUserModal",
  props: {
    visible: {
      type: Boolean,
      required: true,
    },
    user: {
      type: Object,
      required: false,
      default: () => ({}),
    },
  },
  data() {
    return {
      localUser: { ...this.user },
      errorMessage: "",
    };
  },
  watch: {

    user(newUser) {
      this.localUser = { ...newUser };
      this.errorMessage = "";
    },
  },
  methods: {
    validateEmail(email) {
      const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      return re.test(email);
    },
    handleSubmit() {
      this.errorMessage = "";

      if (!this.validateEmail(this.localUser.email)) {
        this.errorMessage = "Por favor, ingresa un correo electrónico válido.";
        return;
      }

      this.$emit("update-user", { ...this.localUser });

      this.$emit("close");
    },
  },
};
</script>