# responsive-mobile
# 移动端多屏响应式布局方案

### [一、理解viewport，meta的设置](#理解viewportmeta的设置)
### [二、理解物理像素，CSS像素，位图像素](#理解物理像素css像素位图像素)
### [三、rem的响应式方案](#rem的响应式方案)
### [四、高清屏border的适配](#高清屏border的适配)
### [五、完整方案示例](#完整方案示例)

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

## 理解物理像素，CSS像素，位图像素
移动设备有几个概念性的像素需要理解。<br/>

#### 1、物理像素：是指显示屏幕上具体的像素点，比如我们常说的iphone6的屏幕分辨率为：1334*750<br/>

DPR（devicePixelRatio）：dpr设备像素缩放比，是指物理像素和显示逻辑像素的缩放比例（web页面的显示逻辑像素就是css像素，因为web页面是按照css单位px来显示的）<br/>

#### 2、CSS像素：在web页面中，css像素可以理解为设备显示时参考的虚拟像素。在iphone6上的css像素是：667*375，即它的DPR是2。<br/>

#### 3、DPR = 物理像素/CSS像素 <br/>

如图所示，盗图请原谅：<br/>
![](https://github.com/ymcdhr/responsive-mobile/blob/master/imgs%20(3).png)

所以，对于iphone6上的viewpot设置，width=device-width，即为width=375px；可以通过“alert(document.documentElement.clientWidth);”来验证。<br/>


#### 4、位图像素：1个位图像素是指栅格图像(如：png, jpg, gif等)的最小单位。在移动设备上，一个位图像素对应于1个物理像素，图片才能得到完美清晰的显示。<br/>

1、例如，图片的原始分辨率是400*400，那么设备的物理像素也是400*400才能够得到最清晰的显示。如果是在iphone6上，dpr为2的情况，css像素即是200*200px对应400*400的原始图片。<br/>
所以在iphone6这种高清屏幕上，图片的原始分辨率计算方法为：w*h = （css width * dpr）*（css height * dpr）；国内大型的电商公司，一般会有专门的图片服务器。且上传高清图片后自动生成不同尺寸的图片<br/>
例如：下面400*400、200*200的图片通过参数来区分。<br/>
https://image2.suning.cn/uimg/b2c/newcatentries/0000000000-000000000721034170_1_400x400.jpg<br/>
https://image2.suning.cn/uimg/b2c/newcatentries/0000000000-000000000721034170_1_200x200.jpg<br/>

2、如果在iphone6这种高清屏幕上，css像素200*200的情况下，使用200*200的图片。那么会出现图片分辨率不够的情况，从而导致图片模糊，如下图：对于dpr=2的retina屏幕而言，1个位图像素对应于4个物理像素，由于单个位图像素不可以再进一步分割，所以只能就近取色，从而导致图片模糊(注意上述的几个颜色值)。盗图。<br/>
![](https://github.com/ymcdhr/responsive-mobile/blob/master/imgs%20(4).png)

3、如果普通屏幕上，dpr为1时，css像素200*200的情况下，使用400*400的图片。就出现一个物理像素点对应4个位图像素点，所以它的取色也只能通过一定的算法(显示结果就是一张只有原图像素总数四分之一，我们称这个过程叫做downsampling)，肉眼看上去虽然图片不会模糊，但是会觉得图片缺少一些锐利度，或者是有点色差(但还是可以接受的)。盗图。<br/>

#### css定义示例：

1.img标签，src='https://image2.suning.cn/uimg/b2c/newcatentries/0000000000-000000000721034170_1_400x400.jpg'<br/>
```style
width: 200px;
height: 200px;
```
2.背景图片
```style
width: 200px;
height: 200px;
background-image: url(https://image2.suning.cn/uimg/b2c/newcatentries/0000000000-000000000721034170_1_400x400.jpg);
background-size: 200px 200px; // 或者: background-size: contain;
```
3.当然src可以使用js条件判断后拼接URL，css中的url也可以用less定义或者媒体查询。
4.使用scale进行缩放，也可以使用viewport中的scale，例如，显示200px宽的图片，将css宽写为400px，图片分辨率也为400px。
```style
width: 400px;
transform: scale(0.5);
```
## rem的响应式方案
#### REM的定义：
rem是相对于根元素<html>来设置字体大小的，这样就意味着，我们只需要在根元素确定一个参考值，在根元素中设置多大的字体，这完全可以根据您自己的需求。<br/>

#### REM与EM、PX的区别：
PX：像素，比较精确的单位，但不好做响应式布局<br/>
EM：以父节点font-size大小为参考点，标准不统一，容易造成混乱<br/>

#### REM支持的浏览器：
支持的浏览器还是蛮多的，比如：Mozilla Firefox 3.6+、Apple Safari 5+、Google Chrome、IE9+和Opera11+。只是可怜的IE6-8无法支持。<br/>

#### REM比例关系：

#### REM使用方法示例：
1.html中默认的 font-size:16px; 也就是 1rem = 16px<br/>
2.以设计宽度为750px为例，那么定义 font-size:50px; 那么 1rem = 50px，比例为：750/50=15<br/>
3.定义页面中的尺寸，就是: 宽度/50 rem（比例为15）。例如：设计稿中dom（20px）的尺寸为：20px/50=0.4rem<br/>
4.针对不同屏幕尺寸，按同比例设置font-size（比例仍为15）。相当于1rem 变成对应大小（font-size变小多少，1rem就同比例变小多少）。<br/>
5.例如：<br/>
屏幕宽度为750px，则html设置的font-size为：750/15 = 50px，设计稿中dom（20px）的尺寸为：20px/50=0.4rem，1rem = 50px<br/>
屏幕宽度为320px，则html设置的font-size为：320/15 = 21.33px....，设计稿中dom（20px）的尺寸依然为：20px/50=0.4rem，1rem = 320/750*50px<br/>

快速计算方法：rem = px / html 的 fontsize，例如15rem = 750px/50，0.4rem = 20/50；<br/>

如下比例关系：<br/>
![](https://github.com/ymcdhr/responsive-mobile/blob/master/imgs%20(1).png)


#### JS动态设置width：
1、html中的font-size按照固定的比例来取值，比如比例是15，那么750px的尺寸，字体就是750/15=50<br/>

2、orientationchange事件，用户翻转屏幕事件，可能存在兼容性问题：不过测试的最新的Iphone端，Safari、QQ浏览器、微信、UC浏览器都支持，时间是2018年4月。<br/>

3、JS动态设置font-size可以适配所有中间尺寸。<br/>

```html
<script>

   (function (doc, win) {

       var docEl = doc.documentElement,

               resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',

               recalc = function () {

                   var clientWidth = docEl.clientWidth;

                   if (!clientWidth) return;

                   docEl.style.fontSize = clientWidth / 15 + 'px';

               };

       if (!doc.addEventListener) return;

       win.addEventListener(resizeEvt, recalc, false);

       doc.addEventListener('DOMContentLoaded', recalc, false);

   })(document, window);

</script>
```


CSS固定样式：以下场景包含了移动端设备绝大多数屏幕尺寸。<br/>
rem标准，750px 为设计基准，除以字体大小基数font-size：50px，即 10px/50 = 0.2rem，以此类推,字体还是 px <br/>

```style
html{font-size:50px;}

body{font-size:24px;}

@media screen and (min-width:320px){

    html{font-size:21.333333333333332px;}

    body{font-size:12px;}

}

@media screen and (min-width:360px){

    html{font-size:24px;}

    body{font-size:12px;}

}

@media screen and (min-width:375px){

    html{font-size:25px;}

    body{font-size:12px;}

}

@media screen and (min-width:384px){

    html{font-size:25.6px;}

    body{font-size:14px;}

}

@media screen and (min-width:400px){

    html{font-size:26.666666666666668px;}

    body{font-size:14px;}

}

@media screen and (min-width:414px){

    html{font-size:27.6px;}

    body{font-size:14px;}

}

@media screen and (min-width:424px){

    html{font-size:28.266666666666667px;}

    body{font-size:14px;}

}

@media screen and (min-width:480px){

    html{font-size:32px;}

    body{font-size:15.36px;}

}

@media screen and (min-width:540px){

    html{font-size:36px;}

    body{font-size:17.28px;}

}

@media screen and (min-width:720px){

    html{font-size:48px;}

    body{font-size:23.04px;}

}

@media screen and (min-width:750px){

    html{font-size:50px;}

    body{font-size:24px;}

}
```

## 高清屏border的适配
1、设计师想要的boder：1px可能是指最小的物理像素，而不一定是最小的css像素。在iphone6这种retina高清屏幕下，dpr为2时最小的物理像素是0.5px。<br/>
2、然而在dpr为1的手机屏幕上，最小的物理像素是1px。如果在css中也写成0.5px，在ios7以下，android等其他系统里，0.5px会被当成为0px处理。<br/>
3、所以可以使用scale的方法来缩小比例，但是这样hack实在是不够通用(如：圆角等)，写起来也麻烦。<br/>
```style
.scale{
    position: relative;
}
.scale:after{
    content:"";
    position: absolute;
    bottom:0px;
    left:0px;
    right:0px;
    border-bottom:1px solid #ddd;
    -webkit-transform:scaleY(.5);
    -webkit-transform-origin:0 0;
}
```
4、更加通用的办法是在viewport中定义scale<br/>

## 完整方案示例
#### 以iphone6为例，dpr为2，clientWidth为375px：<br/>

1、使用viewprot，将CSS像素和物理像素保持一致：<br/>
'width=' + dpr * clientWidth + '

2、再按照dpr比例进行缩放：<br/>
initial-scale=' + scale + ',maximum-scale=' + scale + ', minimum-scale=' + scale + ',user-scalable=no'

3、图片的分辨率使用还是按照正常的css写，比如：设计尺寸是200*200px，css按照正常的rem取值，图片粉饼率应该是400*400；<br/>
4、border最小写1px即可。<br/>


#### 示例代码：<br/>
```
<html>
    <head>

        <meta name="viewport" content="width=device-width, initial-scale=1.0">
            

        <!-- <style>
            /* rem reset */
            html{font-size:50px;}

            body{font-size:24px;}

            @media screen and (min-width:320px){

                html{font-size:21.333333333333332px;}

                body{font-size:12px;}

            }

            @media screen and (min-width:360px){

                html{font-size:24px;}

                body{font-size:12px;}

            }

            @media screen and (min-width:375px){

                html{font-size:25px;}

                body{font-size:12px;}

            }

            @media screen and (min-width:384px){

                html{font-size:25.6px;}

                body{font-size:14px;}

            }

            @media screen and (min-width:400px){

                html{font-size:26.666666666666668px;}

                body{font-size:14px;}

            }

            @media screen and (min-width:414px){

                html{font-size:27.6px;}

                body{font-size:14px;}

            }

            @media screen and (min-width:424px){

                html{font-size:28.266666666666667px;}

                body{font-size:14px;}

            }

            @media screen and (min-width:480px){

                html{font-size:32px;}

                body{font-size:15.36px;}

            }

            @media screen and (min-width:540px){

                html{font-size:36px;}

                body{font-size:17.28px;}

            }

            @media screen and (min-width:720px){

                html{font-size:48px;}

                body{font-size:23.04px;}

            }

            @media screen and (min-width:750px){

                html{font-size:50px;}

                body{font-size:24px;}

            }
        </style> -->

        <style>
            /* reset */
            body,ul{
                margin: 0;
                padding: 0;
            }

            /* style */
            body{
                padding: .4rem;
            }
            .font{
                font-size: 1rem;
            }
            
            .border{
                margin-top: .2rem;
                border-top: 0.5px solid #cccccc;
                padding: .2rem 0;
            }
            ul{
                overflow: hidden;
            }

            li{
                float: left;
                list-style: none;
            }

            .img{
                width: 4.4rem;
                margin-right: .3rem;
            }
        
        </style>

        
        <script>
                
            (function (doc, win) {
        
                var docEl = doc.documentElement;
                var clientWidth = docEl.clientWidth,

                    resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
    
                    recalc = function () {
                        // clientWidth屏幕宽度为css像素
                        var metaEl = document.querySelector('meta[name="viewport"]'),
                            dpr = window.devicePixelRatio || 1,
                            scale = 1 / dpr;

                        if (!clientWidth) return;

                        // 设置viewport，进行缩放，达到高清效果
                        metaEl.setAttribute('content', 'width=' + dpr * clientWidth + ',initial-scale=' + scale + ',maximum-scale=' + scale + ', minimum-scale=' + scale + ',user-scalable=no');

                        // 15为rem比例，要求css中定义的尺寸按照这个单位来写
                        docEl.style.fontSize = clientWidth * dpr/ 15 + 'px';
    
                    };
        
                if (!doc.addEventListener) return;
        
                // 屏幕翻转事件
                win.addEventListener(resizeEvt, recalc, false);
                
                // dom加载结束事件
                doc.addEventListener('DOMContentLoaded', recalc, false);
        
            })(document, window);
        
        </script>

    </head>

    <body>                    
        
        <div>
            <ul>
                <li>
                    <img class="img" src="//image1.suning.cn/uimg/b2c/newcatentries/0070133963-000000010242758275_1_400x400.jpg">                
                </li>
                <li>
                    <img class="img" src="//image1.suning.cn/uimg/b2c/newcatentries/0070133963-000000010242758275_1_400x400.jpg">                
                </li>
                <li>
                    <img class="img" src="//image1.suning.cn/uimg/b2c/newcatentries/0070133963-000000010242758275_1_400x400.jpg">                
                </li>
            </ul>
            <div class="border">
                <a class="font">
                    这里是一段文字~
                </a>
            </div>
        </div>
        <div>
            <ul>
                <li>
                    <img class="img" src="//image1.suning.cn/uimg/b2c/newcatentries/0070133963-000000010242758275_1_400x400.jpg">                
                </li>
                <li>
                    <img class="img" src="//image1.suning.cn/uimg/b2c/newcatentries/0070133963-000000010242758275_1_400x400.jpg">                
                </li>
                <li>
                    <img class="img" src="//image1.suning.cn/uimg/b2c/newcatentries/0070133963-000000010242758275_1_400x400.jpg">                
                </li>
            </ul>
            <div class="border">
                <a class="font">
                    这里是一段文字~
                </a>
            </div>
        </div>

    </body>
</html>
```

