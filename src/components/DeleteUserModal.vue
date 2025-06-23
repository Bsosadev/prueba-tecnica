<template>
  <div v-if="visible" class="fixed inset-0 z-50 flex items-center justify-center bg-black/50 p-2">
    <div class="bg-white rounded-lg shadow-lg w-full max-w-md p-6 relative">
      <button
        @click="$emit('close')"
        class="absolute top-3 right-3 text-gray-500 hover:text-gray-700 focus:outline-none"
        aria-label="Cerrar modal"
      >
        ✕
      </button>

      <h3 class="text-lg font-semibold text-gray-800 mb-4">Confirmar Eliminación</h3>
      <p class="mb-6">
        ¿Estás seguro que deseas eliminar al usuario <strong>{{ user.name }}</strong>?
      </p>

      <div class="flex justify-end space-x-4">
        <button
          @click="$emit('close')"
          class="px-4 py-2 bg-gray-300 text-gray-700 rounded-xl hover:bg-gray-400 focus:outline-none cursor-pointer"
        >
          Cancelar
        </button>
        <button
          @click="confirmDelete"
          class="px-4 py-2 bg-red-600 text-white rounded-xl hover:bg-red-700 focus:outline-none cursor-pointer"
        >
          Eliminar
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import Swal from "sweetalert2";

export default {
  name: "DeleteUserModal",
  props: {
    visible: {
      type: Boolean,
      required: true,
    },
    user: {
      type: Object,
      required: true,
    },
  },
  methods: {
    async confirmDelete() {
      this.$emit("confirm-delete", this.user.id);
      this.$emit("close");

      await Swal.fire({
        icon: "success",
        title: "Usuario eliminado",
        text: `El usuario ${this.user.name} fue eliminado correctamente.`,
        timer: 1200,
        showConfirmButton: false,
      });
    },
  },
};
</script>