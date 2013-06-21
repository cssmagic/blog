# 终于知道 GA 订单统计中的 store 字段怎么用了

GA 的电子商务追踪（订单统计）功能很强大。做过一些电商网站之后，我对这一块还是比较熟悉的。在开发中，需要在订单完成页面，将订单及订单内商品的数据发送给 GA。其中发送订单的代码片断如下（摘自 [GA 官方提供的示例代码](https://developers.google.com/analytics/devguides/collection/gajs/gaTrackingEcommerce#Example)）：

```js
_gaq.push(['_addTrans',
	'1234',           // transaction ID - required
	'Acme Clothing',  // affiliation or store name
	'11.99',          // total - required
	'1.29',           // tax
	'5',              // shipping
	'San Jose',       // city
	'California',     // state or province
	'USA'             // country
]);
```

这其中有一个字段叫 `affiliation or store name`，非必填项（可以留空字符串），直译过来就是“加盟店的名称”。但我一直不知道这个字段有什么用，在以前做过的项目中，都是直接把网站的名称传给它，很显然这个字段没有发挥出实际作用。

我在最近的一个新项目中尝试使用供应商代码来标记这个字段，终于找到它的作用。在电子商务分析的次级维度中可以使用它进行多维分析，在 GA 中它被称为“联属机构”。

![GA 电子商务分析](http://www.cssmagic.net/blog/pic/201306/ga-order-tracking-store-field.png)

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [更多文章](https://github.com/cssmagic/blog/issues)
> * [查看原文与评论](https://github.com/cssmagic/blog/issues/11)
