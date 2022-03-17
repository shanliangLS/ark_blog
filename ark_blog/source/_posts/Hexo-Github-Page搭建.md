---
title: Hexo + Github Page 搭建
date: 2020-10-18 22:12:25
categories:
- 快速上手
tags:
- Hexo
- Github
---

## 安装 Hexo

```bash
npm install hexo-cli -g
```

建站

```bash
hexo init blog
cd blog
npm install
```

新建完成后，指定文件夹的目录如下

```
.
├── _config.yml # 网站的配置信息，您可以在此配置大部分的参数。 
├── package.json
├── scaffolds # 模版文件夹
├── source  # 资源文件夹，除 _posts 文件，其他以下划线_开头的文件或者文件夹不会被编译打包到public文件夹
|   ├── _drafts # 草稿文件
|   └── _posts # 文章Markdowm文件 
└── themes  # 主题文件夹
```

运行

```bash
hexo server  # 简写 hexo s
```

打开浏览器访问 [http://localhost:4000](http://localhost:4000) 预览效果

## Github 配置 SSH key

打开命令行输入 cd ~/.ssh 如果没报错或者提示什么的说明就是以前生成过的，直接使用 cat ~/.ssh/id_rsa.pub 命令查看本机上的 SSH key

```bash
cat ~/.ssh/id_rsa.pub
```

如果之前没有创建，先执行以下命令全局配置一下本地账户

```bash
git config --global user.name "Github 用户名"
git config --global user.email "Github 邮箱地址"
```

生成密钥 SSH key

```bash
ssh-keygen -t rsa -C 'Github 邮箱地址'
```

按照提示完成三次回车（可以不设置密码）即可生成 SSH key，使用 `cat ~/.ssh/id_rsa.pub` 获取 SSH key

首次使用还需要确认并添加主机到本机 SSH 可信列表。若返回 Hi xxx! You've successfully authenticated, but GitHub does not provide shell access. 内容，则证明添加成功

登录 Github 在 Setting 找到 SSH 配置，添加 SSH key

## 部署到 Github Page

进入 Github 创建一个新的仓库，仓库名 `用户名.github.io`

- Github 仅能使用一个同名仓库的代码托管一个静态站点

打开本地项目根目录下的 `_config.yml` 配置文件配置参数

```yaml
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: git
  repo:
    github: git@github.com:xxx.github.io.git
  branch: master
```

再安装一个部署插件 `hexo-deployer-git`

```bash
npm install hexo-deployer-git --save
```

最后执行以下命令即可一键部署上传

```bash
hexo generate deploy  # 简写 hexo g -d
```

稍等一会，在浏览器访问网址 https://你的用户名.github.io 就能看到你的博客啦！！

## 写作

执行下列命令来创建一篇新文章或者新的页面

```bash
hexo new [layout] <title>
# hexo new "Hexo + Github Page 搭建"
```

在命令中指定文章的布局（layout），默认为 post，可以通过修改 _config.yml 中的 default_layout 参数来指定默认布局

### 插入图片

在同名文件夹下放入图片，在文章中添加

```markdown
{% asset_img 图片名称.png %}
```