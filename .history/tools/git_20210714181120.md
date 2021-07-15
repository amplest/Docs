# GIT

## 创建ssh

将`id_rsa.pub`文件中的秘钥复制到对应平台的公/私钥中

``` bash
# 检查本机是否有.ssh文件
cd ~/.ssh
# 配置名称(全局)
git config --global user.name "Author Name"
# 配置邮箱(全局)
git config --global user.email "Author Email"
# 创建ssh key
ssh-keygen -t rsa -C "Author Email”
```

## 配置当前项目用户名与邮箱
``` bash
git config user.name "Author Name"
git config user.email "Author Email"
```

## 远程库链接及上传操作

``` bash
# 初始化本地代码仓
git init     
# 添加本地代码        
git add -A        
# 提交本地代码    
git commit -m "..."   
#添加远程版本库
git remote add origin git@github.com:username/repositoriesname.git 
# 上传代码
git push origin master -f 
```

## CRLF问题
``` bash
# warning: LF will be replaced by CRLF in xxx
git config --global core.autocrlf false
```

## commit规范

| 类型     | 描述                                                                |
| :----------: | --------------------------------------------------------------------- |
| feat     | 新功能（feature）                                                |
| fix/to   | 修复bug，可以是QA发现的BUG，也可以是研发自己发现的BUG |
| fix      | 产生diff并自动修复此问题。适合于一次提交直接修复问题 |
| to       | 只产生diff不自动修复此问题。适合于多次提交。最终修复问题提交时使用fix |
| docs     | 文档（documentation）                                             |
| style    | 格式（不影响代码运行的变动）                            |
| refactor | 重构（即不是新增功能，也不是修改bug的代码变动） |
| perf     | 优化相关，比如提升性能、体验                            |
| test     | 增加测试                                                          |
| chore    | 构建过程或辅助工具的变动                                  |
| revert   | 回滚到上一个版本                                              |
| merge    | 代码合并                                                          |
| sync     | 同步主线或分支的Bug                                           |
