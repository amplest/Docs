# 蓝牙批量配网(BLE)

## 资料参考

- https://blog.csdn.net/qdh186/article/details/80680058
- https://www.jianshu.com/nb/40616486

## 业务代码(BLE)

```html
<template>
  <view class="ble">
    <template>
      <view class="tips flex f24px">
        <uni-icons type="info" color="#DFA93D" size="32"></uni-icons>
        <text v-if="pageStatus === 1" class="tips-txt"
          >仅支持迷你盒场景，搜索时请确认手机距离迷你棒3米范围内</text
        >
        <text v-if="pageStatus === 0 || pageStatus === 2" class="tips-txt"
          >可多选迷你盒蓝牙进行连接，通常展示为 <text>: XXXX</text></text
        >
        <text v-if="pageStatus === 3" class="tips-txt">蓝牙配对状态列表</text>
      </view>
    </template>
    <template v-if="pageStatus === 1">
      <view class="flex flex-center ble-scan">
        <view class="ble-scan-info">
          <image src="../static/yuan-bj.png" class="bg"></image>
          <image src="../static/yuan-dt.png" class="dt"></image>
          <text> 蓝牙设备 搜索中 </text>
        </view>
      </view>
    </template>
    <template v-if="pageStatus === 0">
      <view class="flex flex-center ble-status">
        <empty :src="failPic">
          <template v-slot:text>
            <view>
              <text> 未发现可连接的蓝牙设备 请确认设备位置与蓝牙状态 </text>
            </view>
          </template>
        </empty>
      </view>
      <view class="ble-rescan">
        <view class="btnStatus" @click="handleRescan"> 重新搜索 </view>
      </view>
    </template>
    <template v-if="pageStatus === 2">
      <view class="ble-list">
        <view
          class="flex flex-start ble-item"
          v-for="(item, index) in bleLIst"
          :key="index"
          @click="handleBleDetail(item,index)"
        >
          <image :src="item.checkStatus ? checkOn : check" mode=""></image>
          <view class="ble-item-txt flex flex-between">
            <view class="ble-item-txt-name"> {{item.name}} </view>
            <view class="ble-item-txt-rssi">
              <!-- 信号强度: {{item.RSSI}}% -->
            </view>
          </view>
        </view>
      </view>
      <view class="flex flex-between ble-list-btn">
        <view class="l" @click="handleRescan"> 重新搜索 </view>
        <view class="r" @click="handlePair"> 配对 </view>
      </view>
    </template>
    <template v-if="pageStatus === 3">
      <view class="ble-list">
        <view
          class="flex flex-between ble-item"
          v-for="(item, index) in bleListSelect"
          :key="index"
        >
          <view class="ble-item-txt"> {{item.name}} </view>
          <view
            :style="{color: item.pairStatus === 0 ? '#999': item.pairStatus === 1 ? 'green': 'red' }"
          >
            {{item.pairStatus === 0 ? '等待配对': item.pairStatus === 1 ?
            '配对成功': '配对失败'}}
          </view>
        </view>
      </view>

      <view class="ble-rescan">
        <template v-if="!getBtnStatus()">
          <view> 连接WIFI </view>
        </template>
        <template v-else>
          <view class="btnStatus" @click="handleConnectWiFi"> 连接WIFI </view>
        </template>
      </view>
    </template>
  </view>
</template>
```

