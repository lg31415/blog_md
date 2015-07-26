title: html
date: 2015-07-13 18:48:08
tags: html
categories:
---


#### 设置span宽度

```html
.txtMarquee-top .bd .tempWrap li .name{
    background-color:#fc0;
    display:-moz-inline-box;
    display:inline-block;
    width:35px;
    margin-left: 8px;
}

//设置textarea
 .bangwo_textarea {
        background: #ffffff none repeat scroll 0 0;
        border: 1px solid #ffffff;
        font-family: "微软雅黑";
        height: 48px;
        line-height: 25px;
        outline: medium none;
        overflow: hidden;
        padding-left: 6px;
        padding-right: 6px;
        resize: none;
        width: 487px;
}
```


#### 取得当前文档中获得焦点的元素

```js
var focusedElement = document.activeElement; 
```

#### 对页面上元素的onkeyup绑定方法
```js
window.onload = function(){
    //对页面上元素的onkeyup绑定方法
    document.onkeyup=Index.searchEnter;
}
```

#### 一个js中导入另一个js

```js
 new_element=document.createElement("script");
 new_element.setAttribute("type","text/javascript");
new_element.setAttribute("src","a.js");// 在这里引入了a.js
 document.body.appendChild(new_element);
```

#### window.onload 绑定多个方法

```js
(function(win, doc) {
    contentLoaded=function(fn) {
        var done = false, top = true,
 
        doc = win.document, root = doc.documentElement,
 
        add = doc.addEventListener ? 'addEventListener' : 'attachEvent',
        rem = doc.addEventListener ? 'removeEventListener' : 'detachEvent',
        pre = doc.addEventListener ? '' : 'on',
 
        init = function(e) {
            if (e.type == 'readystatechange' && doc.readyState != 'complete') return;
            (e.type == 'load' ? win : doc)[rem](pre + e.type, init, false);
            if (!done && (done = true)) fn.call(win, e.type || e);
        },
 
        poll = function() {
            try { root.doScroll('left'); } catch(e) { setTimeout(poll, 50); return; }
            init('poll');
        };
 
        if (doc.readyState == 'complete') fn.call(win, 'lazy');
        else {
            if (doc.createEventObject && root.doScroll) {
                try { top = !win.frameElement; } catch(e) { }
                if (top) poll();
            }
            doc[add](pre + 'DOMContentLoaded', init, false);
            doc[add](pre + 'readystatechange', init, false);
            win[add](pre + 'load', init, false);
        }
 
    }
})(window, document);
```

### Float元素父容器在Firefox中自动撑大的方法
#### 1.给父容器使用display属性
```html
div#container {
  display: table; /* 建议使用 */
  /*或者
    display: table-cell
  */
}
```
#### 2.浮动父容器
```html
div#container {
    display: inline;
}
```
### 3、给父容器添加一个min-height属性
```html
div#container {
    min-height: 100px;
}
```

#### 多版本JQuery共存

```js
//匿名函数
(function(){
    /*! jQuery v1.7.2 jquery.com | jquery.org/license */
    //jQuery的源码
    -------------
    // <script type="text/javascript" src="jquery v1.4.4"></script>
    // <script type="text/javascript" src="myJQ.js"></script>
    -------------
    // $myJQ -------> jquery v1.7.2
    var $myJQ = jQuery.noConflict(true);
    
    //注册对象
    window["$myJQ"]=$myJQ;
    
    
})();
```


#### 页面跳转
+ `window.location.href="url"`
    _地址栏变化,跳转页面_
+ `window.frames['frame_name'].location="url"`
    _地址栏不变化,跳转到指定iframe_

## 测试阿迪斯