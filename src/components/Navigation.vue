<template>
  <v-container>
    <v-navigation-drawer v-model="drawer" app>
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title class="text-h6">
            <template v-if="username">{{ username }} </template><br/>
            <v-list-item-subtitle> Hãy Ghi Chú Công Việc Nào!</v-list-item-subtitle>
          </v-list-item-title>
          <!-- <v-list-item-subtitle> A simple & fast todo list </v-list-item-subtitle> -->
        </v-list-item-content>
      </v-list-item>

      <v-divider></v-divider>

      <v-list dense nav>
        <v-list-item v-for="item in items" :key="item.title" :to="item.to" link>
          <v-list-item-icon>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar app color="primary" dark src="salvador.jpg" prominent>
      <template v-slot:img="{ props }">
        <v-img v-bind="props" gradient="to top right, rgba(19,84,122,.5), rgba(128,208,199,.8)"></v-img>
      </template>

      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>

      <v-app-bar-title class="px-0">
        <template v-if="username">{{ username }} </template><br/>
        <template> Hãy Ghi Chú Công Việc Nào!</template><br/>
        <cite class="subtitle-1">{{ today() }}</cite>
      </v-app-bar-title>

      <v-spacer></v-spacer>
    </v-app-bar>

    <v-dialog ref="dialog" v-model="modal" :return-value.sync="username" persistent width="290px">
      <v-card>
        <v-card-title>Hãy Nhập Tên Của Bạn!</v-card-title>
        <v-text-field v-model="username" class="pa-5"  @keyup.enter="$refs.dialog.save(username)"></v-text-field>
        <v-btn text color="primary" @click.stop="modal = false">Hủy Bỏ</v-btn>
        <v-btn text color="primary" @click.stop="$refs.dialog.save( 'Xin Chào: ' + username)">Lưu</v-btn>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
export default {
  name: "Navigation",
  data: () => ({
    username: "",
    drawer: null,
    items: [
      { title: "Danh Sách Công Việc", icon: "mdi-view-list", to: "/" },
      { title: "About", icon: "mdi-help-box", to: "/about" },
      { title: "Huỳnh Vĩnh Tiến", icon: "mdi-account", to: "/" },
    ],
    modal: false,
  }),
  mounted() {
    return localStorage.tasksUser ? (this.username = localStorage.tasksUser) : (this.modal = true);
  },
  methods: {
    today() {
      const today = new Date();
      return today.toLocaleString("en-US", { year: "numeric", month: "short", day: "numeric" });
    },
  },
  watch: {
    username: {
      handler() {
        return (localStorage.tasksUser = this.username);
      },
    },
  },
};
</script>

<style>
.v-app-bar-title__content {
  overflow: unset !important;
}
</style>