```javascript
export default {
  data() {
    return {
      pageStatus: 1, // 0: 蓝牙未开启, 1:蓝牙搜索, 2:蓝牙列表, 3:配对列表
      failPic: require("../static/lanya.png"),
      check: require("../../notice/static/cb.png"),
      checkOn: require("../../notice/static/cb-act.png"),
      bleLIst: [], // 常规蓝牙列表
      bleListPairSuccess: [], // 配对成功列表
      bleListSelect: [], // 当前选择的蓝牙列表
      bleListToWifi: [], // 需要连接wifi的蓝牙
    };
  },
  onLoad() {
    this.bleInit();
    // this.onBLEConnectionStateChange();
  },
  onUnload() {
    uni.closeBluetoothAdapter();
    uni.hideLoading();
  },
  onHide() {
    this.stopBluetoothDevicesDiscovery();
    uni.hideLoading();
  },
  methods: {
    // 吐司
    toast(txt, icon) {
      uni.showToast({
        title: txt,
        icon: icon,
      });
    },
    // 重新扫描
    handleRescan() {
      this.bleLIst = [];
      this.pageStatus = 1;
      setTimeout(() => {
        this.bleInit();
      }, 300);
    },
    // 选择待配对蓝牙列表
    handleBleDetail(item, index) {
      uni.hideNavigationBarLoading();
      uni.setNavigationBarTitle({
        title: "蓝牙连接",
      });
      this.stopBluetoothDevicesDiscovery();
      this.bleLIst[index].checkStatus = !this.bleLIst[index].checkStatus;
    },
    // 配对
    handlePair() {
      let list = [];
      list = this.bleLIst.filter((ele) => {
        return ele.checkStatus === true;
      });
      if (list.length == 0) {
        uni.showToast({
          title: "蓝牙设备不可为空",
          icon: "none",
        });
        return;
      }
      this.bleListSelect = list;
      this.createBLEConnection(list);
    },
    // 按钮状态
    getBtnStatus() {
      let arr = this.bleListSelect;
      let size = this.bleListSelect.length;
      for (let i = 0; i < size; i++) {
        if (arr[i].pairStatus === 0) {
          return false;
        }
      }
      uni.hideLoading();
      return true;
    },
    // 链接wifi
    handleConnectWiFi() {
      if (this.getBtnStatus()) {
        let data = JSON.stringify(this.bleListSelect);
        uni.hideLoading();
        uni.navigateTo({
          url: `/bluetooth/wificonnection/wificonnection?list=${data}`,
        });
      } else {
        uni.hideLoading();
      }
    },
    // 停止搜索
    stopBluetoothDevicesDiscovery() {
      uni.stopBluetoothDevicesDiscovery({
        success: function (res) {
          console.log("停止成功", res);
        },
        fail(err) {
          console.log("停止失败", err);
        },
      });
    },
    // 初始化蓝牙功能
    bleInit() {
      //关闭当前的蓝牙模块
      uni.closeBluetoothAdapter({
        success: (res) => {
          console.log("关闭蓝牙模块成功", res);
          //重新打开蓝牙模块
          uni.openBluetoothAdapter({
            //初始化 蓝牙模块  成功 和 失败的回调
            success: (res) => {
              console.log("初始化蓝牙成功", res);
              this.bleScan();
            },
            fail: (err) => {
              console.log("初始化蓝牙是否开启:", err);
              this.pageStatus = 0;
            },
          });
        },
        fail: (err) => {
          console.log("关闭蓝牙模块出错", err);
        },
      });
    },
    // 扫描
    bleScan() {
      uni.getBluetoothAdapterState({
        success: (res) => {
          console.log("检测蓝牙是否开启成功", res);
          //discovering 是否正在搜索设备
          //available 蓝牙适配器是否可用
          if (res.available == false) {
            uni.showToast({
              title: "设备无法开启蓝牙连接",
              icon: "none",
            });
            return;
          }
          if (res.discovering == false && res.available) {
            uni.startBluetoothDevicesDiscovery({
              services: ["0000FE60-0000-1000-8000-00805F9B34FB"],
              success: (res) => {
                uni.showNavigationBarLoading();
                uni.setNavigationBarTitle({
                  title: "搜索附近蓝牙设备",
                });
                setTimeout(() => {
                  this.onBleDeviceFound();
                }, 1000);
              },
            });
          }
        },
        fail: (err) => {
          console.log("搜索蓝牙信息失败", err);
          this.stopBluetoothDevicesDiscovery();
        },
      });
    },
    // 监听新设备
    onBleDeviceFound() {
      let list = [];
      uni.onBluetoothDeviceFound((res) => {
        res.devices.forEach((device) => {
          // 剔除未知设备
          if (!device.name && !device.localName) {
            return;
          }
          device.checkStatus = false; // 选择状态
          device.pairStatus = 0; // 配对状态
          device.RSSI = Math.max(0, Number(device.RSSI + 100));
          list.push(device);
          console.log("list=>", list.length, list);
        });
        if (list.length > 0) {
          this.bleLIst = list.sort((a, b) => b.RSSI - a.RSSI);
          this.pageStatus = 2;
        }
      });
    },
    // 蓝牙配对
    createBLEConnection(list) {
      let that = this;
      uni.showLoading({
        title: "正在配对",
      });
      this.pageStatus = 3;
      list.forEach((item, index) => {
        that.tryConnect(0, false, list, index);
      });
      this.getBtnStatus();
    },
    // 重连机制
    tryConnect(count, flag, list, index) {
      let that = this;
      if (count < 100 && flag == false) {
        //第一次进方法
        //等待500毫秒执行一次连接
        setTimeout(function () {
          uni.createBLEConnection({
            deviceId: list[index].deviceId,
            success: (res) => {
              console.log("连接成功", list[index].name, count);
              if (res.errCode == 0) {
                that.tryConnect(1, true, list, index);
              }
            },
            fail: (err) => {
              console.log("连接失败:", list[index].name, count);
              that.tryConnect(count + 1, false, list, index);
            },
          });
        }, 1000);
        // return;
      }
      console.log("卡点:", count, flag);
      if (count == 100 && flag == false) {
        console.log("尝试连接:", list[index].name);
        that.$set(list[index], "pairStatus", 2);
        return;
      }
      if (flag == true) {
        console.log("连接成功:", list[index].name);
        that.$set(list[index], "pairStatus", 1);
        return;
      }
    },
  },
};
```

