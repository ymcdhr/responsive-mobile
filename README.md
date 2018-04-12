# responsive-mobile
# 移动端多屏响应式布局方案

## 理解viewport，meta的设置
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

## 理解物理像素、CSS像素、位图像素
移动设备有几个概念性的像素需要理解。<br/>

物理像素：是指显示屏幕上具体的像素点，比如我们常说的iphone6的屏幕分辨率为：1334*750<br/>

DPR（devicePixelRatio）：dpr设备像素缩放比，是指物理像素和显示逻辑像素的缩放比例（web页面的显示逻辑像素就是css像素，因为web页面是按照css单位px来显示的）<br/>

CSS像素：在web页面中，css像素可以理解为设备显示时参考的虚拟像素。在iphone6上的css像素是：667*375，即它的DPR是2。<br/>

DPR = 物理像素/CSS像素 <br/>

如图所示，盗图请原谅：<br/>


所以，对于iphone6上的viewpot设置，width=device-width，即为width=375px；可以通过“alert(document.documentElement.clientWidth);”来验证。<br/>


位图像素：1个位图像素是指栅格图像(如：png, jpg, gif等)的最小单位。在移动设备上，一个位图像素对应于1个物理像素，图片才能得到完美清晰的显示。<br/>

1、例如，图片的原始分辨率是400*400，那么设备的物理像素也是400*400才能够得到最清晰的显示。如果是在iphone6上，dpr为2的情况，css像素即是200*200px对应400*400的原始图片。<br/>
所以在iphone6这种高清屏幕上，图片的原始分辨率计算方法为：w*h = （css width * dpr）*（css height * dpr）；国内大型的电商公司，一般会有专门的图片服务器。且上传高清图片后自动生成不同尺寸的图片<br/>
例如：下面400*400、200*200的图片通过参数来区分。<br/>
https://image2.suning.cn/uimg/b2c/newcatentries/0000000000-000000000721034170_1_400x400.jpg<br/>
https://image2.suning.cn/uimg/b2c/newcatentries/0000000000-000000000721034170_1_200x200.jpg<br/>

2、如果在iphone6这种高清屏幕上，css像素200*200的情况下，使用200*200的图片。那么会出现图片分辨率不够的情况，从而导致图片模糊，如下图：对于dpr=2的retina屏幕而言，1个位图像素对应于4个物理像素，由于单个位图像素不可以再进一步分割，所以只能就近取色，从而导致图片模糊(注意上述的几个颜色值)。盗图。<br/>

3、如果普通屏幕上，dpr为1时，css像素200*200的情况下，使用400*400的图片。就出现一个物理像素点对应4个位图像素点，所以它的取色也只能通过一定的算法(显示结果就是一张只有原图像素总数四分之一，我们称这个过程叫做downsampling)，肉眼看上去虽然图片不会模糊，但是会觉得图片缺少一些锐利度，或者是有点色差(但还是可以接受的)。盗图。<br/>

#### css定义示例：

1.img标签，src='https://image2.suning.cn/uimg/b2c/newcatentries/0000000000-000000000721034170_1_400x400.jpg'<br/>
```html
width: 200px;
height: 200px;
```
2.背景图片
```html
width: 200px;
height: 200px;
background-image: url(https://image2.suning.cn/uimg/b2c/newcatentries/0000000000-000000000721034170_1_400x400.jpg);
background-size: 200px 200px; // 或者: background-size: contain;
```
3.当然src可以使用js条件判断后拼接URL，css中的url也可以用less定义或者媒体查询。
4.使用scale进行缩放，也可以使用viewport中的scale，例如，显示200px宽的图片，将css宽写为400px，图片分辨率也为400px。
```html
width: 400px;
transform: scale(0.5);
```




