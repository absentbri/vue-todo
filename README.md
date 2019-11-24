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
```
docker run -it -p 80:80 --name vue-todo absentbri/vue-todo
```

