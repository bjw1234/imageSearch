# imageSearch
使用动态加载(懒加载)、瀑布流布局以及LightBox实现一个图片搜索效果。

# 开始

做一个小项目，算是对`js`和`jQuery`的一次练习吧。

图片资源来自这个网站（上面有很多高清图片关键还是免费下载的）：
https://unsplash.com/ 

项目具体效果可以点这里：
https://bjw1234.github.io/imageSearch/

# 懒加载插件
使用`jQuery`封装一个简单的滚动加载插件`scrollLoading`。

__ 使用方式： __

```js
// jQuery对象调用scrollLoading方法即可。
// 通过传递实际参数扩展默认值。
$('.loading').scrollLoading({
	attr: 'data-url', // 可选： 图片实际地址属性
	container: $(window), // 可选： 滚动容器
	callback: $.noop() // 可选： 回调函数    
});
```

在HTML中：

```html
<img class="loading" 
	 data-url="http://xxx/xxx/xxx.jpg" 
	 src="data:image/gif;base64,R0lGODlhAQABAIAAAP///wAAACH5BAEAAAAALAAAAAABAAEAAAICRAEAOw==">
```

__ 插件实现思路： __

通过给滚动容器绑定`scroll`事件，在滚动时判断元素（jQuery对象）是否位于可见区域，如果是则动态的改变`img`的`src`属性值。

代码实例：
*只是简单示例，不代表具体内容。*
```js
var loading = function(){
    // 滚动容器高度
    let containerHeight = container.height();
    // 垂直滚动条位置(卷起的高度)
    let containerScrollTop = container.scrollTop();
    // 判断是否位于可见区域
    let position = obj.offset().top - containerScrollTop;
    if(obj.is(':visible')&&(position >= 0 && position < containerHeight)) {
	// 这时就可以去改变img的src属性
	obj.attr(src,url); 
	...
    }
}

// 容器绑定滚动事件
container.bind('scroll',loading);
```

# 瀑布流布局
由于实现起来比较简单所以没有将其封装成插件。

__ 实现思路： __
维护好一个数组，这个数组个数为流体布局的列数，数组元素为每一列的高度值。通过绝对定位的方式将每一个元素布局在合适的位置。
这样说该是比较抽象的，且看代码示例：

```js
// 需要实现瀑布流的一些元素
let boxes = $('.xxx');
// 一个盒子的宽度
let w = boxes.eq(0).outerWidth();
// 列数
let cols = Math.floor($(window) / w);
// 存放列高度的数组
let hArr = [];
boxes.each((index,item) => {
	let h = boxes.eq(index).outerHeight();
	if(index < cols){  // 第一行
    	hArr[index] = h;
    } else {
    	// 拿到最小的那个高度值
    	let minH = Math(...hArr);
      // 拿到其对应的下标
      let i = $.inArray(minH,hArr);
      // 动态的设置元素的定位
      $(item).css({
			'position':'absolute',
			'left': w * i + 'px',
			'top': minH + 'px'
      });      
      // 更新存放列高度的数组
      hArr[i] += boxes.eq(index).outerHHeight();
    }
});
```

# LightBox插件
通过事件代理，当用户点击某一张图片时，弹出一个弹框展示图片。
`img`标签中需要传递的一些参数：

__ 需要的一些参数： __
data-role="lightbox"  // 启动LightBox插件
data-id="xxxxxx" // 该图片的唯一标识符
data-group="xxx-group" // 图片分组的名称
data-source="xxx-url" // 原始大图地址
data-caption="xxxx" // 图片的描述文字

__ 使用方式： __
除了引入必要的js和css文件之外，在代码中`new`这个插件即可：

```js
new LightBox({
	maskOpacity: 0.5, // 遮罩层透明度(可选)
	animateSpeed: 'fast' // 动画执行速度(可选)    
   ... 
});
```
比较重要的一点就是关于图片预加载的实现：

```js
function preLoadPicture(src,callback) {
	let image = new Image();
	image.src = src;
	if(!!window.ActiveXObject){ // IE
    image.onreadystatechange = function(){
        if(image.readyState === 'complete') {
            callback(image.width,image.height);
          }
    	}
    } else { // NOT IE
    	image.onload = function(){
        	callback(image.width,image.height);
        }
    }   
}
```

# 结束
了解了以上内容，实现图片搜索、展示、无限滚动加载的效果就比较简单啦。
无非就是：
* 获取用户输入的`keyWord`
* `ajax`获取图片json信息
* 组装、填充页面HTML数据
* 瀑布流布局处理
* 在页面滚动时判断最后一张图片是否可见，如果是重复以上内容






