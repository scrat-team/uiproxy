uiproxy
=======

uiproxy 模块提供前端点击事件托管，使用 touch 事件模拟点击。

uiproxy 模块返回一个构造函数。

配置对象的 key 为 CSS 选择器，value 为选择器指定对象被点击时的事件处理函数。

__注意__ 目前支持简单选择器（ID、className，tagName），以及派生选择器，不支持子选择器、伪类选择器等，可以为多个选择器绑定同一个处理函数，多个选择器间用英文逗号隔开。

``` javascript
var UIProxy = require('uiproxy'),
    uiproxy = new UIProxy();
```

### uiproxy.on(selector, [proxy], handler)

为我们定义的 uiproxy 绑定一个处理函数，如果参数 callback 为数组，则代表 [handler, context, [...args]]。

如果没有指定事件委托元素，则默认委托在 document 上。

``` javascript
// 虚拟一个提示框组件用于示例
var alert = new Alert();

function onHome(e) {
    // ...
}

function onClose(event) {
    this.hide(); // this 已被指向 alert
}

function onNavi(event, text) {
    console.log(text); // 'say hello'
}

// ...

// id 选择器
uiproxy.on('#home', onHome);

// className 选择器
uiproxy.on('.button', onButton);

// 标签选择器
uiproxy.on('label', onLabel);

// 多种选择器共同绑定一个响应函数
uiproxy.on('.link, .url', onURL);

// 组合选择器
uiproxy.on('.button.active', onActiveButton);

// 派生选择器，并且指定 context
uiproxy.on('.alert .close', [onClose, alert]);

// 派生选择器，并且指定 context 和参数
uiproxy.on('#navi .button', [onNavi, this, 'say hello']);

// 将事件绑定在某个元素上
uiproxy.on('.ok', '.alert', onOK);
```

### uiproxy.on(config)

可以将上述的配置合并为一个配置对象。

__说明__： 暂时不支持配置委托元素。

``` javascript
var events = {
    '#home': onHome,
    '.button': onButton,
    'label', onLabel,
    '.link, .url': onURL,
    '.button.active': onActiveButton,
    '.alert .close': [onClose, alert],
    '#navi .button': [onNavi, this, 'say hello']
    ...
};
uiproxy.on(events);
```

### uiproxy.off(selector, [proxy], handler)

解绑单条配置。

__注意__ 如果事件委托在某个元素上，则必须指定该元素才能正常解绑。

``` javascript
// id 选择器
uiproxy.off('#home', onHome);

// 多种选择器共同绑定一个响应函数
uiproxy.off('.link, .url', onURL);

// 派生选择器，并且指定 context
uiproxy.off('.alert .close', [onClose, alert]);

// 派生选择器，并且指定 context 和参数
uiproxy.off('#navi .button', [onNavi, this, 'say hello']);

// 将事件绑定在某个元素上
uiproxy.off('.ok', '.alert', onOK);
```

### uiproxy.off(config)

解绑配置对象中的配置。

``` javascript
uiproxy.off(events);
```

### uiproxy.off(selector, true)

解绑指定选择器的所有响应函数。

``` javascript
uiproxy.on('#home', onHome);
uiproxy.on('#home', onTap);

uiproxy.off('#home', true);
```

### uiproxy.off(true, proxy)

接触绑定委托在某个元素上的所有响应函数

``` javascript
uiproxy.on('.button', '.alert', onButton);
uiproxy.on('.close', '.alert', onClose);

uiproxy.off(true, '.alert');
```