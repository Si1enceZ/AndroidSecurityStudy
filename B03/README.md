
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

* [FRIDA脚本系列（三）超神篇：百度AI“调教”抖音AI](#frida脚本系列三超神篇百度ai调教抖音ai)
	* [0x01. 日本抖音美如画](#0x01-日本抖音美如画)
	* [0x02. 如何让抖音AI更加“智能”](#0x02-如何让抖音ai更加智能)
		* [点赞&关注&上滑&转发：模拟点击](#点赞关注上滑转发模拟点击)
		* [评论：模拟点击&输入框](#评论模拟点击输入框)
	* [0x03. 如何“训练”抖音AI](#0x03-如何训练抖音ai)
		* [截图](#截图)
		* [将图片传回`PC`](#将图片传回pc)
		* [将图片上传到百度`AI`平台](#将图片上传到百度ai平台)
		* [获取百度`AI`结果](#获取百度ai结果)
		* [按照结果决定是否点赞](#按照结果决定是否点赞)
	* [0x04. 百度AI效果演示](#0x04-百度ai效果演示)

<!-- /code_chunk_output -->



## FRIDA脚本系列（三）超神篇：百度AI“调教”抖音AI

### 0x01. 日本抖音美如画

在万恶的资本主义国家，女孩子普遍都吃不饱，每天也没啥事情干，只能在沙滩上晒晒太阳，拍点短视频填充自己空虚的灵魂，在穿着上也是怎么省怎么来，比如布料普遍不会太多，有时候甚至会用**几根线**来代替面料，资本主义实在是太可恶了。

![](pic/01.gif)

![](pic/02.gif)

![](pic/03.gif)

![](pic/04.gif)

为了培养社会主义的接班人，让更多的人跟我一起去批判资本主义，我在朋友圈发了一些上述的视频（样本地址：[github主页](https://github.com/hookmaster/frida-all-in-one)/sample/TikTokJapan/，为了大家批判的足够充分，我采集了足够多的`mp4`样本。）

大家纷纷表示此时此刻正需要这样的反面教材，有人甚至发私信问我，如何可以得到更多的这样的反面教材，他们已经通过一系列方法，成功的进入敌人的大本营，奈何不懂日语，刷出来的都是一些索然无味的视频，非常懊恼。

为了解决大家的刚性需求，建设富强民主文明和谐的程序员社区，我们来研究一下，如何使用最为前沿的`AI`技术，结合胶水框架——`frida`，提高大家刷出反面教材的几率，做到真正的造福程序猿，提高幸福感和获得感。

（PS：文中大量使用黑产惯用技术，为避免效仿，仅介绍过时的工具，且不做调试）

### 0x02. 如何让抖音AI更加“智能”

首先我们来看下日本抖音的界面，跟国内的版本并没有什么不同。

![](pic/05.png)

那就其实很容易想到，只要看到自己喜欢的反面教材，尤其是泳装、沙滩、比基尼的内容，只要疯狂点赞就好了。

做足全套的话，就是点赞、关注、评论加转发，这些是正向反馈。

更有甚者，可以滑进某个喜欢的爱豆页面，将她发的每一个视频全都点赞一遍，这个正向反馈也足够大了，不过会模糊我们的目标——泳装比基尼，毕竟爱豆也不是每个短视频都比基尼。

我们应该目标明确：就是要看比基尼！要不然潜入敌人内部的目的何在？国产小姐姐也足够迷人。

如果不是比基尼呢？负面反馈好像真的很少，直接上滑跳过这则视频即可。

#### 点赞&关注&上滑&转发：模拟点击

在安卓手机上进行模拟点击的技术实在是太多了，常见的有按键精灵、触摸精灵，辅助功能、adb调试指令直接点击等等。

这个产业链也是非常成熟，大量黑灰从业者开发出大量的脚本工具，来实现“定制”的功能。

![](pic/07.jpg)

`frida-server`注入到目标进程之后，可以加载一些模拟点击的库，根据`frida`脚本及点击脚本的逻辑，有针对性地对指定内容进行点赞、关注、上滑和转发。这些操作的位置都是固定的，只要模拟用户点击上去即可实现。

![](pic/08.jpg)

辅助功能是另外一个方法，而且这是一个完全合法的方法，毕竟这是安卓系统自带的一个功能，提供给残障人士去用的，`frida-server`注入到目标进程之后，可以直接加载一些辅助功能的代码库，进行操作。

另外安卓的调试命令`adb`也是可以直接在屏幕上进行点击的，格式如下。可以在`frida`的客户端直接用`python`的`os`库调用`adb`的相关命令，进行屏幕点击的相关操作，`adb`点击的相关研究也特别多，百度搜一下可以找到很多代码。

```
adb shell
shell@PRO6:/ $ input tap 125 521
shell@PRO6:/ $
```

最最`low`的方式就是在`Windows`模拟器里跑，有`Intel Houdini for arm-x86`在，也可以把抖音跑起来，但抖音肯定会做检测就对了。然后使用`python`的`pywin32`实现`Windows`窗口的点击，这样做有两个好处，一个是脱离真机，成本大大降低，还有就是做批量化很容易。缺点也是容易被检测到。

当然，在`iOS`上，这类需求也是屡见不鲜的，除了上述触摸精灵本身也支持`iOS`端之外，`iOS`的模拟点击库也是层出不穷，`frida-server`可以加载这些`gadget`，调用`API`，实现模拟点击、双击、滑动等功能。

![](pic/09.jpg)

#### 评论：模拟点击&输入框

模拟点击的功能实现了，在输入框里输入评论更加小菜一碟了，使用`frida-server`来`hook`输入框那简直跟玩一样，属于入门技巧，这里是一段典型的`hook` `TextView`的代码。

```js
console.log("Script loaded successfully ");
Java.perform(function () {
    var tv_class = Java.use("android.widget.TextView");
    tv_class.setText.overload("java.lang.CharSequence").implementation = function (x) {
        var string_to_send = x.toString();
        var string_to_recv;
        send(string_to_send); // send data to python code
        recv(function (received_json_object) {
            string_to_recv = received_json_object.my_data
        }).wait(); //block execution till the message is received
        return this.setText(string_to_recv);
    }
});
```

需要注意的是，输入的字符串肯定不可以全都是一样的，这样也太明显了，最好用`sqlite`存一个`舔狗集合.db`，然后不断从里面调就行了。

### 0x03. 如何“训练”抖音AI

#### 截图

在`frida`的客户端，使用`python`调`os`包，执行`adb`调试命令，对正在运行中的手机进行截图操作，截图会保存在`sd`卡上。运行的频率可以是每两三秒运行一次之类的，具体看具体调试结果。

```
$ adb shell screencap -p /sdcard/01.png
```

#### 将图片传回`PC`

逻辑跟截图相同，取回`sd`卡里的照片。

```
$ adb pull /sdcard/01.png
```

#### 将图片上传到百度`AI`平台

百度`AI`平台的调用接口还是比较简单易用的，提供的功能也非常多，比如`GIF`色情识别，短视频审核，图像审核等等，将图片使用`base64`编码之后，`post`到百度`AI`的服务器即可，返回的`json`数据即为百度`AI`对图片的判定结果。

![](pic/10.JPG)


#### 获取百度`AI`结果

返回的`json`包也是简单粗暴，直接将不合规内容以及可能性全都打印出来，这样的格式化数据想要提取出来进行逻辑判断只能说是太容易了。

```json
{
	"conclusion": "不合规",
	"log_id": "15537510677705536",
	"data": [
		{
			"msg": "存在性感内容",
			"probability": 0.999525,
			"type": 2
		},
		{
			"msg": "存在水印码内容",
			"probability": 0.8558467,
			"type": 5
		},
		{
			"msg": "存在公众人物",
			"stars": [
				{
					"probability": 0.73777246,
					"name": "孙淑媚"
				}
			],
			"type": 11
		}
	],
	"conclusionType": 2
}
```

#### 按照结果决定是否点赞

在`frida`客户端，将结果动态影响到`frida-server`是非常容易的，[前面的教程](https://github.com/hookmaster/frida-all-in-one/blob/master/03.%E5%9F%BA%E6%9C%AC%E6%A1%88%E4%BE%8B%E4%B8%8A%E6%89%8B/%E4%B8%80%E7%AF%87%E6%96%87%E7%AB%A0%E5%B8%A6%E4%BD%A0%E9%A2%86%E6%82%9FFrida%E7%9A%84%E7%B2%BE%E9%AB%93(%E5%9F%BA%E4%BA%8E%E5%AE%89%E5%8D%938.1)/READMD.md)中已经讲得非常丰富了。

### 0x04. 百度AI效果演示

上面说了这么多，如果百度`AI`不能准确地将我们想要看的“反面教材”圈出来点赞，那岂不是都白干了。所以百度`AI`一定要准，对于“反面教材”的筛选要毫不留情，尽量不要有漏网之鱼。虽然哪怕即使有，使用百度`AI`“调戏”抖音`AI`这个事情也是有意义的，因为成功率已经相当高了。

![](pic/06.png)

我们选择`色情识别`功能，来验证一下，到底识别率怎么样。

![](pic/11.JPG)

色情识别，存在性感内容，很好。

![](pic/12.JPG)

色情识别，存在性感内容，很好。

![](pic/13.JPG)

色情识别，存在性感内容，很好。

![](pic/14.JPG)

色情识别，通过，很好。

![](pic/16.JPG)

色情识别，通过，很好。

![](pic/15.JPG)

色情识别，存在性感内容，很好。

百度`AI`真棒！比基尼一张不落！全都识别了出来！

真好。

蒽，抖音看片，指日可待！