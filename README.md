## Lory QrCode
Build Status Latest Stable Version Latest Unstable Version License Total Downloads


简介
Lory QrCode 是基于强大的Bacon/BaconQrCode库开发的适用于当前最流行的Laravel框架的一个扩展库.便于Laravel用户可以很方便地使用.


翻译
我们在寻找可以将本文档翻译成韩语或日语的伙伴加入,将本文档翻译成当地语言.愿意翻译的朋友们欢迎pull给我.


配置
Composer 设置
首先,添加 QrCode 包添加到你的 composer.json 文件的 require 里:
```
"require": {
	"lorysoftwareio/simple-qrcode": "~2"
}
```
然后,运行 composer update .

#### 添加 Service Provider
###### Laravel <= 5.4
注册 SimpleSoftwareIO\QrCode\QrCodeServiceProvider::class 至 config/app.php 的 providers 数组里.

添加 Aliases
###### Laravel <= 5.4
最后,注册 'QrCode' => SimpleSoftwareIO\QrCode\Facades\QrCode::class 至 config/app.php 的 aliases 数组里.


#### 简例
打印视图
一个重要的应用是在打印页面添加的来源二维码.这里我们只需要在 footer.blade.php 文件里添加如下代码即可!
```
<div class="visible-print text-center">
	{!! QrCode::size(100)->generate(Request::url()); !!}
	<p>Scan me to return to the original page.</p>
</div>
```
嵌入二维码
你可以嵌入一个二维码在你的Email里,让收信的用户可以快速扫描.以下是使用 Laravel 实现的一个例子:
```
//Inside of a blade template.
<img src="{!!$message->embedData(QrCode::format('png')->generate('Embed me into an e-mail!'), 'QrCode.png', 'image/png')!!}">
```
使用说明
基本使用
使用QrCode的Generator非常方便. 多数情况下只要这样:
```
QrCode::generate('Make me into a QrCode!');
```
这就能创建一个内容是:"Make me into a QrCode!" 的二维码了.

生成二维码
Generate 是用来创建二维码的方法.
```
QrCode::generate('Make me into a QrCode!');
```
注意:要创建二维码必须使用此方法

Generate 默认返回一个 SVG 格式的图片文本. 你可以在Laravel 的 Blade 系统中把它显示到浏览器中,使用方式如下:
```
{!! QrCode::generate('Make me into a QrCode!'); !!}
```
generate 方法的第二个参数是指定要存储图片数据的文件地址.
```
QrCode::generate('Make me into a QrCode!', '../public/qrcodes/qrcode.svg');
```
自己定义输出图片格式
QrCode Generator 默认输出SVG格式的图片.

注意! format 方法必须第一个被设置, 其它的设置如: size, color, backgroundColor, 和 margin 的设置必须在它的后边.

支持 PNG，EPS，SVG 三种格式,设置方式如下:
```
QrCode::format('png');  //Will return a PNG image
QrCode::format('eps');  //Will return a EPS image
QrCode::format('svg');  //Will return a SVG image
```
尺寸设置
QrCode 的 Generator 默认返回可能最小像素单位的二维码.

你可以使用 size 方法去设置它的尺寸.下方是设置像素尺寸的实例:
```
QrCode::size(100);
```

边距设置
也支持设置边距. 设置方式如下:
```
QrCode::margin(100);
```
容错级别设置
改变二维码的容错级别也很方便. 只要这么设置:
```
QrCode::errorCorrection('H');
```
下方是 errorCorrection 方法支持的容错级别设置.

#### 加LOGO图(没有圆边)

![avatar](https://raw.githubusercontent.com/SimpleSoftwareIO/simple-qrcode/master/docs/imgs/merged-qrcode.png?raw=true)

merge 方法可以让QrCode为生成结果加上LOGO图片. 下方是常见的为二维码加LOGO图片的使用方式.
```
QrCode::merge($filename, $percentage, $absolute);
```

//生成一个中间有LOGO图片的二维码
```
QrCode::format('png')->merge('path-to-image.png')->generate();
```
//生成一个中间有LOGO图片的二维码,且LOGO图片占整个二维码图片的30%.
```
QrCode::format('png')->merge('path-to-image.png', .3)->generate();
```
//使用绝对路径的LOGO图片地址创建二维码,LOGO图片占整个二维码图片的30%.
```
QrCode::format('png')->merge('http://www.google.com/someimage.png', .3, true)->generate();
```
merge 方法当前只支持PNG格式的图片 默认使用相对于应用程序的根路径,把第三个参数设置为 true 就能切换到使用绝对路径

为了让二维码保持高可识别度,建议在使用 merge 方法时把二维码的容错级别提高. 我们默认使用: errorCorrection('H').

Merged Logo

##### 加二进制LOGO图片
mergeString 方法与 merge 方法类似, 不同的是它允许你使用一个二进制的String代替LOGO文件路径. 这在使用 Storage 存储时,会显得很方便. 它的参数与 merge 类似.
```
QrCode::mergeString(Storage::get('path/to/image.png'), $percentage);
```
//使用中间件读取的图片数据作为LOGO图片来创建二维码.
```
QrCode::format('png')->mergeString(Storage::get('path/to/image.png'))->generate();
```
//使用中间件读取的图片数据作为LOGO图片来创建二维码, 且LOGO图片占整个二维码图片的30%.
```
QrCode::format('png')->mergeString(Storage::get('path/to/image.png'), .3)->generate();
```
和 merge 方法一样,当前只支持PNG格式. 同样建议将二维码的容错级别提高.

高级用法
所有的方法都支持连贯操作. generate 方法必须在最后 format 必须在第一个. 例如:
```
QrCode::size(250)->color(150,90,10)->backgroundColor(10,14,244)->generate('Make me a QrCode!');
QrCode::format('png')->size(399)->color(40,40,40)->generate('Make me a QrCode!');
```
你还能不存储图片,而使用 base64_encode 来将二进制数据直接显示成二维码图片.
```
<img src="data:image/png;base64, {!! base64_encode(QrCode::format('png')->size(100)->generate('Make me into an QrCode!')) !!} ">
```

#### 生成圆边图边LOGO二维码

![avatar](https://img2018.cnblogs.com/blog/646346/201903/646346-20190314234503105-395504255.png)

```
Qrcode::format('png')->size(100)->cornergenerate('https://www.hao123.com', $cornerimg, $logoimg,public_path("/img/cc.png"));
```

