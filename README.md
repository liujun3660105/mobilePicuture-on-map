# pictureonmap

> pictures by iphone position on map

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).

上传照片时，通过exif读取照片的元数据，获取照片的经纬度，基于openlayer将点位打在google.cn的遥感影像图上。
