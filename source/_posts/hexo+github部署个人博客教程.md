---
title: hexo+github部署个人博客教程
date: 2019-07-25
categories: 技能
author: yangpei
tags:
  - 技能
comments: true
cover_picture: /images/banner.jpg
---

[hexo 官方文档](https://hexo.io/zh-cn/docs/)

这是我的个人博客效果: [YP blog](https://iloveyou11.github.io/)

<!-- more -->

#### 搭建的具体流程

- 安装 git，申请 github 账号，生成 ssh 密钥，在 github 新建 new SSH Key
- 在 github 新建个人仓库用户名，取名为**''用户名.github.io''**这个用户名使用你的 GitHub 帐号名称代替，这个是固定写法
- 安装 nodejs
- 安装 hexo `npm install -g hexo-cli`
- 初始化博客 `hexo init [name]`创建博客项目
- 为了检测网站别按顺序输入以下命令：`hexo g` 、`hexo s`，打开 localhost:4000 查看网站是否成功显示
- 关联 github，安装包`npm install hexo-deployer-git --save`，修改\_config.yml 配置文件

```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/name/name.github.io.git  #记得加.git
  branch: master
```

- 分别运行`hexo g` 、`hexo s`，在https://[yourname].github.io/查看博客（在下方会讲命令的具体作用）
- 关于主题更换（默认是 landscape 主题，如果想更换主题请看这里）
  在主题官网选择好主题，找到对应的 github 仓库，`git clone [theme path] themes/[theme name]`安装到本项目的 themes 文件夹下，并修改\_config.yml 配置文件中 theme（名字与你命名的主题文件夹名字相同），再分别运行

```
hexo clean  #清除缓存
hexo g   #相当于hexo generate，生成博客
hexo d   #相当于hexo deploy，部署博客
```

打开你的博客地址即可看到效果

- 关于博客书写
  `hexo n "博客名字"`，会在`source/_posts`文件夹下生成 markdown 文件，使用 markdown 语法书写博客即可，建议使用[Marxico](http://marxi.co/)可视化工具编写（也可以采用其他的可视化工具，视个人而定）
- 博客的格式
  博客的大体可以采用以下格式（不同主题格式或许有些不同，可以自行查阅相关文档）：

```markdown
---
title: Hello World
date: 2019-07-25
author: XXX
categories: XXX
tags:
  - Android
comments: true
cover_picture: /images/banner.jpg
---

这里写博客开头部分内容

# 如果添加下行语句，文章会自动生成“更多”按钮，点击后 more 后面的内容才会显示

<!-- more -->

这里写后续的博客内容
```

- 关于图片
  如果采用本地项目文件夹存储图片，每次打开博客会加载大量的图片，会导致性能有所下降，建议使用图床存储，如[七牛](https://www.qiniu.com/) 或 [贴图库](http://www.tietuku.com/) ，在文章中直接采用外链引入图片

**[坑]**：如果 hexo g 生成的文件缺少 index.html 和 archive，部署后打开博客会出现 404 的错误说找不到 index,html 文件,在 github 仓库中打开确实没有.造成这个问题的原因是缺少相应的 npm 包，输入命令`npm ls --depth 0`，安装好缺少的包重新发布即可.好像是因为安装了新的主题而引起的,这个问题也是折腾了我好久.-\_-

#### hexo 常用命令

```
npm install hexo -g #全局安装Hexo
npm update hexo -g #升级hexo
hexo init #初始化项目
hexo n "name" <=> hexo new "name" #新建名为name的文章
hexo g <=> hexo generate #生成博客
hexo s <=> hexo server #启动服务，本地预览效果
hexo d <=> hexo deploy #部署博客
```

#### typora 可视化工具的常用快捷键(windows 平台)

```
加粗：ctrl+b
倾斜：ctrl+i
下划线：ctrl+u
删除线：alt+shift+5
标题：ctrl+数字
代码块：ctrl+alt+f
表格：ctrl+t
插入图片：直接拖动到指定位置即可或者ctrl+shift+i
插入链接：ctrl+k
Shift+Tab键 格式化代码
生成目录：[TOC]按回车
选中一整行：ctrl+l
选中单词：ctrl+d
选中相同格式的文字：ctrl+e
跳转到文章开头：ctrl+home
跳转到文章结尾：ctrl+end
搜索：ctrl+f
替换：ctrl+h
```

#### markdown 常用语法

**标题**

```
# 这是一级标题
## 这是二级标题
### 这是三级标题
#### 这是四级标题
##### 这是五级标题
###### 这是六级标题
```

**字体**

```
**这是加粗的文字**
*这是倾斜的文字*`
***这是斜体加粗的文字***
~~这是加删除线的文字~~
```

**引用**

```
>这是引用的内容
>>这是引用的内容
>>>>>>>>>>这是引用的内容
```

**分割线**

```
---
----
***
*****
```

**图片**

```
![图片alt](图片地址 ''图片title'')
图片alt就是显示在图片下面的文字，相当于对图片内容的解释。
图片title是图片的标题，当鼠标移到图片上时显示的内容。title可加可不加
```

**超链接**

```
[超链接名](超链接地址 "超链接title") #title可加可不加
<a href="超链接地址" target="_blank">超链接名</a>
```

**列表**

```
- 列表内容  #无序列表
+ 列表内容
* 列表内容
1.列表内容  #有序列表
2.列表内容
3.列表内容
```

**表格**

```
表头|表头|表头
---|:--:|---:
内容|内容|内容
内容|内容|内容
```

**代码**

````
 `代码内容`  #单行

(```)  #多行，空格不要，```后面可以写上语言类别，如javascript等
  代码...
  代码...
  代码...
(```)
````

**流程图**

````
```flow
st=>start: 开始
op=>operation: My Operation
cond=>condition: Yes or No?
e=>end
st->op->cond
cond(yes)->e
cond(no)->op
&```
````

**字体大小颜色、大小、背景色设置**

```
红色文字: <font color="#dd0000">红色文字：</font><br />
字体大小: <font size="1">size为1</font><br />
背景色:   <table><tr><td bgcolor=#FF00FF>背景色的设置是按照十六进制颜色值：#7FFFD4</td></tr></table>
```
