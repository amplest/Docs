# DEDECMS

## 标签调用

### 公共区域

| Code                                                                                 | Info                  |
| ------------------------------------------------------------------------------------ | --------------------- |
| {dede:include filename="head.htm"/}                                                  | 公共文件调用head/foot |
| {dede:global.cfg_powerby/}                                                           | 版权调用          |
| {dede:global.cfg_beian/}                                                             | 备案编号          |
| {dede:field name='typename'/}                                                        | 当前栏目名称    |
| {dede:type typeid='17'}\<a href="[field:typelink/]">[field:typename/]\</a>{/dede:type} | 调用指定id栏目  |
| {dede:pagelist istitem="index,pre,next,end,option,info," listsize="5"/}              | 分页调用          |

### 列表区域

| Code                                                         | Info             |
| ------------------------------------------------------------ | ---------------- |
| [field:pubdate function="MyDate('Y-m-d',@me)"/]              | 2021-07-14格式 |
| [field:pubdate function="MyDate('\<b>Y\</b>\<p>d-m\</p>',@me)"/] | 样式自定义  |
| [field:arcurl /]                                             | 链接           |
| [field:litpic/]                                              | 缩略图        |
| [field:title /]                                              | 标题           |
| [field:fulltitle/]                                           | 全标题        |
| [field:infos /]                                              | 简介           |
| [field:infos function="cn_substr(@me,字符数)"/]           | 简介: 可限制字数 |
| [field.description]                                          | 摘要           |
| [field:description function="cn_substr(@me,字符数)"/]     | 摘要: 可限制字数 |

### 文章区域

| Code                                                 | Info           |
| ---------------------------------------------------- | -------------- |
| {dede:field.pubdate function="MyDate('Y-m-d',@me)"/} | 2021-07-14格式 |
| {dede:field.title/}                                  | 标题         |
| {dede:field.body/}                                   | 内容         |
| {dede:field.writer/}                                 | 作者         |
| {dede:field.description/}                            | 摘要         |
| {dede:prenext get='pre'/}                            | 上一篇      |
| {dede:prenext get='next'/}                           | 下一篇      |

### 单页区域

单页区域常规操作及其他操作

| Code                                                 | Info           |
| ---------------------------------------------------- | -------------- |
| {dede:field.content/} | 单页内容调用 |

``` javascript
// 限制字数，一般适用于首页简介
{dede:sql sql="SELECT content FROM dede_arctype where id=6"}
    [field:content function=cn_substr(Html2Text(@me),500)/]
{/dede:sql}

// 正常展示文字
{dede:sql sql="SELECT content FROM dev_arctype where id=30"}
    [field:content/]
{/dede:sql}
```
### 栏目区域

#### 顶级栏目调用

``` html
{dede:channel row="5" type="top"}
	<a href='[field:typeurl/]'>[field:typename/]</a>
{/dede:channel}
```

#### 顶级栏目(高亮)

``` html
<li {dede:field name=typeid runphp="yes"}(@me=="")? @me=" class='menu_on'":@me="";{/dede:field}>
	<a href="/">网站首页</a>
</li>
{dede:channel type='top' row='8' currentstyle="<li class='menu_on'><a href='~typelink~' >~typename~</a> </li>" }
	<li><a href="[field:typelink/]">[field:typename/]</a></li>
{/dede:channel}
```

#### 二级栏目

``` html
<!-- 固定id的下的二级栏目 -->
{dede:channel type='son' typeid='1'}
    <a href='[field:typeurl/]'>[field:typename/]</a>
{/dede:channel} 

<!-- 当前顶级栏目下的所有二级栏目 -->
{dede:channel type='son'}
    <a href='[field:typeurl/]' title="[field:typename/]">[field:typename/]</a>
{/dede:channel}
```

#### 二级栏目(高亮)

``` html
{dede:channel type='son' row='100' currentstyle="<a href='~typelink~' class='left-nav-on'>~typename~</a>"}
    <a href='[field:typelink/]'>[field:typename/]</a>
{/dede:channel}
```

#### 带顶级栏目的二级栏目(高亮)

``` html
<ul>
<li {dede:field name=typeid runphp="yes"}(@me=="")? @me=" class='active'":@me="";{/dede:field}>
  <a href="/">网站首页</a>
</li>
{dede:channelartlist typeid='25,26,27,28,29,30,31,32,33' row='100' currentstyle="active" }
  <li class="{dede:field.currentstyle/}"><a href="{dede:field name='typeurl'/}"> {dede:field name='typename'/}</a>
  <div class="head-menu-down">
    {dede:channel type='son' noself='yes'}<a href="[field:typelink/]" >[field:typename/]</a>{/dede:channel}
  </div>
  </li>
{/dede:channelartlist}
</ul>
```

