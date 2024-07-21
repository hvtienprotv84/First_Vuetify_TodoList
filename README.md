<div align="center">
  <img src="https://cdn.vuetifyjs.com/docs/images/brand-kit/v-logo-atom.svg" width="160"/> &nbsp; &nbsp;
  <img src="https://velog.velcdn.com/images/holawan/post/57af9071-087d-41aa-84ac-e675eabc44a2/image.png" width="220"/>
  <img src="https://upload.wikimedia.org/wikipedia/commons/f/f1/Vue.png" width="160"/> &nbsp; &nbsp;
</div>

# First Vuetify - TodoList
- Vue 2
- Vuetify (Framework của VueJS)
- VueX
- Docker
- Responsive Website và Mobile

# RUN
- `npm install`
- `npm run serve`

# Build Và Chạy Dự Án Trên Docker

# **Yêu Cầu:**

- [Docker](https://www.docker.com/get-started)
- [Docker Compose](https://docs.docker.com/compose/install)

### Chạy Và Build Dự Án lên "Images" của Docker 
- (Bước 1 chỉ Build Dự Án Chưa Có Thể Chạy Dự Án Trên Port của Docker)
```
docker-compose up -d
```

### Chạy Và Build Dự Án lên "Containers" của Docker 
- (Bước 2 Đã Hoàn Thiện Vì Đã Đưa Dự Án lên "Containers" của Docker và Chạy Dự Án Với Port: `http://localhost:8080/` của Docker)
```
yarn build
```
