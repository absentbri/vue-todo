# vue-todo

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Lints and fixes files
```
npm run lint
```

### Compiles and minifies for production
```
npm run build
```

### Builds production Docker (served by alpine nginx)
```
docker build -t absentbri/vue-todo .
```

### Creates and runs docker container
#### interactive
```
docker run -it -p 80:80 --name vue-todo absentbri/vue-todo
```
#### headless
```
docker run -d -p 80:80 --name vue-todo absentbri/vue-todo
```

http://localhost:80

### Docker Compose method
```bash
docker-compose up
```

Binds to port 8081 

http://localhost:8081