```css
@keyframes rotate {
  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}

page {
  background-color: #ffffff;
}

.tips {
  padding: 0 32px;
  height: 64px;
  line-height: 64px;
  background-color: rgba(223, 169, 61, 0.2);
  position: fixed;
  top: 0;
  width: 750px;

  &-txt {
    font-size: 24px;
    color: #dfa93d;
    font-weight: 400;
    padding-left: 12px;
    flex-grow: 1;
  }
}

.ble {
  height: calc(100vh - 64px);
  margin-top: 64px;
  overflow: hidden;

  &-scan {
    &-info {
      position: absolute;
      width: 540px;
      height: 540px;
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);

      text {
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        font-size: 36px;
        color: #4c87fe;
        text-align: center;
        line-height: 48px;
        font-weight: 600;
      }

      .bg {
        width: 540px;
        height: 540px;
      }

      .dt {
        width: 500px;
        height: 500px;
        position: absolute;
        left: 20px;
        top: 20px;
        z-index: 2;
        animation: rotate 3s linear infinite;
      }
    }
  }

  &-status {
    height: calc(100% - 176px);

    text {
      font-size: 28px;
      font-weight: 400;
      color: #484a4c;
      line-height: 48px;
    }
  }

  &-rescan {
    height: 88px;
    width: 750px;
    position: fixed;
    bottom: 88px;
    padding: 0 72px;

    view {
      width: 606px;
      height: 88px;
      border-radius: 44px;
      text-align: center;
      font-size: 28px;
      font-weight: 600;
      line-height: 88px;
      color: #babdc2;
      background: #e4e8ee;
    }
  }

  &-list {
    height: calc(100% - 176px);
    padding: 0 32px;
    overflow-y: auto;

    &-btn {
      height: 88px;
      width: 750px;
      padding: 0 32px;
      position: fixed;
      bottom: 88px;
      left: 0;

      .l,
      .r {
        width: 331px;
        height: 88px;
        border-radius: 44px;
        text-align: center;
        line-height: 88px;
        font-size: 28px;
        font-weight: 600;
      }

      .l {
        border: 1px solid #d4dae2;
        color: #484a4c;
      }

      .r {
        color: #ffffff;
        background: #4c87fe;
      }
    }
  }

  &-item {
    padding: 32px 0;
    align-items: center;
    position: relative;

    &::before {
      content: "";
      width: 100%;
      height: 1px;
      background: #f3f5f7;
      position: absolute;
      top: -1px;
    }

    image {
      width: 40px;
      height: 40px;
      flex-shrink: 0;
    }

    &-txt {
      padding-left: 8px;
      height: 28px;
      font-size: 28px;
      font-weight: 400;
      color: #2b2c2d;
      line-height: 28px;
      flex-grow: 1;

      &-rssi {
        color: #999999;
        font-size: 24px;
      }
    }
  }
}

.btnStatus {
  background: #4c87fe !important;
  color: #ffffff !important;
}
```

