## 虚拟定位方式

在iOS设备上实现虚拟定位大致有这几种方式：

- 使用越狱设备通过hook相关App的定位方法来实现虚拟定位
- 使用Xcode模拟定位
- 使用第三方工具（比如[爱思助手](https://www.i4.cn/news_detail_31175.html)、[iMyFone AnyTo](https://tw.imyfone.com/location-changer/guide/#step1)等）中的虚拟定位功能来实现虚拟定位
- 使用外接设备（比如车载蓝牙等）实现虚拟定位

对普通用户来说，使用第三方工具虚拟定位，是最便捷也是最常用的。就拿爱思助手来说，只需要在Mac上安装爱思助手软件，用数据线连接iPhone，就可以使用虚拟定位功能，设置一下经纬度就会立即在手机上生效。

## 检测虚拟定位

针对虚拟定位的检测，在查阅资料的过程中，发现很多人提到虚拟定位无法获取海拔值和海拔精度，为了验证这一点，我们从位置信息入手，将不同定位获取到的位置信息打印出来进行对比。

下面是使用iOS15.4.1设备，分别通过真实定位、Xcode虚拟定位、爱思助手虚拟定位、iMyFone AnyTo虚拟定位获取到的位置信息：

|                                                   | 真实定位           | Xcode虚拟定位 | 爱思助手虚拟定位 | iMyFone AnyTo虚拟定位 |
| ------------------------------------------------- | ------------------ | ------------- | ---------------- | --------------------- |
| location.coordinate.longitude                     | 116.40956810902968 | -0.1337       | 114.151041893    | 114.16255             |
| location.coordinate.latitude                      | 40.03917982159124  | 51.50998      | 22.276178143     | 22.279356             |
| location.altitude                                 | 43.542202949523926 | 0.0           | 0.0              | 0.0                   |
| location.horizontalAccuracy                       | 35.0               | 5.0           | 5.0              | 5.0                   |
| location.verticalAccuracy                         | 8.337175369262695  | -1.0          | -1.0             | -1.0                  |
| location.course                                   | -1.0               | -1.0          | -1.0             | -1.0                  |
| location.speed                                    | -1.0               | -1.0          | -1.0             | -1.0                  |
| location.speedAccuracy                            | -1.0               | -1.0          | -1.0             | -1.0                  |
| location.sourceInformation?.isSimulatedBySoftware | false              | true          | true             | true                  |
| location.sourceInformation?.isProducedByAccessory | false              | false         | false            | false                 |

> 上面的`location`是一个`CLLocation`对象

从上面的数据来看，真实定位和虚拟定位在`altitude`、`horizontalAccuracy`、`verticalAccuracy`、`isSimulatedBySoftware`这些值上是有差异的。

其中`isSimulatedBySoftware`和`isProducedByAccessory`这两个属性仅在iOS15及以上的系统中可用：

- [isSimulatedBySoftware](https://developer.apple.com/documentation/corelocation/cllocationsourceinformation/3861807-issimulatedbysoftware)：是否使用设备上的软件模拟定位
- [isProducedByAccessory](https://developer.apple.com/documentation/corelocation/cllocationsourceinformation/3861806-isproducedbyaccessory)：是否从外接设备获取定位

如果要检测虚拟定位，可以从下面来考虑：

- 对于iOS15及以上的系统，直接使用系统提供的`isSimulatedBySoftware`和`isProducedByAccessory`即可
- 对于iOS15以下的系统，可以使用位置特征值`altitude`、`horizontalAccuracy`、`verticalAccuracy`来判断

方法如下：

```
func isFakeLocation(_ location: CLLocation) -> Bool {
    if #available(iOS 15.0, *) {
        return location.sourceInformation?.isSimulatedBySoftware == true || location.sourceInformation?.isProducedByAccessory == true
    } else {
        return location.altitude == 0.0 && location.verticalAccuracy == -1 && location.horizontalAccuracy == 5.0
    }
}
```

> 注意：这种方式只对Xcode和第三方工具的虚拟定位检测有效，其他方式暂未尝试。

## 参考资料

https://developer.apple.com/documentation/corelocation/cllocationsourceinformation

https://mp.weixin.qq.com/s/5ivzxYjiZ74px6ibwFCH8g



