<template>
  <div class="px-6 about">
    <h1>TodoList - Vuetify</h1>

    <p><strong>Công Nghệ Sử Dụng</strong> <br/> - Vue 2 <br/> - Vuetify (Framework của VueJS)<br/> - VueX <br/>  - Docker</p>

    <p><strong>Chức Năng Của TodoList</strong></p>
    <ul style="margin-top: -15px; margin-bottom: 10px;">
      <li>Thêm / Chỉnh Sửa Công Việc (Task)</li>
      <li>Thêm ngày hết hạn (chỉ tháng/ngày)</li>
      <li>Đánh dấu nhiệm vụ là đã hoàn thành</li>
      <li>Xóa Công Việc (Task)</li>
      <li>Sắp Xếp Danh Sách Kéo Thả</li>
    </ul>
    <v-divider></v-divider>

    <br />
    <h4>&copy; Mọi Bản Quyền Thuộc Về Huỳnh Vĩnh Tiến</h4>
    <v-list dense>
      <v-list-item-group color="primary">
        <v-list-item v-for="network in networks" :key="network.id" @click="goTo(network.link)">
          <v-list-item-icon>
            <v-icon v-text="`mdi-${network.icon}`"></v-icon>
          </v-list-item-icon>
          <v-list-item-content>
            <v-list-item-title v-text="network.name"></v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list-item-group>
    </v-list>
  </div>
</template>

<script>
export default {
  name: "About",
  data() {
    return {
      networks: [
        {
          name: "Huỳnh Vĩnh Tiến",
          icon: "github",
          link: "https://github.com/hvtienprotv84",
        },
        {
          name: "Huỳnh Vĩnh Tiến",
          icon: "facebook",
          link: "https://www.facebook.com/vinhtien.huynh.77/",
        },
      ],
    };
  },
  methods: {
    goTo(link) {
      return window.open(link, "network");
    },
  },
};
</script>
