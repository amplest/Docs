# npm

## 常规操作

```bash
# 查看node版本
npm -v
# 清楚nodejs的cache
npm cache clean -f
# 更新npm到最新版本
npm install npm@latest -g
```

## 更换镜像地址

```bash
# 得到原本的镜像地址
npm get registry
# 切换淘宝
npm config set registry http://registry.npm.taobao.org/
# 换成原来
npm config set registry https://registry.npmjs.org/
```

## cnpm

```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