## 业务代码(WIFI)

```html
<template>
  <view class="wifi">
    <template v-if="pageStatus !== 0">
      <view class="tips flex f24px">
        <uni-icons type="info" color="#DFA93D" size="32"></uni-icons>
        <text v-if="pageStatus === 1" class="tips-txt">请选择要连接的WIFI</text>
        <text v-if="pageStatus === 2" class="tips-txt"
          >以下设备正在尝试连接到网络【WIFI名】</text
        >
      </view>
    </template>
    <template v-if="pageStatus === 0">
      <view class="flex flex-center wifi-status">
        <empty :src="failPic">
          <template v-slot:text>
            <view>
              <text> 未发现可连接的WIFI 请先将手机联网 </text>
            </view>
          </template>
        </empty>
      </view>
      <view class="wifi-rescan">
        <view @click="handleRescan" class="btnStatus"> 刷新 </view>
      </view>
    </template>
    <template v-if="pageStatus === 1">
      <view class="wifi-list">
        <view
          class="flex flex-start wifi-item"
          v-for="(item, index) in wifiList"
          :key="index"
          @click="handleWifiDetail(item,index)"
        >
          <image :src="item.checkStatus ? checkOn : check" mode=""></image>
          <view class="wifi-item-txt"> {{item.SSID}} </view>
        </view>
      </view>
      <view class="flex flex-between wifi-list-btn">
        <view class="l" @click="handleRescan"> 刷新 </view>
        <view class="r" @click="handleWifi"> 连接 </view>
      </view>
    </template>
    <template v-if="pageStatus === 2">
      <view class="wifi-list">
        <view
          class="flex flex-between wifi-item"
          v-for="(item, index) in wifiConnectableList"
          :key="index"
        >
          <view class="wifi-item-txt"> {{item.name}} </view>
          <view
            class="wifi-item-status"
            :style="{color: item.wifiStatus == 0 ? '#909498':item.wifiStatus == 1 ? 'green' : item.wifiStatus == 2 ? 'red':'#909498'}"
          >
            {{item.wifiStatus == 0 ? '写入成功':item.wifiStatus == 1 ? '已连接'
            : item.wifiStatus == 2 ? '连接失败':'等待联网'}}
          </view>
        </view>
      </view>
      <view class="flex flex-between wifi-list-btn">
        <template v-if="!getBtnStatus()">
          <view class="c"> 回到首页 </view>
        </template>
        <template v-else>
          <view class="c btnStatus" @click="handleUrl"> 回到首页 </view>
        </template>
      </view>
    </template>
    <!-- password -->
    <uni-popup ref="wifiDialog" type="dialog">
      <uni-popup-dialog
        mode="input"
        :isCloseClear="true"
        :showCancel="false"
        title="输入WIFI密码"
        placeholder="输入WIFI密码"
        @confirm="handleConnectWiFi"
      >
      </uni-popup-dialog>
    </uni-popup>
  </view>
</template>
```

