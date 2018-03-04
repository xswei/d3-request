# d3-request

**这个模块在 D3 5.0 中已经被弃用，请使用 [d3-fetch](https://github.com/xswei/d3-fetch) 代替**

这个模块提供了一个方面的操作 XMLHttpRequest 的封装。例如加载一个文本文件:

```js
d3.text("/path/to/file.txt", function(error, text) {
  if (error) throw error;
  console.log(text); // Hello, world!
});
```

加载并解析一个 CSV 文件:

```js
d3.csv("/path/to/file.csv", function(error, data) {
  if (error) throw error;
  console.log(data); // [{"Hello": "world"}, …]
});
```

使用 past 方法传递参数:

```js
d3.request("/path/to/resource")
    .header("X-Requested-With", "XMLHttpRequest")
    .header("Content-Type", "application/x-www-form-urlencoded")
    .post("a=2&b=3", callback);
```

这个模块内置支持解析 [JSON](#json), [CSV](#csv) 和 [TSV](#tsv)；在浏览器端也会支持[HTML](#html) 和 [XML](#xml)。你也可以基于 [request](#request) 添加额外的格式或者直接使用 [request](#request)。

## Installing

NPM 安装: `npm install d3-request`. 此外也可以下载[latest release](https://github.com/d3/d3-request/releases/latest). 也可以直接从 [d3js.org](https://d3js.org) 以 [standalone library](https://d3js.org/d3-request.v1.min.js) 或者作为 [D3 4.0](https://github.com/d3/d3) 的一部分直接载入. 支持 AMD, CommonJS 和最基本的标签引入，如果使用标签引入会暴露 `d3` 全局变量:

```html
<script src="https://d3js.org/d3-collection.v1.min.js"></script>
<script src="https://d3js.org/d3-dispatch.v1.min.js"></script>
<script src="https://d3js.org/d3-dsv.v1.min.js"></script>
<script src="https://d3js.org/d3-request.v1.min.js"></script>
<script>

d3.csv("/path/to/file.csv", callback);

</script>
```

## API Reference

<a name="request" href="#request">#</a> d3.<b>request</b>(<i>url</i>[, <i>callback</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/request.js#L4 "Source")

根据指定的 *url* 返回一个新的 *request*。如果没有指定 *callback* 则返回的 *request* 不会立即 [sent](#request_send) 此时还可以继续配置其他选项。如果指定了 *callback* 则等价于立即调用 [*request*.get](#request_get)：

```js
d3.request(url)
    .get(callback);
```

如果你希望指定一个特殊的请求头或者 mime 类型，则必须不能直接给构造器指定 *callback*。要使用 [*request*.header](#request_header) 或 [*request*.mimeType](#request_mimeType) 设置好之后使用 [*request*.get](#request_get) 来指定 *callback*。 参考 d3.json](#json), [d3.csv](#csv), [d3.tsv](#tsv), [d3.html](#html) 和 [d3.xml](#xml)。

<a name="request_header" href="#request_header">#</a> <i>request</i>.<b>header</b>(<i>name</i>[, <i>value</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/request.js#L51 "Source")

如果指定了 *value* 则将请求头对应的 *name* 设置为 *value* 并返回请求实例。如果 *value* 为 `null` 则表示移除 *name* 对应的值。如果没有指定 *value* 则返回当前实例请求头 *name* 对应的值。请求头名称不区分大小写。

请求头的设置仅仅只能在请求被 [sent](#request_send) 之前。因此如果要设置请求头的话不能直接为 [request constructor](#request) 传入 *callback*。使用 [*request*.get](#request_get) 或相似的方法代替。例如:

```js
d3.request(url)
    .header("Accept-Language", "en-US")
    .header("X-Requested-With", "XMLHttpRequest")
    .get(callback);
```

请注意这个模块默认情况下不会将 `X-Requested-With` 设置为 `XMLHttpRequest`. 一些服务器需要这个头来缓解不必要的请求，但是会触发跨域检查，如果需要的话需要在发送请求前设置这个头。

<a name="request_mimeType" href="#request_mimeType">#</a> <i>request</i>.<b>mimeType</b>([<i>type</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/request.js#L60 "Source")

如果指定了 *type* 则表示将请求实例的 mime type 设置为指定的值并返回当前实例。如果 *type* 为 `null` 则表示清除当前的 mime type (如果有的话)。如果 *type* 没有指定则返回当前的 mime type，默认为 `null`. mime type 被用来设置 ["Accept" request header](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) 和 [overrideMimeType](http://www.w3.org/TR/XMLHttpRequest/#the-overridemimetype%28%29-method)(如果支持)。

请求 mime type 只能在请求被 [sent](#request_send) 前修改。因此如果要设置这个属性的话不能直接给 [request constructor](#request) 传入 *callback*。要使用 [*request*.get](#request_get) 或相似的方法代替。例如：

```js
d3.request(url)
    .mimeType("text/csv")
    .get(callback);
```

<a name="request_user" href="#request_user">#</a> <i>request</i>.<b>user</b>([<i>value</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/request.js#L80 "Source")

如果指定了 *value* 则设置将用于认证的 user name 设置为指定的 *value* 并返回当前的请求实例。如果没有指定 *value* 则返回当前的 user name，默认为 `null`.

<a name="request_password" href="#request_password">#</a> <i>request</i>.<b>password</b>([<i>value</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/request.js#L84 "Source")

如果指定了 *value* 则设置将用于认证的 password 设置为指定的 *value* 并返回当前的请求实例。如果没有指定 *value* 则返回当前的 password，默认为 `null`.

<a name="request_timeout" href="#request_timeout">#</a> <i>request</i>.<b>timeout</b>([<i>timeout</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/request.js#L74 "Source")

如果指定了 *timeout* 则将请求的 [timeout](http://www.w3.org/TR/XMLHttpRequest/#the-timeout-attribute) 属性设置为指定的毫秒数并返回当前的请求实例。如果 *timeout* 没有指定则返回当前的响应超时时间，默认为 0.

<a name="request_responseType" href="#request_responseType">#</a> <i>request</i>.<b>responseType</b>([<i>type</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/request.js#L68 "Source")

如果指定了 *type* 则设置 [response type](http://www.w3.org/TR/XMLHttpRequest/#the-responsetype-attribute) 并返回当前的请求实例。典型的类型为 `​` (空字符串), `arraybuffer`, `blob`, `document`, 和 `text`。如果没有指定 *type* 则返回当前的响应类型，默认为 ``.

<a name="request_response" href="#request_response">#</a> <i>request</i>.<b>response</b>(<i>value</i>) [<>](https://github.com/d3/d3-request/blob/master/src/request.js#L90 "Source")

设置响应值处理函数并返回当前请求实例。响应值函数用来将响应值 `XMLHttpRequest` 进一步处理为期望的数据。可以参考 [json](#json) 和 [text](#text).

<a name="request_get" href="#request_get">#</a> <i>request</i>.<b>get</b>([<i>data</i>][, <i>callback</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/request.js#L96 "Source")

等价于使用 `GET` 方法进行 [*request*.send](#request_send) 操作:

```js
request.send("GET", data, callback);
```

<a name="request_post" href="#request_post">#</a> <i>request</i>.<b>post</b>([<i>data</i>][, <i>callback</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/request.js#L101 "Source")

等价于使用 `POST` 方法进行 [*request*.send](#request_send) 操作:

```js
request.send("POST", data, callback);
```

<a name="request_send" href="#request_send">#</a> <i>request</i>.<b>send</b>(<i>method</i>[, <i>data</i>][, <i>callback</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/request.js#L106 "Source")

使用指定的 *method* (比如 `GET` 或 `POST`) 发出请求，可以通过 *data* 参数设置请求体。如果指定了 *callback* 则在请求成功或失败时会调用。回调会接受两个参数: `error` (如果出错的话) 以及 [response value(响应值)](#request_response)。如果出错的话 响应值为 `undefined`. 等价于:

```js
request
    .on("error", function(error) { callback(error); })
    .on("load", function(xhr) { callback(null, xhr); })
    .send(method, data);
```

如果没有指定 *callback* 则 "load" 和 "error" 事件句柄应该通过 [*request*.on](#request_on) 来注册。

<a name="request_abort" href="#request_abort">#</a> <i>request</i>.<b>abort</b>() [<>](https://github.com/d3/d3-request/blob/master/src/request.js#L121 "Source")

中断请求并返回当前的请求实例。参考 [XMLHttpRequest’s abort](http://www.w3.org/TR/XMLHttpRequest/#the-abort%28%29-method).

<a name="request_on" href="#request_on">#</a> <i>request</i>.<b>on</b>(<i>type</i>[, <i>listener</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/request.js#L126 "Source")

如果指定了 *listener* 则将对应的 *type* 的句柄设置为 *listener* 并返回当前的请求实例。如果对应的 *type* 已经有事件句柄则将其替换。如果 *listener* 为 `null` 则表示将指定的 *type* 句柄移除(如果有的话)。如果没有指定 *listener* 则返回当前 *type* 对应的事件监听器(如果有的话)。

*type* 必须为以下几种:

* `beforesend` - 允许在请求被[sent](#request_send) 之前自定义请求头.
* `progress` - 监测 [progress of the request(请求进度)](http://www.w3.org/TR/progress-events/).
* `load` - 请求成功完成时.
* `error` - 请求完成但是未成功。包括 4xx 和 5xx 响应码.

为同一种时间注册多个事件监听器时需要为 *type* 设置一个名称，比如 `load.foo` 和 `load.bar`. 参考 [d3-dispatch](https://github.com/d3/d3-dispatch) 获取更多信息。

<a name="csv" href="#csv">#</a> d3.<b>csv</b>(<i>url</i>[[, <i>row</i>], <i>callback</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/csv.js "Source")

返回一个用来请求指定 *url* 并且默认 mime type 为 `text/csv` 的 [CSV](https://github.com/d3/d3-dsv#csvParse) 文件。如果没有指定 *callback* 则等价于:

```js
d3.request(url)
    .mimeType("text/csv")
    .response(function(xhr) { return d3.csvParse(xhr.responseText, row); });
```

如果指定了 *callback* 则会发出一个 [GET](#request_get) 请求，等价于:

```js
d3.request(url)
    .mimeType("text/csv")
    .response(function(xhr) { return d3.csvParse(xhr.responseText, row); })
    .get(callback);
```

可选的 *row* 转换函数可以用来遍历和过滤行对象为更具体的表现形式。参考 [*dsv*.parse](https://github.com/d3/d3-dsv#dsv_parse) 获取更多详情，例如：

```js
function row(d) {
  return {`
    year: new Date(+d.Year, 0, 1), // convert "Year" column to Date
    make: d.Make,
    model: d.Model,
    length: +d.Length // convert "Length" column to number
  };
}
```

返回的 *request* 实例暴露一个 *request*.row 方法作为 *row* 转换函数传递给 d3.csv。可以在发送之前配置其他选项。例如:

```js
d3.csv(url, row, callback);
```

等价于:

```js
d3.csv(url)
    .row(row)
    .get(callback);
```

<a name="html" href="#html">#</a> d3.<b>html</b>(<i>url</i>[, <i>callback</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/html.js "Source")

返回一个新的用于从指定 *url* 获取 HTML 并且默认 mime type 为 `text/html` 的 [*request*](#request)。HTML 文件以 [document fragment](https://developer.mozilla.org/en-US/docs/DOM/range.createContextualFragment) 的形式返回。如果没有指定 *callback* 则等价于: 

```js
d3.request(url)
    .mimeType("text/html")
    .response(function(xhr) { return document.createRange().createContextualFragment(xhr.responseText); });
```

如果指定了 *callback*, 则会以 [GET](#request_get) 形式发出请求，等价于:

```js
d3.request(url)
    .mimeType("text/html")
    .response(function(xhr) { return document.createRange().createContextualFragment(xhr.responseText); })
    .get(callback);
```

HTML 的解析需要全局的 document 并且依赖 [DOM Ranges](https://dom.spec.whatwg.org/#ranges)，从 8.3 版本开始 [not supported by JSDOM](https://github.com/tmpvar/jsdom/issues/317) 不再支持，因此这个方法只能在浏览器中使用，而不能在 Node 环境中使用。

<a name="json" href="#json">#</a> d3.<b>json</b>(<i>url</i>[, <i>callback</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/json.js "Source")

返回一个从指定 *url* 以 [get](#request_get) 形式获取 [JSON](http://json.org) 文件的 [*request*](#request)，默认的 mime type 为 `application/json`。如果没有指定 *callback* 则等价于:

```js
d3.request(url)
    .mimeType("application/json")
    .response(function(xhr) { return JSON.parse(xhr.responseText); });
```

如果指定了 *callback* 则会以 [GET](#request_get) 形式发出请求，等价于：

```js
d3.request(url)
    .mimeType("application/json")
    .response(function(xhr) { return JSON.parse(xhr.responseText); })
    .get(callback);
```

<a name="text" href="#text">#</a> d3.<b>text</b>(<i>url</i>[, <i>callback</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/text.js "Source")

返回一个从指定 *url* 以 [get](#request_get)形式获取文本文件的 [*request*](#request)，默认的 mime type 为 `text/plain`. 如果没有指定 *callback* 则等价于:

```js
d3.request(url)
    .mimeType("text/plain")
    .response(function(xhr) { return xhr.responseText; });
```

如果指定了 *callback*， 则会以 [GET](#request_get) 形式发出请求，等价于：

```js
d3.request(url)
    .mimeType("text/plain")
    .response(function(xhr) { return xhr.responseText; })
    .get(callback);
```

<a name="tsv" href="#tsv">#</a> d3.<b>tsv</b>(<i>url</i>[[, <i>row</i>], <i>callback</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/tsv.js "Source")

返回一个用来请求指定 *url* 并且默认 mime type 为 `text/tab-separated-values` 的 [TSV](https://github.com/d3/d3-dsv#tsvParse) 文件。如果没有指定 *callback* 则等价于:

```js
d3.request(url)
    .mimeType("text/tab-separated-values")
    .response(function(xhr) { return d3.tsvParse(xhr.responseText, row); });
```

如果指定了 *callback* 则会以 [GET](#request_get)] 形式发出请求，等价于：

```js
d3.request(url)
    .mimeType("text/tab-separated-values")
    .response(function(xhr) { return d3.tsvParse(xhr.responseText, row); })
    .get(callback);
```

可选的 *row* 转换函数可以用来遍历和过滤行对象为更具体的表现形式。参考 [*dsv*.parse](https://github.com/d3/d3-dsv#dsv_parse) 获取更多详情，例如：

```js
function row(d) {
  return {
    year: new Date(+d.Year, 0, 1), // convert "Year" column to Date
    make: d.Make,
    model: d.Model,
    length: +d.Length // convert "Length" column to number
  };
}
```

返回的 *request* 实例暴露一个 *request*.row 方法作为 *row* 转换函数传递给 d3.tsv。可以在发送之前配置其他选项。例如:

```js
d3.tsv(url, row, callback);
```

等价于：

```js
d3.tsv(url)
    .row(row)
    .get(callback);
```

<a name="xml" href="#xml">#</a> d3.<b>xml</b>(<i>url</i>[, <i>callback</i>]) [<>](https://github.com/d3/d3-request/blob/master/src/xml.js "Source")

返回一个从指定 *url* 以 [get](#request_get) 形式获取 XML 文件的 [*request*](#request)，默认的 mime type 为 `application/xml`。如果没有指定 *callback* 则等价于：

```js
d3.request(url)
    .mimeType("application/xml")
    .response(function(xhr) { return xhr.responseXML; });
```

如果指定了 *callback* 则会以 [GET](#request_get) 形式发出请求，等价于：

```js
d3.request(url)
    .mimeType("application/xml")
    .response(function(xhr) { return xhr.responseXML; })
    .get(callback);
```

XML 的解析依赖于 [*xhr*.responseXML](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/responseXML)。从 1.8 版本开始，[node-XMLHttpRequest](https://github.com/driverdan/node-XMLHttpRequest/issues/8) 不再支持 [*xhr*.responseXML](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/responseXML)，因此此方法只能在浏览器中使用，而不能在 Node 环境中使用。
