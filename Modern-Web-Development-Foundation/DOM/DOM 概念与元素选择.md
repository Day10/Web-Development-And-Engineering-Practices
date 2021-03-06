[![返回目录](https://parg.co/U0y)](https://parg.co/UHU) 


# DOM 概念与元素选择


# Selector(选择器)

## getElementBy*



## querySelector*

HTML5向Web API新引入了document.querySelector以及document.querySelectorAll两个方法用来更方便地从DOM选取元素，功能类似于jQuery的选择器。这使得在编写原生JavaScript代码时方便了许多。该方法的浏览器兼容性目前各主流浏览器对此API提供了良好支持，IE需8+。详情见[caniuse](http://caniuse.com/queryselector)。

两个方法使用差不多的语法，都是接收一个字符串参数，这个参数需要是合法的CSS选择语法。需要注意的是，传入的选择器参数是要经过字符串转义的，譬如我们有一个类名为为'foo:bar'，所以正确的做法是将反斜杠转义后'.foo\\:bar'再传递给querySelector，后者在接收到'.foo\\:bar'这个参数后，字符串将两个反斜杠转义成一个，然后querySelector前面得到的一个反斜杠与冒号结合进行转义得到正确结果。

``` 
element = document.querySelector('selectors');
elementList = document.querySelectorAll('selectors');
```

其中参数selectors 可以包含多个CSS选择器，用逗号隔开。

``` 
element = document.querySelector('selector1,selector2,...');
elementList = document.querySelectorAll('selector1,selector2,...');
```

使用这两个方法无法查找带伪类状态的元素，比如querySelector(':hover')不会得到预期结果。不过需要注意的是该系列方法返回的是非实时的结果，想要区别什么是实时非实时的返回结果，请看下例：


``` javascript
//HTML代码<div id="container">
    <div></div>
    <div></div>

</div>

//首先选取页面中id为container的元素
container=document.getElementById('#container');
console.log(container.childNodes.length)//结果为2
//然后通过代码为其添加一个子元素
container.appendChild(document.createElement('div'));
//这个元素不但添加到页面了，这里的变量container也自动更新了
console.log(container.childNodes.length)//结果为3
```

通过上面的例子就很好地理解了什么是会实时更新的元素。document.getElementById返回的便是实时结果，上面对其添加一个子元素后，再次获取所有子元素个数，已经由原来的2个更新为3个(这里不考虑有些浏览器比如Chrome会把空白也解析为一个子节点)。



### Polyfill
在IE7以及之前的浏览器中，并不能使用querySelect*等方法，因此需要做以下的Polyfil:
```
if (!document.querySelectorAll) {
  document.querySelectorAll = function (selectors) {
    var style = document.createElement('style'), elements = [], element;
    document.documentElement.firstChild.appendChild(style);
    document._qsa = [];

    style.styleSheet.cssText = selectors + '{x-qsa:expression(document._qsa && document._qsa.push(this))}';
    window.scrollBy(0, 0);
    style.parentNode.removeChild(style);

    while (document._qsa.length) {
      element = document._qsa.shift();
      element.style.removeAttribute('x-qsa');
      elements.push(element);
    }
    document._qsa = null;
    return elements;
  };
}

if (!document.querySelector) {
  document.querySelector = function (selectors) {
    var elements = document.querySelectorAll(selectors);
    return (elements.length) ? elements[0] : null;
  };
}


```

## 亲属节点

### 父节点

语法：nodeObject.parentNode。其中，nodeObject 为节点对象（元素节点）。例如，获取 id="demo"的节点的父节点：

``` javascript
document.getElementById("demo").parentNode;
```

### 兄弟节点

以下属性与元素节点的同级元素相关。

**（1）nextElementSibling**

nextElementSibling属性返回指定元素的后一个同级元素，如果没有则返回null。

``` 
// 假定HTML代码如下
// <div id="div-01">Here is div-01</div>
// <div id="div-02">Here is div-02</div>
var el = document.getElementById('div-01');
el.nextElementSibling
// <div id="div-02">Here is div-02</div>

```

**（2）previousElementSibling**

previousElementSibling属性返回指定元素的前一个同级元素，如果没有则返回null。

### 子节点

**1）children**

children属性返回一个类似数组的动态对象（实时反映变化），包括当前元素节点的所有子元素。如果当前元素没有子元素，则返回的对象包含零个成员。

``` javascript
// para是一个p元素节点
if (para.children.length) {
  var children = para.children;
    for (var i = 0; i < children.length; i++) {
      // ...
    }
}
```

**（2）childElementCount**

childElementCount属性返回当前元素节点包含的子元素节点的个数。

**（3）firstElementChild**

firstElementChild属性返回第一个子元素，如果没有，则返回null。

**（4）lastElementChild**

lastElementChild属性返回最后一个子元素，如果没有，则返回null。