```javascript
export default {
  data() {
    return {
      pageStatus: 0, // 0: wifi未开启, 1:wifi列表, 2:wifi状态列表
      failPic: require("../static/wifi-kong.png"),
      checkOn: require("../static/sbmc_yes.png"),
      check: require("../static/sbmc_no.png"),
      wifiList: [],
      actWifiData: null,
      bleList: [], // 可链接wifi的蓝牙列表
      passWord: "",
      wifiConnectableList: [], // 蓝牙连接配对成功,可配网列表
      wifiSucessList: [], // wifi连接状态
      servesData: null,
      characteristicsData: null,
    };
  },
  onLoad(option) {
    console.log("option=>", JSON.parse(option.list));
    this.bleList = JSON.parse(option.list);
    uni.getSystemInfo({
      success: (res) => {
        console.log("system info: ", res);
        const isIOS = res.platform === "ios";
        if (isIOS) {
          uni.showModal({
            title: "提示",
            content:
              "由于系统限制，iOS用户请手动进入系统WiFi页面，然后返回小程序。",
            showCancel: false,
            success: (res) => {
              this.wifiInit();
            },
          });
          return;
        }
        this.wifiInit();
      },
    });
  },
  onShow() {
    this.onBLECharacteristicValueChange();
  },
  onUnload() {
    this.closeWifi();
  },
  methods: {
    // 刷新
    handleRescan() {
      this.pageStatus = 1;
      uni.showLoading({
        title: "正在扫描Wifi",
      });
      this.wifiInit();
    },
    // 连接
    handleWifi() {
      let isSelectWifi = this.wifiList.filter(
        (ele) => ele.checkStatus === true
      );

      if (isSelectWifi.length) {
        this.$refs.wifiDialog.open();
      } else {
        uni.showToast({
          title: "请选择wifi",
          icon: "error",
        });
      }
    },
    // 回到首页
    handleUrl() {
      setTimeout(() => {
        uni.reLaunch({
          url: "../../pages/index/index",
        });
      }, 300);
    },
    // Wifi详情
    handleWifiDetail(item, index) {
      uni.hideNavigationBarLoading();
      this.closeWifi();
      this.wifiList.forEach((item) => {
        item.checkStatus = false;
      });
      this.actWifiData = item;
      this.wifiList[index].checkStatus = !this.wifiList[index].checkStatus;
    },
    // 初始化wifi
    wifiInit() {
      uni.hideLoading();
      console.log("wifi模块关闭成功");
      uni.startWifi({
        success: (res) => {
          console.log("wifi模块初始化成功!");
          this.wifiScan();
        },
        fail: (err) => {
          console.log("wifi模块初始化失败!");
          this.closeWifi();
          this.pageStatus = 0;
        },
      });
    },
    // 监听wifi列表
    wifiScan() {
      uni.showNavigationBarLoading();
      uni.setNavigationBarTitle({
        title: "搜索附近Wifi",
      });
      uni.getWifiList({
        success: (res) => {
          uni.onGetWifiList((res) => {
            let wifiList = res.wifiList
              .sort((a, b) => b.signalStrength - a.signalStrength)
              .map((wifi) => {
                const strength = Math.ceil(wifi.signalStrength * 4);
                const checkStatus = false;
                return Object.assign(wifi, {
                  strength,
                  checkStatus,
                });
              });
            this.wifiList = wifiList;
            if (wifiList.length > 0) {
              this.pageStatus = 1;
            }
          });
        },
        fail: (err) => {
          uni.hideNavigationBarLoading();
          this.pageStatus = 0;
        },
      });
    },
    // 关闭wifi模块
    closeWifi() {
      uni.stopWifi({
        success: (res) => {
          console.log("wifi关闭成功: ", res);
        },
        fail: (err) => {
          console.error("wifi关闭失败: ", err);
        },
      });
    },
    // 连接wifi
    handleConnectWiFi(e, txt) {
      this.pageStatus = 2;
      this.passWord = txt;
      this.$refs.wifiDialog.close();
      let sendString = this.actWifiData.SSID + "," + this.passWord;
      let buffer = new ArrayBuffer(sendString.length);
      let dataView = new Uint8Array(buffer);
      for (let i = 0; i < sendString.length; i++) {
        dataView[i] = sendString.charCodeAt(i);
      }
      console.log("通信数据=>", sendString, buffer);
      uni.showLoading({
        title: "设备正在配网",
      });
      this.getBleServes(this.bleList, buffer);
    },
    // 获取蓝牙服务信息
    getBleServes(list, buffer) {
      console.log("蓝牙批量连接获取服务开始!");
      let nList = list.filter((ele) => {
        return ele.pairStatus == 1;
      });
      nList.forEach((item) => {
        item.wifiStatus = null;
      });
      this.wifiConnectableList = nList;
      nList.forEach((item, index) => {
        this.bleRewrite(0, false, nList, index, buffer);
      });
      this.getBtnStatus();
    },
    // 蓝牙服务重写机制
    bleRewrite(count, flag, list, index, buffer) {
      let that = this;
      if (count < 100 && flag === false) {
        setTimeout(() => {
          uni.getBLEDeviceServices({
            deviceId: list[index].deviceId,
            success: (res) => {
              console.log("获取服务成功:", list[index].name);
              that.servesData = res;
              uni.getBLEDeviceCharacteristics({
                deviceId: list[index].deviceId,
                serviceId: that.servesData.services[0].uuid,
                success: (res) => {
                  console.log("getBLEDeviceCharacteristics=>", res);
                  that.characteristicsData = res;
                  uni.notifyBLECharacteristicValueChange({
                    state: true,
                    deviceId: list[index].deviceId,
                    serviceId: that.servesData.services[0].uuid,
                    characteristicId:
                      this.characteristicsData.characteristics[0].uuid,
                    success: function (res) {
                      console.log("启用notify成功");
                    },
                  });
                  that.bleRewrite(1, true, list, index, buffer);
                },
                fail: (err) => {
                  console.log("getBLEDeviceCharacteristics=>", err);
                  that.bleRewrite(count + 1, false, list, index, buffer);
                },
              });
            },
            fail: (err) => {
              console.log("获取服务失败:", list[index].name);
              that.bleRewrite(count + 1, false, list, index, buffer);
            },
          });
        }, 1000);
      }
      if (count == 100 && flag == false) {
        console.log("多次重启服务失败!");
        return;
      }
      if (flag == true) {
        that.bleWrite(list[index], buffer);
        return;
      }
    },
    // ble write
    bleWrite(item, buffer) {
      // 蓝牙相关的信息及写入应该都是在这里完成才行
      setTimeout(() => {
        uni.writeBLECharacteristicValue({
          deviceId: item.deviceId,
          serviceId: this.servesData.services[0].uuid,
          characteristicId: this.characteristicsData.characteristics[0].uuid,
          value: buffer,
          success: function (res) {
            console.log("写入完成!");
          },
          fail: (err) => {
            console.log("写入失败!");
          },
        });
      }, 500);
    },
    // buffet to string
    buf2string(buffer) {
      var arr = Array.prototype.map.call(new Uint8Array(buffer), (x) => x);
      var str = "";
      for (var i = 0; i < arr.length; i++) {
        str += String.fromCharCode(arr[i]);
      }
      return str;
    },
    // 监听设备返回信息
    onBLECharacteristicValueChange() {
      uni.onBLECharacteristicValueChange((res) => {
        console.log("状态", this.buf2string(res.value));
        this.wifiSucessList.push(res);
        // 写入成功: 0; 连接成功: 1; 密码错误: 2;
        this.wifiConnectableList.forEach((item, index) => {
          if (res.deviceId === item.deviceId) {
            item.wifiStatus = Number(this.buf2string(res.value));
            this.$set(
              this.wifiConnectableList[index],
              "wifiStatus",
              item.wifiStatus
            );
          }
        });
        this.wifiSucessList = this.unique(this.wifiSucessList, res.value);
        this.getBtnStatus();
        console.log(
          "onBLECharacteristicValueChange=>",
          this.wifiSucessList,
          this.wifiConnectableList,
          res
        );
      });
    },
    unique(narr, status) {
      for (var i = 0; i < narr.length - 1; i++) {
        for (var j = i + 1; j < narr.length; j++) {
          if (narr[i].deviceId == narr[j].deviceId) {
            narr[i].value = status;
            narr.splice(j, 1);
            j--;
          }
        }
      }
      return narr;
    },
    getBtnStatus() {
      let arr = this.wifiConnectableList;
      let size = this.wifiConnectableList.length;
      for (let i = 0; i < size; i++) {
        if (arr[i].wifiStatus === 0 || arr[i].wifiStatus === null) {
          return false;
        }
      }
      uni.hideLoading();
      return true;
    },
  },
};
```

