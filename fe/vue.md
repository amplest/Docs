# Vue

## 环境打包

``` json
"dev": "vue-cli-service serve", //默认加载.env.development
"test": "vue-cli-service serve --mode test", //加载.env.test
"pro": "vue-cli-service serve --mode production",//加载.env.production
"build": "vue-cli-service build", //默认加载.env.production
"build:dev": "vue-cli-service build --mode development", //加载.env.development
"build:test": "vue-cli-service build --mode test", //加载.env.test
```