#### 顶级栏目与二级栏目分开调用

``` html
<li><a href="">Home</a></li>
<li>{dede:type typeid='1'}<a href='[field:typelink/]'>[field:typename/]</a>{/dede:type}
<div class="navdow">
    {dede:channel type="son" typeid="1"}
    <a href="[field:typeurl/]" title="[field:typename/]">[field:typename/]</a>
    {/dede:channel}
</div>
```

#### 调用id为3，5的顶级栏目及相对应的二级栏目

``` html
{dede:channelartlist typeid='3,5'}
    <a href="{dede:field name='typeurl'/}">
        <b>{dede:field name='typename'/}</b>
    </a>
    {dede:channel type='son' noself='yes'}
        <a href="[field:typelink/]">[field:typename/]</a>
    {/dede:channel}
{/dede:channelartlist}
```

#### 调用id为3的顶级栏目及相对应的二级栏目

``` html
{dede:channelartlist typeid='3,3'}
    <a href="{dede:field name='typeurl'/}">
        {dede:field name='typename'/}
    </a>
    {dede:channel type='son' noself='yes'}
        <a href="[field:typelink/]">[field:typename/]</a>
    {/dede:channel}
{/dede:channelartlist} 
```

#### 调用id为4的顶级栏目及相对应的产品，带高亮

``` html
{dede:channelartlist typeid='2,53' currentstyle='active'}
<li class="{dede:field.currentstyle/}">
    <a href="{dede:field name='typeurl'/}">
        {dede:field name='typename'/}
    </a>
    <div class="pro-subnav">
        {dede:arclist isweight='Y' orderby='weight' orderway='asc' row='100' titlelen='500'}
        <a href="[field:arcurl/]">- [field:title/]</a>
        {/dede:arclist}
    </div>
</li>
{/dede:channelartlist}
```

#### channelartlist标签支持currentstyle

`include\taglib\channelartlist.lib.php`

``` php
// 找到如下代码
$pv->Fields['typeurl'] = GetOneTypeUrlA($typeids[$i]);
// 再此代码下方添加如下代码
if($typeids[$i]['id'] == $refObj->TypeLink->TypeInfos['id'] || $typeids[$i]['id'] == $refObj->TypeLink->TypeInfos['topid'] ){
  $pv->Fields['currentstyle'] = $currentstyle ? $currentstyle : 'current';
}
else{
 $pv->Fields['currentstyle'] = '';
}
```

``` html
<!-- currentstyle='current' 中的current也可以修改为自己想要的类名即可 -->
{dede:channelartlist typeid='2' currentstyle='current'}
 <li class='{dede:field.currentstyle/}'><a href='{dede:field name='typeurl'/}'>{dede:field name='typename'/}</a></li>
{/dede:channelartlist}
```

#### 调用id为53的顶级栏目及相对应的产品，带高亮，同时调用id为57栏目的产品

```html
{dede:channelartlist typeid='53,53' currentstyle='active'}
<li class="{dede:field.currentstyle/}">
    <a href="{dede:field name='typeurl'/}">
        {dede:field name='typename'/}
    </a>
    <div class="pro-subnav">
        {dede:channel type='son' noself='yes' row='3'}
            <a href="[field:typelink/]">- [field:typename/]</a>
        {/dede:channel}
        {dede:arclist isweight='Y' typeid='57' orderby='weight' orderway='asc' row='100' titlelen='500'}
        <a href="[field:arcurl/]">- [field:title/]</a>
        {/dede:arclist}
    </div>
</li>
{/dede:channelartlist}
```

### TKD区域

#### 首页

``` html
<title>{dede:global.cfg_webname/}</title>
<meta name="description" content="{dede:global.cfg_description/}" />
<meta name="keywords" content="{dede:global.cfg_keywords/}" />
```

#### 列表/单页

``` html
<title>{dede:field.seotitle /}_{dede:global.cfg_webname/}</title>
<meta name="description" content="{dede:field.description function='html2text(@me)'/}" />
<meta name="keywords" content="{dede:field.keywords /}" />
```

#### 文章页

``` html
<title>{dede:field.title /}_{dede:global.cfg_webname/}</title>
<meta name="description" content="{dede:field.description function='html2text(@me)'/}" />
<meta name="keywords" content="{dede:field.keywords /}" />
```

### 点击量/浏览量

``` html
<script src="{dede:field name='phpurl'/}/count.php?view=yes&aid={dede:field name='id'/}&mid={dede:field name='mid'/}" type='text/javascript' language="javascript"></script>

<script src="/plus/count.php?view=yes&aid=[field:id/]&mid=[field:mid/]" type='text/javascript' language="javascript"></script>
```