```css
page {
  background-color: #ffffff;
}

.tips {
  padding: 0 32px;
  height: 64px;
  line-height: 64px;
  background-color: rgba(223, 169, 61, 0.2);
  position: fixed;
  top: 0;
  width: 750px;

  &-txt {
    font-size: 24px;
    color: #dfa93d;
    font-weight: 400;
    padding-left: 12px;
    flex-grow: 1;
  }
}

.wifi {
  height: calc(100vh - 64px);
  margin-top: 64px;
  overflow: hidden;

  &-scan {
    &-info {
      position: absolute;
      width: 540px;
      height: 540px;
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);

      text {
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        font-size: 36px;
        color: #4c87fe;
        text-align: center;
        line-height: 48px;
        font-weight: 600;
      }

      .bg {
        width: 540px;
        height: 540px;
      }

      .dt {
        width: 500px;
        height: 500px;
        position: absolute;
        left: 20px;
        top: 20px;
        z-index: 2;
        animation: rotate 3s linear infinite;
      }
    }
  }

  &-status {
    height: calc(100% - 176px);

    text {
      font-size: 28px;
      font-weight: 400;
      color: #484a4c;
      line-height: 48px;
    }
  }

  &-rescan {
    height: 88px;
    width: 750px;
    position: fixed;
    bottom: 88px;
    padding: 0 72px;

    view {
      width: 606px;
      height: 88px;
      border-radius: 44px;
      text-align: center;
      font-size: 28px;
      font-weight: 600;
      line-height: 88px;
      color: #babdc2;
      background: #e4e8ee;
    }
  }

  &-list {
    height: calc(100% - 176px);
    padding: 0 32px;
    overflow-y: auto;

    &-btn {
      height: 88px;
      width: 750px;
      padding: 0 32px;
      position: fixed;
      bottom: 88px;
      left: 0;

      .l,
      .r {
        width: 331px;
        height: 88px;
        border-radius: 44px;
        text-align: center;
        line-height: 88px;
        font-size: 28px;
        font-weight: 600;
      }

      .l {
        border: 1px solid #d4dae2;
        color: #484a4c;
      }

      .r {
        color: #ffffff;
        background: #4c87fe;
      }

      .c {
        width: 686px;
        height: 88px;
        background: #e4e8ee;
        border-radius: 100px;
        font-size: 28px;
        font-weight: 600;
        text-align: center;
        line-height: 88px;
      }
    }
  }

  &-item {
    padding: 32px 0;
    align-items: center;
    position: relative;

    &::before {
      content: "";
      width: 100%;
      height: 1px;
      background: #f3f5f7;
      position: absolute;
      top: -1px;
    }

    image {
      width: 40px;
      height: 40px;
    }

    &-txt {
      padding-left: 8px;
      height: 28px;
      font-size: 28px;
      font-weight: 400;
      color: #2b2c2d;
      line-height: 28px;
    }

    &-status {
      font-size: 28px;
      font-weight: 400;
      color: #909498;
    }
  }
}

.btnStatus {
  background: #4c87fe !important;
  color: #ffffff !important;
}
```
