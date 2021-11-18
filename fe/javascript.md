# JavaScript

## 复制(IOS9 兼容)

```javascript
copyText(text) {
    // 数字没有 .length 不能执行selectText 需要转化成字符串
    const textString = text.toString();
    let input = document.querySelector('#copy-input');
    if (!input) {
    input = document.createElement('input');
    input.id = "copy-input";
    input.readOnly = "readOnly";        // 防止ios聚焦触发键盘事件
    input.style.position = "absolute";
    input.style.left = "-1000px";
    input.style.zIndex = "-1000";
    document.body.appendChild(input)
    }

    input.value = textString;
    // ios必须先选中文字且不支持 input.select();
    selectText(input, 0, textString.length);
    if (document.execCommand('copy')) {
    document.execCommand('copy');
        this.toast = true;
        if (this.toast) {
            setTimeout(() => {
                this.toast = false
            }, 1000);
        }
    }else {
    console.log('不兼容');
    }
    input.blur();
    // input自带的select()方法在苹果端无法进行选择，所以需要自己去写一个类似的方法
    // 选择文本。createTextRange(setSelectionRange)是input方法
    function selectText(textbox, startIndex, stopIndex) {
    if (textbox.createTextRange) {//ie
        const range = textbox.createTextRange();
        range.collapse(true);
        range.moveStart('character', startIndex);//起始光标
        range.moveEnd('character', stopIndex - startIndex);//结束光标
        range.select();//不兼容苹果
    } else {//firefox/chrome
        textbox.setSelectionRange(startIndex, stopIndex);
        textbox.focus();
    }
    }
}
```

## 图片下载

```javascript
downloadCode() {
    var oQrcode = document.querySelector('#qrcode')
    var url = oQrcode.src
    var a = document.createElement('a')
    var event = new MouseEvent('click')
    // 下载图名字
    a.download = '这是下载的文件名称'
    //url
    a.href = url
    //合成函数，执行下载
    a.dispatchEvent(event)
}
```

## JS 附加样式表

```javascript
var link = document.createElement("link");
link.rel = "stylesheet";
link.href = "/xx/xx2/public.css?v=" + new Date().getTime();
document.head.appendChild(link);
```

## 常用正则

```javascript
//去除空格
String.prototype.Trim = function () {
  return this.replace(/\s+/g, "");
};

//去除换行
function ClearBr(key) {
  key = key.replace(/<\/?.+?>/g, "");
  key = key.replace(/[\r\n]/g, "");
  return key;
}

//去除左侧空格
function LTrim(str) {
  return str.replace(/^\s*/g, "");
}

//去右空格
function RTrim(str) {
  return str.replace(/\s*$/g, "");
}

//去掉字符串两端的空格
function trim(str) {
  return str.replace(/(^\s*)|(\s*$)/g, "");
}

//去除字符串中间空格
function CTim(str) {
  return str.replace(/\s/g, "");
}

//是否为由数字组成的字符串
function is_digitals(str) {
  var reg = /^[0-9]*$/; //匹配整数
  return reg.test(str);
}

// 去除tab
function delTab(str) {
  str.replace(/\t/g, "");
}

// 剔除字符串中的大括号
function removeBlock(str) {
  if (str) {
    var reg = /^\{/gi;
    var reg2 = /\}$/gi;
    str = str.replace(reg, "");
    str = str.replace(reg2, "");
    return str;
  } else {
    return str;
  }
}
```

## 字符转换

```javascript
// 判断是否为中文
function isChinese(s){
  return /[\u4e00-\u9fa5]/.test(s);
  // 0x4E06 丆 0x4E64 乤 ,都不是汉字，但是在汉字区间内
  // return c <= 0x9FA5 && c >= 0x4E00 && (c != 0x4E06 && c != 0x4E64);
}

// 中文字符串转Unicode
toUnicode (s) {
  let str = "";
  for (let i = 0; i < s.length; i++) {
      str +="\\u"+s.charCodeAt(i).toString(16)+"\t";
  }
  return str;
}

// 十六进制Unicode编码 转换为 中文字符串
toStr(n){
  var str = "";
  var s = n.split('\\u');
  for(var i = 0;i < s.length;i++){
      str += String.fromCharCode(parseInt(s[i],16))+"\t";
  }
  return str;
}
var b = "\\u80dc    \\u591a    \\u8d1f    \\u5c11";
document.write(toStr(b));    // 胜    多    负    少

```

## 获取重复的UUID

