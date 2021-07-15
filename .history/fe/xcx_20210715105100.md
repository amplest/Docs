# 小程序

## 优秀解决方案

- [F2 移动端可视化方案](https://f2.antv.vision/zh)

## Flex布局

### 元素

上下结构, 下方自适应高度

``` html
<view class="ms-flex ms-main--center container">
  <view class="ms-flex ms-dir--left container-1">
    <view class="container-1-top">这是第一个元素</view>
    <view class="ms-flex ms-main--center container-1-bottom">
      <view class="container-1-bottom-inner">
        <view wx:for="{{20}}" wx:key="index">这是最里面的元素</view>
      </view>
    </view>
  </view>
</view>
```

``` css
.ms-flex {
  display: flex;
}
.ms-main--center {
  justify-content: center;
}
.ms-dir--left {
  flex-direction: column;
}
.ms-cross--center {
  align-items: center;
}
.ms-cross--stretch {
  align-items: stretch;
}

.container {
  height: 100vh;
  background: #aaa;
  padding: 20rpx;
  box-sizing: border-box;
}

.container-1 {
  height: 100%;
  width: 100%;
  background-color: #bbb;
  padding: 20rpx;
  box-sizing: border-box;
}

.container-1-top {
  background-color: #ccc;
  height: 120rpx;
  padding: 20rpx;
  box-sizing: border-box;
  margin-bottom: 20rpx;
}

.container-1-bottom {
  background-color: #ccc;
  padding: 20rpx;
  height: 100%;
}

.container-1-bottom-inner {
  background: #ddd;
  width: 100%;
  height: 100%;
  overflow-y: auto;
}
```

### space-between

多行实现列均分, 使用space-between的时碰到最后一行是单数的情况下, 中间就会有空缺, 解决方案

``` html
<view class="list">
    <image src="xx"></image>
    <image src="xx"></image>
    <image src="xx"></image>
</view>
```

``` css
.list {
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
}
.list image {
    width: 375rpx;
    height: 375rpx;
}
/*最重要的部分*/
.list:after {
    content: '';
    width: 375rpx;
}
```

## 刘海胡子

```css
padding-top: constant(safe-area-inset-top);
padding-top: env(safe-area-inset-top);
padding-right: constant(safe-area-inset-right);
padding-right: env(safe-area-inset-right);
padding-bottom: env(safe-area-inset-bottom);
padding-bottom: constant(safe-area-inset-bottom);
padding-bottom: calc(constant(safe-area-inset-bottom) + 50px);
padding-bottom: calc(env(safe-area-inset-bottom) + 50px);
padding-left: constant(safe-area-inset-left);
padding-left: env(safe-area-inset-left);
```

## WXS

### replace()

```javascript
str.replace(getRegExp('-', 'g'), '/')

// 时间对比
var timeCompare = function(subscribe_stime,start_time){
    var new_subscribe_stime = subscribe_stime.replace(getRegExp('-', 'g'), '/');
    var new_start_time = start_time.replace(getRegExp('-', 'g'), '/');

    var compare1 = getDate() > getDate(new_subscribe_stime);
    var compare2 = getDate(new_start_time) > getDate();
    var compare = compare1 && compare2 ;
    return compare;
}
```

使用`getDate()`在IOS系统中存在兼容问题, IOS中`getDate()`只支持`2020/02/12 12:00:00`格式, 结局方案如下

``` javascript
var timeCompare = function(subscribe_stime,start_time){
    var new_subscribe_stime = subscribe_stime.replace(getRegExp('-', 'g'), '/');
    var new_start_time = start_time.replace(getRegExp('-', 'g'), '/');
    var compare1 = getDate() > getDate(new_subscribe_stime);
    var compare2 = getDate(new_start_time) > getDate();
    var compare = compare1 && compare2 ;
    return compare;
}
```

## 音频(audio)

``` html
 <audio controls poster="{{info.default_image}}" name="{{info.goods_name}}" author="{{info.details}}" src="{{info.audio_url}}" id="myAudio" bindtap='onAudio'></audio>
```

``` javascript
onReady() {
    this.audioCtx = wx.createAudioContext('myAudio')
},
onAudio() {
    this.setData({
      isPlay: !this.data.isPlay
    })
    if (this.data.isPlay) {
      this.audioCtx.play();
    } else {
      this.audioCtx.pause();
    }
}
```

## 地理位置重复授权

```javascript
onLoad() {
wx.getLocation({
  type: "wgs84",
  success(res) {
    //如果首次授权成功则执行地图定位操作，具体实现代码与此文无关，就不贴出
  },
  fail: function(res) {
    //授权失败
    wx.getSetting({
      //获取用户的当前设置，返回值中只会出现小程序已经向用户请求过的权限
      success: function(res) {
        //成功调用授权窗口
        var statu = res.authSetting;
        if (!statu["scope.userLocation"]) {
          //如果设置中没有位置权限
          wx.showModal({
            //弹窗提示
            title: "是否授权当前位置",
            content:
              "需要获取您的地理位置，请确认授权，否则地图功能将无法使用",
            success: function(tip) {
              if (tip.confirm) {
                wx.openSetting({
                  //点击确定则调其用户设置
                  success: function(data) {
                    if (data.authSetting["scope.userLocation"] === true) {
                      //如果设置成功
                      wx.showToast({
                        //弹窗提示
                        title: "授权成功",
                        icon: "success",
                        duration: 1000
                      });
                      wx.getLocation({
                        //通过getLocation方法获取数据
                        type: "wgs84",
                        success(res) {
                          //成功的执行方法
                        }
                      });
                    }
                  }
                });
              } else {
                //点击取消按钮，则刷新当前页面
                wx.redirectTo({
                  //销毁当前页面，并跳转到当前页面
                  url: "index" //此处按照自己的需求更改
                });
              }
            }
          });
        }
      },
      fail: function(res) {
        wx.showToast({
          title: "调用授权窗口失败",
          icon: "success",
          duration: 1000
        });
      }
    });
  }
});
}
```

## 时间戳转换

``` javascript
let examList = res.data.data.lists;
examList.forEach((e) => {
    e.time = app.util.formatTime(new Date(Number(e.createtime)));
    e.date = app.util.formatTime(new Date(Number(e.createtime)));
    e.time = new Date(Number(e.createtime) * 1000).toLocaleDateString();
});
```

## 兄弟元素问题

``` css
:nth-of-type(2) /* 同级第二个兄弟元素*/
```

## 原生picker问题

小程序picker组件中使用fileds中的year的时候会出现显示错误，如2020年显示20

给value默认值的时候如果是使用`new Date().getFullYear()`的话需要使用`String`方法转换下，使用`start/end`的时候也是同理(针对ios)

---

# 💎 项目

## 蓝牙配网(uni-app)
