---
id: doc1
title: Docusaurus 构建网站
sidebar_label: Docusaurus 构建网站
---

## 1. 初始化环境配置

+ 安装Git

+ 安装Node.js

  ```
  $ brew install node
  $ node -v
  
  v8.5.0
  ```

+ 创建一个GitHub项目：

  例如 repository 名为 `docusaurus-tutorial`，不要加`.gitignore`与license

+ 在终端：

  ```
  cd /Users/NAME/doc_projects # macOS example
  
  git clone git@github.com:USERNAME/docusaurus-tutorial.git # SSH
  # or
  git clone https://github.com/USERNAME/docusaurus-tutorial.git # HTTPS
  ```

+ 安装 `Docusaurus 初始化`命令

  ```
  npm install --global docusaurus-init
  ```

## 2. 初始化一个站点

```
$ cd docusaurus-tutorial
$ docusaurus-init # 将创建一些模板，部分文件如下：

├── Dockerfile
├── docker-compose.yml
├── docs
│   ├── doc1.md
│   ├── doc2.md
│   ├── doc3.md
│   ├── exampledoc4.md
│   └── exampledoc5.md
└── website
    ├── README.md
    ├── blog
    │   ├── 2016-03-11-blog-post.md
    │   ├── 2017-04-10-blog-post-two.md
    │   ├── 2017-09-25-testing-rss.md
    │   ├── 2017-09-26-adding-rss.md
    │   └── 2017-10-24-new-version-1.0.0.md
    ├── sidebars.json
    ├── siteConfig.js

$ cd website && npm start  # 将会在浏览器打开 http://localhost:3000
```



## 3. 创建一个页面

### 3.1 创建一个常规的页面

1. 进入`website/pages/en`文件夹，保存下面内容到`hello-world.js`:

```js
const React = require('react');

const CompLibrary = require('../../core/CompLibrary.js');

const Container = CompLibrary.Container;
const GridBlock = CompLibrary.GridBlock;

function HelloWorld(props) {
  return (
    <div className="docMainWrapper wrapper">
      <Container className="mainContainer documentContainer postContainer">
        <h1>Hello World!</h1>
        <p>This is my first page!</p>
      </Container>
    </div>
  );
}

module.exports = HelloWorld;
```

2. 然后到 http://localhost:3000/hello-world 查看内容

### 3.2 创建一个文档页面

1. 进入`docs`文件夹(与website文件夹在同一级)，并创建`doc9.md`文件。

2. 在`doc9.md`中放入下面内容：

   ```
   ---
   id: doc9
   title: This is Doc 9
   ---
   
   I can write content using [GitHub-flavored Markdown syntax](https://github.github.com/gfm/).
   
   ## Markdown Syntax
   
   **Bold** _italic_ `code` [Links](#url)
   
   > Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
   > id sem consectetuer libero luctus adipiscing.
   
   * Hey
   * Ho
   * Let's Go
   ```

3. 修改`sidebars.json`将markdown文件显示出来：打开`website/sidebars.json`，并添加`doc9`. `ID`要与`doc9.md`的ID相同

   ```json
   {
     "docs": {
       "Docusaurus": [
         "doc1",
   +     "doc9"
       ],
       "First Category": ["doc2"],
       "Second Category": ["doc3"]
     },
     "docs-other": {
       "First Category": ["doc4", "doc5"]
     }
   }
   ```

4. `cmd` + `C` 关闭启动并`npm start`重启

5. http://localhost:3000/docs/doc9  将能显示该markdown页面。

## 4. 发布站点

1. 编辑`docusaurus-tutorial/website/siteConfig.js` 修改以下内容：

   ```js
   const siteConfig = {
     ...
     url: 'https://USERNAME.github.io', // Replace USERNAME with your GitHub username.
     baseUrl: '/docusaurus-tutorial/', // The name of your GitHub project.
     projectName: 'docusaurus-tutorial',  // The name of your GitHub project. Same as above.
     organizationName: 'USERNAME' // Your GitHub username.
     ...
   }
   ```

2. 在终端`cmd+C`

3. 在`website`文件夹，运行`npm run build`. 该命令会生成HTML静态文件到`docusaurus-tutorial/website/build` 文件夹。

4. 用自己的用户名替换 `USERNAME`并运行下面命令：

   

   ```
   GIT_USER=USERNAME CURRENT_BRANCH=master USE_SSH=true npm run publish-gh-pages # SSH
   # or
   GIT_USER=USERNAME CURRENT_BRANCH=master npm run publish-gh-pages # HTTPS
   ```

   HTML会放到`https://github.com/USERNAME/docusaurus-tutorial`

## 5. 添加版本

杀手级功能--版本控制。

### 5.1 发布一个版本

假设你对当前版本满意，并想将其冻结为`V1.0.0`版本:

```
$ cd website
$ npm run examples versions # 生成一个 versions.json，它保存该项目中的所有的版本信息
$ npm run version 1.0.0 # 生成你想冻结的版本，如1.0.0;将考本docs文件到website/versioned_docs/version-1.0.0文件夹
```

输入`npm start`启动，可以看到左上角有版本信息。现在，如果去`docs/doc1.md`修改一些信息，打开看网页还是没变。这是因为版本的信息已被保存，直到下个版本才能被显示出来。

### 5.2 下一个版本

假如文件有修改，还不想发布，可以通过：

`http://localhost:3000/Linux-best-practice/docs/next/doc1` 来查看。

如果发布到下个版本，可以输入：

```
npm run version 1.0.1
```

则`1.0.1`版本成为了过去，当前版本变成了`1.0.1`.但仍可以通过URL访问旧版本：`http://localhost:3000/docusaurus-tutorial/docs/1.0.0/doc1`

然后按照`第4步`进行发布吧。