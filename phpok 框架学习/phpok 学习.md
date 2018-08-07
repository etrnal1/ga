## phpok 学习



### 模板

{}  <!-- -- >定界符

#### 引用

 文件名

 <!-- include file = -- >

 <!-- include file = -- >

#### 示例

<!-- -include tpl=head- >

<!-- include tpl=head welcome=欢迎使用 -->

循环

<!-- loop from=$rslist key=$key value=$value [id=rslist_id] -->
...
<!-- /loop -->



##### 条件控制

`<!-- if 条件 -->`

`...`

`<!-- /if --`>

`<!-- if 条件 -->`

`...`

`<!-- elseif 条件2 -->`

`...`

`<!-- /if -->`



### api 帮助文档

### 变量与方法



### 二次开发

#### 程序与架构

control 类

### 模板制作

数据调用



```
<!-- php:$list = phpok('标识串','参数') -->
```

### 主要文件目录

framework 

admin   控制器





view  视图



debug 在网站页面进行调试



{debug 里面随便调试文档 }













### 系统变量 

$sys 

$menu_id  控制左边栏的menu_id 

如果存在，即有左边栏，如果不存在则没;

