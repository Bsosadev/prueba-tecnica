<template>
  <div id="app">
    <UserList />
  </div>
</template>

<script>
import UserList from "./components/UsersList.vue";

export default {
  name: "App",
  components: {
    UserList,
  },
};
</script>