``` javascript
/**
 * 随机生成数字
 *
 * 示例：生成长度为 12 的随机数：randomNumber(12)
 * 示例：生成 3~23 之间的随机数：randomNumber(3, 23)
 *
 * @param1 最小值 | 长度
 * @param2 最大值
 * @return int 生成后的数字
 */
export function randomNumber() {
  // 生成 最小值 到 最大值 区间的随机数
  const random = (min, max) => {
    return Math.floor(Math.random() * (max - min + 1) + min)
  }
  if (arguments.length === 1) {
    let [length] = arguments
  // 生成指定长度的随机数字，首位一定不是 0
    let nums = [...Array(length).keys()].map((i) => (i > 0 ? random(0, 9) : random(1, 9)))
    return parseInt(nums.join(''))
  } else if (arguments.length >= 2) {
    let [min, max] = arguments
    return random(min, max)
  } else {
    return Number.NaN
  }
}

/**
 * 随机生成uuid
 * @return string 生成的uuid
 */
export function randomUUID() {
  let chats = '0123456789abcdef'
  return randomString(32, chats)
}

/**
 * 随机生成字符串
 * @param length 字符串的长度
 * @param chats 可选字符串区间（只会生成传入的字符串中的字符）
 * @return string 生成的字符串
 */
export function randomString(length, chats) {
  if (!length) length = 1
  if (!chats) chats = '0123456789qwertyuioplkjhgfdsazxcvbnm'
  let str = ''
  for (let i = 0; i < length; i++) {
    let num = randomNumber(0, chats.length - 1)
    str += chats[num]
  }
  return str
}
```

## 下划线转驼峰

```javascript
export function underLine2CamelCase(string){
  return string.replace( /_([a-z])/g, function( all, letter ) {
    return letter.toUpperCase();
  });
}
```

## 简单防抖

``` javascript
/**
 * 简单实现防抖方法
 *
 * 防抖(debounce)函数在第一次触发给定的函数时，不立即执行函数，而是给出一个期限值(delay)，比如100ms。
 * 如果100ms内再次执行函数，就重新开始计时，直到计时结束后再真正执行函数。
 * 这样做的好处是如果短时间内大量触发同一事件，只会执行一次函数。
 *
 * @param fn 要防抖的函数
 * @param delay 防抖的毫秒数
 * @returns {Function}
 */
export function simpleDebounce(fn, delay = 100) {
  let timer = null
  return function () {
    let args = arguments
    if (timer) {
      clearTimeout(timer)
    }
    timer = setTimeout(() => {
      fn.apply(null, args)
    }, delay)
  }
}
```

## 替换文字(非正则)

``` javascript
/**
 * 不用正则的方式替换所有值
 * @param text 被替换的字符串
 * @param checker  替换前的内容
 * @param replacer 替换后的内容
 * @returns {String} 替换后的字符串
 */
export function replaceAll(text, checker, replacer) {
  let lastText = text
  text = text.replace(checker, replacer)
  if (lastText !== text) {
    return replaceAll(text, checker, replacer)
  }
  return text
}
```

## 过滤对象中为空的属性

``` javascript
/**
 * 过滤对象中为空的属性
 * @param obj
 * @returns {*}
 */
export function filterObj(obj) {
  if (!(typeof obj == 'object')) {
    return;
  }

  for ( let key in obj) {
    if (obj.hasOwnProperty(key)
      && (obj[key] == null || obj[key] == undefined || obj[key] === '')) {
      delete obj[key];
    }
  }
  return obj;
}
```

## 时间格式化

``` javascript
/**
 * 时间格式化
 * @param value
 * @param fmt
 * @returns {*}
 */
export function formatDate(value, fmt) {
  let regPos = /^\d+(\.\d+)?$/;
  if(regPos.test(value)){
    //如果是数字
    let getDate = new Date(value);
    let o = {
      'M+': getDate.getMonth() + 1,
      'd+': getDate.getDate(),
      'h+': getDate.getHours(),
      'm+': getDate.getMinutes(),
      's+': getDate.getSeconds(),
      'q+': Math.floor((getDate.getMonth() + 3) / 3),
      'S': getDate.getMilliseconds()
    };
    if (/(y+)/.test(fmt)) {
      fmt = fmt.replace(RegExp.$1, (getDate.getFullYear() + '').substr(4 - RegExp.$1.length))
    }
    for (let k in o) {
      if (new RegExp('(' + k + ')').test(fmt)) {
        fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? (o[k]) : (('00' + o[k]).substr(('' + o[k]).length)))
      }
    }
    return fmt;
  }else{
    value = value.trim();
    return value.substr(0,fmt.length);
  }
}
```

## 深度克隆对象、数组

``` javascript
/**
 * 深度克隆对象、数组
 * @param obj 被克隆的对象
 * @return 克隆后的对象
 */
export function cloneObject(obj) {
  return JSON.parse(JSON.stringify(obj))
}

```

## 常见校验方式

``` javascript
/**
 * 邮箱
 * @param {*} s
 */
export function isEmail (s) {
  return /^([a-zA-Z0-9._-])+@([a-zA-Z0-9_-])+((.[a-zA-Z0-9_-]{2,3}){1,2})$/.test(s)
}

/**
 * 手机号码
 * @param {*} s
 */
export function isMobile (s) {
  return /^1[0-9]{10}$/.test(s)
}

/**
 * 电话号码
 * @param {*} s
 */
export function isPhone (s) {
  return /^([0-9]{3,4}-)?[0-9]{7,8}$/.test(s)
}

/**
 * URL地址
 * @param {*} s
 */
export function isURL (s) {
  return /^http[s]?:\/\/.*/.test(s)
}
```