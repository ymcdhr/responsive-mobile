# responsive-mobile
# 移动端多屏响应式布局方案

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```
```
viewport：用户网页的可视区域。

width：控制 viewport 的大小，可以指定的一个值，如 600，或者特殊的值，如 device-width 为设备的宽度（单位为缩放为 100% 时的 CSS 的像素）。

其中，width的默认值大多为980（跟设备和浏览器有关）。如果不指定宽度，在移动设备上会出现滚动条，如下图。

initial-scale：初始缩放比例，也即是当页面第一次 load 的时候缩放比例。在设置了initial-scale=1 之后，我们可以以1:1 的比例进行页面设计。

maximum-scale：允许用户缩放到的最大比例。

minimum-scale：允许用户缩放到的最小比例。

user-scalable：用户是否可以手动缩放。
```


如下图，使用viewport的对比，chrome模拟器，在iPhone上效果类似。


![](https://raw.githubusercontent.com/ymcdhr/responsive-mobile/master/imgs%20(5).png)
