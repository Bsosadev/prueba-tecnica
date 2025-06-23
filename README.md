# Prueba CRUD con Vue.js

Aplicación para gestionar usuarios (crear, editar, eliminar) utilizando Vue.js con API Options. Los datos se almacenan localmente en `localStorage`

---

## Características

- ✅ CRUD completo: Crear, leer, actualizar y eliminar usuarios
- 💬 Modales interactivos para formularios y confirmaciones
- 🔍 Búsqueda dinámica por nombre, usuario o email
- 🧠 Validaciones personalizadas con SweetAlert2
- 💾 Almacenamiento local con `localStorage`
- 🎨 Interfaz responsiva con TailwindCSS

---

## Tecnologías usadas

- [Vue.js 3 (Options API)](https://vuejs.org/)
- [TailwindCSS](https://tailwindcss.com/)
- [Vite](https://vitejs.dev/)
- [SweetAlert2](https://sweetalert2.github.io/)
- LocalStorage (almacenamiento local del navegador)

---

## Instalación y ejecución

1. Clona este repositorio:

```bash
git clone https://github.com/Bsosadev/prueba-tecnica
cd prueba-tecnica
```

2. Instala dependencias:

```bash
npm install
```

3. Ejecuta el servidor de desarrollo:

```bash
npm run dev
```

4. Abre tu navegador en:

```
http://localhost:5173/
```

---

## Estructura del proyecto

```
src/
├── components/
│   ├── CreateUserModal.vue
│   ├── EditUserModal.vue
│   ├── DeleteUserModal.vue
│   └── UsersList.vue
├── App.vue
└── main.js
```

- `UsersList.vue`: Componente principal que maneja el estado local y muestra la lista de usuarios.
- Modales para crear, editar y eliminar usuarios, implementados como componentes independientes.

---