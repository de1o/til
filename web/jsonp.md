### JSONP

今天看京东获取评论数量的接口，无法通过模拟请求得到返回数据。大失败。

顺便研究了一下jsonp这个东西。

源起浏览器的同源策略（Same-Origin Policy），安全起见，不同源（源一般由protocol，hostname，port三者决定，IE不考虑port）的脚本不能访问彼此的资源。如果一个源A的页面想访问来自源B的内容怎么办呢？JSONP是一个解决方案。

虽然XMLHttpRequest不能访问不同源的资源。HTML里的script标签却可能加载并执行其他源的JS。这样只要由源A在script标签里引用源B的脚本（浏览器自然会发起一个请求，同时在参数里指定一个callback函数，源B的服务器取出该参数，并构造callback(data)这样的回复，又因为script标签返回的内容会被浏览器执行，相当于在源A的页面上会执行callback(data)这个函数调用。这样就达到了在源A的页面上访问源B上资源的目的。

显然，JSONP存在安全风险，只应该在可信的服务器之间进行JSONP通信。否则就是送上门的XSS了。

参考

http://www.jianshu.com/p/4e17445d66e2