#promise

[参考原文](http://www.html5rocks.com/en/tutorials/es6/promises/#toc-async)

##产生promise的需求

promise用于解决event所解决不了的问题。比如我们想给图片的加载加上handler。通过监听图片的load，error事件无法解决图片在监听动作生效以前图片就已完成load或者load失败的情况。

如果你不关心一个动作在什么时候发生，而只关注它是否已经发生。并根据它当前的状态来决定需要执行的内容。那么promise适合你。

promise定义的状态：

	* fulfilled - promise所关联的动作成功完成
	* rejected - promise所关联的动作执行失败
	* pending - 动作尚未进入fulfilled/rejected两种状态之一
	* settled - 动作进入fulfilled/rejected两种状态之一

##es6里的promise api

###定义一个promise：
~~~javascript
var promise = new Promise(function(resolve, reject) {
  // do a thing, possibly async, then…

  if (/* everything turned out fine */) {
    resolve("Stuff worked!");
  }
  else {
    reject(Error("It broke"));
  }
});
~~~

###使用这个promise：
~~~js
promise.then(function(result) {
  console.log(result); // "Stuff worked!"
}, function(err) {
  console.log(err); // Error: "It broke"
});
~~~

也就说，promise主要的api是，提供一个`then`方法，接受两个函数，分别在Promise fulfilled和rejected的时候使用。

jQuery的Defered由于也有then方法的存在，可以通过Promise.resolve($.ajax("/whatever.json))来将Defered转成一个js promise。但是Defered在success/fail的时候接受到的三个参数只会被保留下第一个（success的时候是response而fail的时候是xhrObj）。

> JS的promises最先以feature的形式出现在dom里，随后被移到JS中。这样Promises可以在非浏览器的执行环境下使用。新的使用异步调用的DOM API也同时在使用promises(Quota Management, Font Load Events...)。


###串联
then方法会返回同一个promise，所以可以串联起来使用。

~~~js
var promise = new Promise(function(resolve, reject) {
  resolve(1);
});

promise.then(function(val) {
  console.log(val); // 1
  return val + 2;
}).then(function(val) {
  console.log(val); // 3
});
~~~
从代码中可以看出，前一个then方法return的值会用于下一个then方法的输入。(按：此处的调用形式很像gulp的pipe调用形式。)

###异步执行队列

上面说到then方法的返回值，实际上，如果返回的值是一个promise-like的玩意儿，下一个then方法就会等待这个promise settles（达到fulfilled或是rejected状态）为止，如果说成功了，继续将这个promise返回的值作为该then函数的参数输入。

利用这点，我们可以在then函数里继续构造新的promise，达到连接多个异步操作的目的。

###立即settle的promise
通常我们使用Promise是为了等待某个动作的发生/条件达成，但是有时我们也需要没有实际等待动作，也就是可以立刻使用promise.then(resolve)来调用resolve方法的promise。此时我们使用`Promise.resolve`。

简单地说，Promise.resolve(params)会返回一个promise，当你使用`promise.then(resolve_func)`的时候，相当于触发了`resolve_func(params)`，如果params本身是一个Promise对象，会直接返回这个对象，如果传入的是一个promise-like的对象，会被转换成一个Promise对象后返回。

###并发请求

我们的要求是，并发请求多个信息，并按**顺序**，**尽快**将得到的内容显示在页面上。

~~~js
getJSON('story.json').then(function(story) {
  addHtmlToPage(story.heading);

  // Map our array of chapter urls to
  // an array of chapter json promises.
  // This makes sure they all download parallel.
  return story.chapterUrls.map(getJSON)
    .reduce(function(sequence, chapterPromise) {
      // Use reduce to chain the promises together,
      // adding content to the page for each chapter
      return sequence.then(function() {
        // Wait for everything in the sequence so far,
        // then wait for this chapter to arrive.
        return chapterPromise;
      }).then(function(chapter) {
        addHtmlToPage(chapter.html);
      });
    }, Promise.resolve());
}).then(function() {
  addTextToPage("All done");
}).catch(function(err) {
  // catch any error that happened along the way
  addTextToPage("Argh, broken: " + err.message);
}).then(function() {
  document.querySelector('.spinner').style.display = 'none';
});
~~~

对上面代码的理解：

首先通过`story.chapterUrls.map(getJSON)`并行发起对所有页面的请求。同时得到一个由`getJSON Promise`构成的队列。基于这个队列进行reduce。reduce的参数是一个立即settle的Promise对象sequence。通过在这个sequence对象的then方法里返回charpterPromise并串联一个将charpterPromise的返回内容添加到页面上的then方法。这样处理就会等待charpterPromise的完成。添加完毕后又将sequence返回给reduce函数，进行下一个charpter的处理。这样的话，每个chapter的请求过程是并行的，但是聚合的过程却是按序的。达到了我们的要求。

（按：所以说我们为什么需要立即settle的Promise的原因其实是需要在它的then方法里等待另外一个promise的完成，并在完成后返回自身？或者说我的疑问是，`Promise.resolve`有没有在map-reduce之外的用法？）