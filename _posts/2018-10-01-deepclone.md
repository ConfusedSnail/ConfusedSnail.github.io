---
layout: post
title: 深拷贝
tags: JavaScript
math: true
date: 2018-10-03 15:32 +0800
---
<p data-nodeid="1042">我把深浅拷贝单独作为一讲来专门讲解，是因为在 JavaScript 的编程中经常需要对数据进行复制，什么时候用深拷贝、什么时候用浅拷贝，是开发过程中需要思考的；同时深浅拷贝也是前端面试中比较高频的题目。</p>
<p data-nodeid="1043">但是我在面试候选人的过程中，发现有很多同学都没有搞懂深拷贝和浅拷贝的区别和定义。最近我也在一些关于 JavaScript 的技术文章中发现，里面很多关于深浅拷贝的代码写得比较简陋，从面试官的角度来讲，简陋的答案是不太能让人满意的。</p>
<p data-nodeid="1044">因此，深入学习这部分知识有助于提高你手写 JS 的能力，以及对一些边界特殊情况的深入思考能力，这一讲我会结合最基础但是又容易写不好的的题目来帮助你提升。</p>
<p data-nodeid="1045">在开始之前，我先抛出来两个问题，你可以思考一下。</p>
<ol data-nodeid="1046">
<li data-nodeid="1047">
<p data-nodeid="1048">拷贝一个很多嵌套的对象怎么实现？</p>
</li>
<li data-nodeid="1049">
<p data-nodeid="1050">在面试官眼中，写成什么样的深拷贝代码才能算合格？</p>
</li>
</ol>
<p data-nodeid="1051">带着这两个问题，我们先来看下浅拷贝的相关内容。</p>
<h3 data-nodeid="1052">浅拷贝的原理和实现</h3>
<p data-nodeid="1053">对于浅拷贝的定义我们可以初步理解为：</p>
<blockquote data-nodeid="1054">
<p data-nodeid="1055">自己创建一个新的对象，来接受你要重新复制或引用的对象值。如果对象属性是基本的数据类型，复制的就是基本类型的值给新对象；但如果属性是引用数据类型，复制的就是内存中的地址，如果其中一个对象改变了这个内存中的地址，肯定会影响到另一个对象。</p>
</blockquote>
<p data-nodeid="1056">下面我总结了一些 JavaScript 提供的浅拷贝方法，一起来看看哪些方法能实现上述定义所描述的过程。</p>
<h4 data-nodeid="1057">方法一：object.assign</h4>
<p data-nodeid="1058">object.assign 是 ES6 中 object 的一个方法，该方法可以用于 JS 对象的合并等多个用途，其中一个用途就是可以进行浅拷贝。该方法的第一个参数是拷贝的目标对象，后面的参数是拷贝的来源对象（也可以是多个来源）。</p>
<blockquote data-nodeid="1059">
<p data-nodeid="1060">object.assign 的语法为：Object.assign(target, ...sources)</p>
</blockquote>
<p data-nodeid="1061">object.assign 的示例代码如下：</p>
<pre class="lang-javascript" data-nodeid="1062"><code data-language="javascript"><span class="hljs-keyword">let</span> target = {};
<span class="hljs-keyword">let</span> source = { <span class="hljs-attr">a</span>: { <span class="hljs-attr">b</span>: <span class="hljs-number">1</span> } };
<span class="hljs-built_in">Object</span>.assign(target, source);
<span class="hljs-built_in">console</span>.log(target); <span class="hljs-comment">// { a: { b: 1 } };</span>
</code></pre>
<p data-nodeid="1063">从上面的代码中可以看到，通过 object.assign 我们的确简单实现了一个浅拷贝，“target”就是我们新拷贝的对象，下面再看一个和上面不太一样的例子。</p>
<pre class="lang-javascript" data-nodeid="1064"><code data-language="javascript"><span class="hljs-keyword">let</span> target = {};
<span class="hljs-keyword">let</span> source = { <span class="hljs-attr">a</span>: { <span class="hljs-attr">b</span>: <span class="hljs-number">2</span> } };
<span class="hljs-built_in">Object</span>.assign(target, source);
<span class="hljs-built_in">console</span>.log(target); <span class="hljs-comment">// { a: { b: 10 } }; </span>
source.a.b = <span class="hljs-number">10</span>; 
<span class="hljs-built_in">console</span>.log(source); <span class="hljs-comment">// { a: { b: 10 } }; </span>
<span class="hljs-built_in">console</span>.log(target); <span class="hljs-comment">// { a: { b: 10 } };</span>
</code></pre>
<p data-nodeid="1065">从上面代码中我们可以看到，首先通过 Object.assign 将 source 拷贝到 target 对象中，然后我们尝试将 source 对象中的 b 属性由 2 修改为 10。通过控制台可以发现，打印结果中，三个 target 里的 b 属性都变为 10 了，证明 Object.assign 暂时实现了我们想要的拷贝效果。</p>
<p data-nodeid="1066">但是使用 object.assign 方法有几点需要注意：</p>
<ul data-nodeid="1067">
<li data-nodeid="1068">
<p data-nodeid="1069">它不会拷贝对象的继承属性；</p>
</li>
<li data-nodeid="1070">
<p data-nodeid="1071">它不会拷贝对象的不可枚举的属性；</p>
</li>
<li data-nodeid="1072">
<p data-nodeid="1073">可以拷贝 Symbol 类型的属性。</p>
</li>
</ul>
<p data-nodeid="1074">可以简单理解为：Object.assign 循环遍历原对象的属性，通过复制的方式将其赋值给目标对象的相应属性，来看一下这段代码，以验证它可以拷贝 Symbol 类型的对象。</p>
<pre class="lang-javascript" data-nodeid="1075"><code data-language="javascript"><span class="hljs-keyword">let</span> obj1 = { <span class="hljs-attr">a</span>:{ <span class="hljs-attr">b</span>:<span class="hljs-number">1</span> }, <span class="hljs-attr">sym</span>:<span class="hljs-built_in">Symbol</span>(<span class="hljs-number">1</span>)}; 
<span class="hljs-built_in">Object</span>.defineProperty(obj1, <span class="hljs-string">'innumerable'</span> ,{
    <span class="hljs-attr">value</span>:<span class="hljs-string">'不可枚举属性'</span>,
    <span class="hljs-attr">enumerable</span>:<span class="hljs-literal">false</span>
});
<span class="hljs-keyword">let</span> obj2 = {};
<span class="hljs-built_in">Object</span>.assign(obj2,obj1)
obj1.a.b = <span class="hljs-number">2</span>;
<span class="hljs-built_in">console</span>.log(<span class="hljs-string">'obj1'</span>,obj1);
<span class="hljs-built_in">console</span>.log(<span class="hljs-string">'obj2'</span>,obj2);
</code></pre>
<p data-nodeid="1076">我们来看一下控制台打印的结果，如下图所示。</p>
<p data-nodeid="1077"><img src="https://s0.lgstatic.com/i/image2/M01/04/DD/CgpVE1_23KWABxpDAAC0XN0mEv4915.png" alt="Drawing 0.png" data-nodeid="1207"></p>
<p data-nodeid="1078">从上面的样例代码中可以看到，利用 object.assign 也可以拷贝 Symbol 类型的对象，但是如果到了对象的第二层属性 obj1.a.b 这里的时候，前者值的改变也会影响后者的第二层属性的值，说明其中依旧存在着访问共同堆内存的问题，也就是说这种方法还不能进一步复制，而只是完成了浅拷贝的功能。</p>
<h4 data-nodeid="1079">方法二：扩展运算符方式</h4>
<p data-nodeid="1080">我们也可以利用 JS 的扩展运算符，在构造对象的同时完成浅拷贝的功能。</p>
<blockquote data-nodeid="1081">
<p data-nodeid="1082">扩展运算符的语法为：let cloneObj = { ...obj };</p>
</blockquote>
<p data-nodeid="1083">代码如下所示。</p>
<pre class="lang-javascript" data-nodeid="1084"><code data-language="javascript"><span class="hljs-comment">/* 对象的拷贝 */</span>
<span class="hljs-keyword">let</span> obj = {<span class="hljs-attr">a</span>:<span class="hljs-number">1</span>,<span class="hljs-attr">b</span>:{<span class="hljs-attr">c</span>:<span class="hljs-number">1</span>}}
<span class="hljs-keyword">let</span> obj2 = {...obj}
obj.a = <span class="hljs-number">2</span>
<span class="hljs-built_in">console</span>.log(obj)  <span class="hljs-comment">//{a:2,b:{c:1}} console.log(obj2); //{a:1,b:{c:1}}</span>
obj.b.c = <span class="hljs-number">2</span>
<span class="hljs-built_in">console</span>.log(obj)  <span class="hljs-comment">//{a:2,b:{c:2}} console.log(obj2); //{a:1,b:{c:2}}</span>
<span class="hljs-comment">/* 数组的拷贝 */</span>
<span class="hljs-keyword">let</span> arr = [<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>];
<span class="hljs-keyword">let</span> newArr = [...arr]; <span class="hljs-comment">//跟arr.slice()是一样的效果</span>
</code></pre>
<p data-nodeid="1085">扩展运算符 和 object.assign 有同样的缺陷，也就是实现的浅拷贝的功能差不多，但是如果属性都是基本类型的值，使用扩展运算符进行浅拷贝会更加方便。</p>
<h4 data-nodeid="1086">方法三：concat 拷贝数组</h4>
<p data-nodeid="1087">数组的 concat 方法其实也是浅拷贝，所以连接一个含有引用类型的数组时，需要注意修改原数组中的元素的属性，因为它会影响拷贝之后连接的数组。不过 concat 只能用于数组的浅拷贝，使用场景比较局限。代码如下所示。</p>
<pre class="lang-javascript" data-nodeid="1088"><code data-language="javascript"><span class="hljs-keyword">let</span> arr = [<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>];
<span class="hljs-keyword">let</span> newArr = arr.concat();
newArr[<span class="hljs-number">1</span>] = <span class="hljs-number">100</span>;
<span class="hljs-built_in">console</span>.log(arr);  <span class="hljs-comment">// [ 1, 2, 3 ]</span>
<span class="hljs-built_in">console</span>.log(newArr); <span class="hljs-comment">// [ 1, 100, 3 ]</span>
</code></pre>
<h4 data-nodeid="1089">方法四：slice 拷贝数组</h4>
<p data-nodeid="1090">slice 方法也比较有局限性，因为它仅仅针对数组类型。slice 方法会返回一个新的数组对象，这一对象由该方法的前两个参数来决定原数组截取的开始和结束时间，是不会影响和改变原始数组的。</p>
<blockquote data-nodeid="1091">
<p data-nodeid="1092">slice 的语法为：arr.slice(begin, end);</p>
</blockquote>
<p data-nodeid="1093">我们来看一下 slice 怎么使用，代码如下所示。</p>
<pre class="lang-javascript" data-nodeid="1094"><code data-language="javascript"><span class="hljs-keyword">let</span> arr = [<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, {<span class="hljs-attr">val</span>: <span class="hljs-number">4</span>}];
<span class="hljs-keyword">let</span> newArr = arr.slice();
newArr[<span class="hljs-number">2</span>].val = <span class="hljs-number">1000</span>;
<span class="hljs-built_in">console</span>.log(arr);  <span class="hljs-comment">//[ 1, 2, { val: 1000 } ]</span>
</code></pre>
<p data-nodeid="1095">从上面的代码中可以看出，这就是浅拷贝的限制所在了——它只能拷贝一层对象。如果存在对象的嵌套，那么浅拷贝将无能为力。因此深拷贝就是为了解决这个问题而生的，它能解决多层对象嵌套问题，彻底实现拷贝。这一讲的后面我会介绍深拷贝相关的内容。</p>
<h3 data-nodeid="1096">手工实现一个浅拷贝</h3>
<p data-nodeid="1097">根据以上对浅拷贝的理解，如果让你自己实现一个浅拷贝，大致的思路分为两点：</p>
<ol data-nodeid="1098">
<li data-nodeid="1099">
<p data-nodeid="1100">对基础类型做一个最基本的一个拷贝；</p>
</li>
<li data-nodeid="1101">
<p data-nodeid="1102">对引用类型开辟一个新的存储，并且拷贝一层对象属性。</p>
</li>
</ol>
<p data-nodeid="1103">那么，围绕着这两个思路，请你跟着我的操作，自己来实现一个浅拷贝吧，代码如下所示。</p>
<pre class="lang-javascript" data-nodeid="1104"><code data-language="javascript"><span class="hljs-keyword">const</span> shallowClone = <span class="hljs-function">(<span class="hljs-params">target</span>) =&gt;</span> {
  <span class="hljs-keyword">if</span> (<span class="hljs-keyword">typeof</span> target === <span class="hljs-string">'object'</span> &amp;&amp; target !== <span class="hljs-literal">null</span>) {
    <span class="hljs-keyword">const</span> cloneTarget = <span class="hljs-built_in">Array</span>.isArray(target) ? []: {};
    <span class="hljs-keyword">for</span> (<span class="hljs-keyword">let</span> prop <span class="hljs-keyword">in</span> target) {
      <span class="hljs-keyword">if</span> (target.hasOwnProperty(prop)) {
          cloneTarget[prop] = target[prop];
      }
    }
    <span class="hljs-keyword">return</span> cloneTarget;
  } <span class="hljs-keyword">else</span> {
    <span class="hljs-keyword">return</span> target;
  }
}
</code></pre>
<p data-nodeid="1105">从上面这段代码可以看出，利用类型判断，针对引用类型的对象进行 for 循环遍历对象属性赋值给目标对象的属性，基本就可以手工实现一个浅拷贝的代码了。</p>
<p data-nodeid="1106">那么了解了实现浅拷贝代码的思路，接下来我们再看看深拷贝是怎么实现的。</p>
<h3 data-nodeid="1107">深拷贝的原理和实现</h3>
<p data-nodeid="1108">浅拷贝只是创建了一个新的对象，复制了原有对象的基本类型的值，而引用数据类型只拷贝了一层属性，再深层的还是无法进行拷贝。深拷贝则不同，对于复杂引用数据类型，其在堆内存中完全开辟了一块内存地址，并将原有的对象完全复制过来存放。</p>
<p data-nodeid="1109">这两个对象是相互独立、不受影响的，彻底实现了内存上的分离。总的来说，深拷贝的原理可以总结如下：</p>
<blockquote data-nodeid="1110">
<p data-nodeid="1111">将一个对象从内存中完整地拷贝出来一份给目标对象，并从堆内存中开辟一个全新的空间存放新对象，且新对象的修改并不会改变原对象，二者实现真正的分离。</p>
</blockquote>
<p data-nodeid="1112">现在原理你知道了，那么怎么去实现深拷贝呢？我也总结了几种方法分享给你。</p>
<h4 data-nodeid="1301" class="">方法一：乞丐版（JSON.stringify）</h4>

<p data-nodeid="1823" class="">JSON.stringify() 是目前开发过程中最简单的深拷贝方法，其实就是把一个对象序列化成为 JSON 的字符串，并将对象里面的内容转换成字符串，最后再用 JSON.parse() 的方法将JSON 字符串生成一个新的对象。示例代码如下所示。</p>

<pre class="lang-javascript" data-nodeid="1115"><code data-language="javascript"><span class="hljs-keyword">let</span> obj1 = { <span class="hljs-attr">a</span>:<span class="hljs-number">1</span>, <span class="hljs-attr">b</span>:[<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>] }
<span class="hljs-keyword">let</span> str = <span class="hljs-built_in">JSON</span>.stringify(obj1)；
<span class="hljs-keyword">let</span> obj2 = <span class="hljs-built_in">JSON</span>.parse(str)；
<span class="hljs-built_in">console</span>.log(obj2);   <span class="hljs-comment">//{a:1,b:[1,2,3]} </span>
obj1.a = <span class="hljs-number">2</span>；
obj1.b.push(<span class="hljs-number">4</span>);
<span class="hljs-built_in">console</span>.log(obj1);   <span class="hljs-comment">//{a:2,b:[1,2,3,4]}</span>
<span class="hljs-built_in">console</span>.log(obj2);   <span class="hljs-comment">//{a:1,b:[1,2,3]}</span>
</code></pre>
<p data-nodeid="2345" class="">从上面的代码可以看到，通过 JSON.stringify 可以初步实现一个对象的深拷贝，通过改变 obj1 的 b 属性，其实可以看出 obj2 这个对象也不受影响。</p>

<p data-nodeid="2867" class="">但是使用 JSON.stringify 实现深拷贝还是有一些地方值得注意，我总结下来主要有这几点：</p>

<ol data-nodeid="1118">
<li data-nodeid="1119">
<p data-nodeid="1120">拷贝的对象的值中如果有函数、undefined、symbol 这几种类型，经过 JSON.stringify 序列化之后的字符串中这个键值对会消失；</p>
</li>
<li data-nodeid="1121">
<p data-nodeid="1122">拷贝 Date 引用类型会变成字符串；</p>
</li>
<li data-nodeid="1123">
<p data-nodeid="1124">无法拷贝不可枚举的属性；</p>
</li>
<li data-nodeid="1125">
<p data-nodeid="1126">无法拷贝对象的原型链；</p>
</li>
<li data-nodeid="1127">
<p data-nodeid="1128">拷贝 RegExp 引用类型会变成空对象；</p>
</li>
<li data-nodeid="1129">
<p data-nodeid="1130">对象中含有 NaN、Infinity 以及 -Infinity，JSON 序列化的结果会变成 null；</p>
</li>
<li data-nodeid="1131">
<p data-nodeid="1132">无法拷贝对象的循环应用，即对象成环 (obj[key] = obj)。</p>
</li>
</ol>
<p data-nodeid="3389" class="">针对这些存在的问题，你可以尝试着用下面的这段代码亲自执行一遍，来看看如此复杂的对象，如果用 JSON.stringify 实现深拷贝会出现什么情况。</p>

<pre class="lang-javascript" data-nodeid="1134"><code data-language="javascript"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">Obj</span>(<span class="hljs-params"></span>) </span>{ 
  <span class="hljs-keyword">this</span>.func = <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params"></span>) </span>{ alert(<span class="hljs-number">1</span>) }; 
  <span class="hljs-keyword">this</span>.obj = {<span class="hljs-attr">a</span>:<span class="hljs-number">1</span>};
  <span class="hljs-keyword">this</span>.arr = [<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>];
  <span class="hljs-keyword">this</span>.und = <span class="hljs-literal">undefined</span>; 
  <span class="hljs-keyword">this</span>.reg = <span class="hljs-regexp">/123/</span>; 
  <span class="hljs-keyword">this</span>.date = <span class="hljs-keyword">new</span> <span class="hljs-built_in">Date</span>(<span class="hljs-number">0</span>); 
  <span class="hljs-keyword">this</span>.NaN = <span class="hljs-literal">NaN</span>;
  <span class="hljs-keyword">this</span>.infinity = <span class="hljs-literal">Infinity</span>;
  <span class="hljs-keyword">this</span>.sym = <span class="hljs-built_in">Symbol</span>(<span class="hljs-number">1</span>);
} 
<span class="hljs-keyword">let</span> obj1 = <span class="hljs-keyword">new</span> Obj();
<span class="hljs-built_in">Object</span>.defineProperty(obj1,<span class="hljs-string">'innumerable'</span>,{ 
  <span class="hljs-attr">enumerable</span>:<span class="hljs-literal">false</span>,
  <span class="hljs-attr">value</span>:<span class="hljs-string">'innumerable'</span>
});
<span class="hljs-built_in">console</span>.log(<span class="hljs-string">'obj1'</span>,obj1);
<span class="hljs-keyword">let</span> str = <span class="hljs-built_in">JSON</span>.stringify(obj1);
<span class="hljs-keyword">let</span> obj2 = <span class="hljs-built_in">JSON</span>.parse(str);
<span class="hljs-built_in">console</span>.log(<span class="hljs-string">'obj2'</span>,obj2);
</code></pre>
<p data-nodeid="1135">通过上面这段代码可以看到执行结果如下图所示。</p>
<p data-nodeid="1136"><img src="https://s0.lgstatic.com/i/image/M00/8D/03/CgqCHl_23LaAXa7jAAFYsb1e_kA876.png" alt="Drawing 1.png" data-nodeid="1252"></p>
<p data-nodeid="1137">使用 JSON.stringify 方法实现深拷贝对象，虽然到目前为止还有很多无法实现的功能，但是这种方法足以满足日常的开发需求，并且是最简单和快捷的。而对于其他的也要实现深拷贝的，比较麻烦的属性对应的数据类型，JSON.stringify 暂时还是无法满足的，那么就需要下面的几种方法了。</p>
<h4 data-nodeid="1138" class="">方法二：基础版（手写递归实现）</h4>
<p data-nodeid="1139" class="">下面是一个实现 deepClone 函数封装的例子，通过 for in 遍历传入参数的属性值，如果值是引用类型则再次递归调用该函数，如果是基础数据类型就直接复制，代码如下所示。</p>
<pre class="lang-javascript" data-nodeid="1140"><code data-language="javascript"><span class="hljs-keyword">let</span> obj1 = {
  <span class="hljs-attr">a</span>:{
    <span class="hljs-attr">b</span>:<span class="hljs-number">1</span>
  }
}
<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">deepClone</span>(<span class="hljs-params">obj</span>) </span>{ 
  <span class="hljs-keyword">let</span> cloneObj = {}
  <span class="hljs-keyword">for</span>(<span class="hljs-keyword">let</span> key <span class="hljs-keyword">in</span> obj) {                 <span class="hljs-comment">//遍历</span>
    <span class="hljs-keyword">if</span>(<span class="hljs-keyword">typeof</span> obj[key] ===<span class="hljs-string">'object'</span>) { 
      cloneObj[key] = deepClone(obj[key])  <span class="hljs-comment">//是对象就再次调用该函数递归</span>
    } <span class="hljs-keyword">else</span> {
      cloneObj[key] = obj[key]  <span class="hljs-comment">//基本类型的话直接复制值</span>
    }
  }
  <span class="hljs-keyword">return</span> cloneObj
}
<span class="hljs-keyword">let</span> obj2 = deepClone(obj1);
obj1.a.b = <span class="hljs-number">2</span>;
<span class="hljs-built_in">console</span>.log(obj2);   <span class="hljs-comment">//  {a:{b:1}}</span>
</code></pre>
<p data-nodeid="3911" class="te-preview-highlight">虽然利用递归能实现一个深拷贝，但是同上面的 JSON.stringify 一样，还是有一些问题没有完全解决，例如：</p>

<ol data-nodeid="1142">
<li data-nodeid="1143">
<p data-nodeid="1144">这个深拷贝函数并不能复制不可枚举的属性以及 Symbol 类型；</p>
</li>
<li data-nodeid="1145">
<p data-nodeid="1146">这种方法只是针对普通的引用类型的值做递归复制，而对于 Array、Date、RegExp、Error、Function 这样的引用类型并不能正确地拷贝；</p>
</li>
<li data-nodeid="1147">
<p data-nodeid="1148">对象的属性里面成环，即循环引用没有解决。</p>
</li>
</ol>
<p data-nodeid="1149">这种基础版本的写法也比较简单，可以应对大部分的应用情况。但是你在面试的过程中，如果只能写出这样的一个有缺陷的深拷贝方法，有可能不会通过。</p>
<p data-nodeid="1150">所以为了“拯救”这些缺陷，下面我带你一起看看改进的版本，以便于你可以在面试种呈现出更好的深拷贝方法，赢得面试官的青睐。</p>
<h4 data-nodeid="1151">方法三：改进版（改进后递归实现）</h4>
<p data-nodeid="1152">针对上面几个待解决问题，我先通过四点相关的理论告诉你分别应该怎么做。</p>
<ol data-nodeid="1153">
<li data-nodeid="1154">
<p data-nodeid="1155">针对能够遍历对象的不可枚举属性以及 Symbol 类型，我们可以使用 Reflect.ownKeys 方法；</p>
</li>
<li data-nodeid="1156">
<p data-nodeid="1157">当参数为 Date、RegExp 类型，则直接生成一个新的实例返回；</p>
</li>
<li data-nodeid="1158">
<p data-nodeid="1159">利用 Object 的 getOwnPropertyDescriptors 方法可以获得对象的所有属性，以及对应的特性，顺便结合 Object 的 create 方法创建一个新对象，并继承传入原对象的原型链；</p>
</li>
<li data-nodeid="1160">
<p data-nodeid="1161">利用 WeakMap 类型作为 Hash 表，因为 WeakMap 是弱引用类型，可以有效防止内存泄漏（你可以关注一下 Map 和 weakMap 的关键区别，这里要用 weakMap），作为检测循环引用很有帮助，如果存在循环，则引用直接返回 WeakMap 存储的值。</p>
</li>
</ol>
<p data-nodeid="1162">关于第 4 点的 WeakMap，这里我不进行过多的科普讲解了，你如果不清楚可以自己再通过相关资料了解一下。我也经常在给人面试中看到有人使用 WeakMap 来解决循环引用问题，但是很多解释都是不够清晰的。</p>
<p data-nodeid="1163">当你不太了解 WeakMap 的真正作用时，我建议你不要在面试中写出这样的代码，如果只是死记硬背，会给自己挖坑的。<strong data-nodeid="1274">因为你写的每一行代码都是需要经过深思熟虑并且非常清晰明白的，这样你才能经得住面试官的推敲</strong>。</p>
<p data-nodeid="1164">当然，如果你在考虑到循环引用的问题之后，还能用 WeakMap 来很好地解决，并且向面试官解释这样做的目的，那么你所展示的代码，以及你对问题思考的全面性，在面试官眼中应该算是合格的了。</p>
<p data-nodeid="1165">那么针对上面这几个问题，我们来看下改进后的递归实现的深拷贝代码应该是什么样子的，如下所示。</p>
<pre class="lang-javascript" data-nodeid="1166"><code data-language="javascript"><span class="hljs-keyword">const</span> isComplexDataType = <span class="hljs-function"><span class="hljs-params">obj</span> =&gt;</span> (<span class="hljs-keyword">typeof</span> obj === <span class="hljs-string">'object'</span> || <span class="hljs-keyword">typeof</span> obj === <span class="hljs-string">'function'</span>) &amp;&amp; (obj !== <span class="hljs-literal">null</span>)
<span class="hljs-keyword">const</span> deepClone = <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params">obj, hash = new WeakMap(</span>)) </span>{
  <span class="hljs-keyword">if</span> (obj.constructor === <span class="hljs-built_in">Date</span>) 
  <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> <span class="hljs-built_in">Date</span>(obj)       <span class="hljs-comment">// 日期对象直接返回一个新的日期对象</span>
  <span class="hljs-keyword">if</span> (obj.constructor === <span class="hljs-built_in">RegExp</span>)
  <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> <span class="hljs-built_in">RegExp</span>(obj)     <span class="hljs-comment">//正则对象直接返回一个新的正则对象</span>
  <span class="hljs-comment">//如果循环引用了就用 weakMap 来解决</span>
  <span class="hljs-keyword">if</span> (hash.has(obj)) <span class="hljs-keyword">return</span> hash.get(obj)
  <span class="hljs-keyword">let</span> allDesc = <span class="hljs-built_in">Object</span>.getOwnPropertyDescriptors(obj)
  <span class="hljs-comment">//遍历传入参数所有键的特性</span>
  <span class="hljs-keyword">let</span> cloneObj = <span class="hljs-built_in">Object</span>.create(<span class="hljs-built_in">Object</span>.getPrototypeOf(obj), allDesc)
  <span class="hljs-comment">//继承原型链</span>
  hash.set(obj, cloneObj)
  <span class="hljs-keyword">for</span> (<span class="hljs-keyword">let</span> key <span class="hljs-keyword">of</span> <span class="hljs-built_in">Reflect</span>.ownKeys(obj)) { 
    cloneObj[key] = (isComplexDataType(obj[key]) &amp;&amp; <span class="hljs-keyword">typeof</span> obj[key] !== <span class="hljs-string">'function'</span>) ? deepClone(obj[key], hash) : obj[key]
  }
  <span class="hljs-keyword">return</span> cloneObj
}
<span class="hljs-comment">// 下面是验证代码</span>
<span class="hljs-keyword">let</span> obj = {
  <span class="hljs-attr">num</span>: <span class="hljs-number">0</span>,
  <span class="hljs-attr">str</span>: <span class="hljs-string">''</span>,
  <span class="hljs-attr">boolean</span>: <span class="hljs-literal">true</span>,
  <span class="hljs-attr">unf</span>: <span class="hljs-literal">undefined</span>,
  <span class="hljs-attr">nul</span>: <span class="hljs-literal">null</span>,
  <span class="hljs-attr">obj</span>: { <span class="hljs-attr">name</span>: <span class="hljs-string">'我是一个对象'</span>, <span class="hljs-attr">id</span>: <span class="hljs-number">1</span> },
  <span class="hljs-attr">arr</span>: [<span class="hljs-number">0</span>, <span class="hljs-number">1</span>, <span class="hljs-number">2</span>],
  <span class="hljs-attr">func</span>: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params"></span>) </span>{ <span class="hljs-built_in">console</span>.log(<span class="hljs-string">'我是一个函数'</span>) },
  <span class="hljs-attr">date</span>: <span class="hljs-keyword">new</span> <span class="hljs-built_in">Date</span>(<span class="hljs-number">0</span>),
  <span class="hljs-attr">reg</span>: <span class="hljs-keyword">new</span> <span class="hljs-built_in">RegExp</span>(<span class="hljs-string">'/我是一个正则/ig'</span>),
  [<span class="hljs-built_in">Symbol</span>(<span class="hljs-string">'1'</span>)]: <span class="hljs-number">1</span>,
};
<span class="hljs-built_in">Object</span>.defineProperty(obj, <span class="hljs-string">'innumerable'</span>, {
  <span class="hljs-attr">enumerable</span>: <span class="hljs-literal">false</span>, <span class="hljs-attr">value</span>: <span class="hljs-string">'不可枚举属性'</span> }
);
obj = <span class="hljs-built_in">Object</span>.create(obj, <span class="hljs-built_in">Object</span>.getOwnPropertyDescriptors(obj))
obj.loop = obj    <span class="hljs-comment">// 设置loop成循环引用的属性</span>
<span class="hljs-keyword">let</span> cloneObj = deepClone(obj)
cloneObj.arr.push(<span class="hljs-number">4</span>)
<span class="hljs-built_in">console</span>.log(<span class="hljs-string">'obj'</span>, obj)
<span class="hljs-built_in">console</span>.log(<span class="hljs-string">'cloneObj'</span>, cloneObj)
</code></pre>
<p data-nodeid="1167">我们看一下结果，cloneObj 在 obj 的基础上进行了一次深拷贝，cloneObj 里的 arr 数组进行了修改，并未影响到 obj.arr 的变化，如下图所示。</p>
<p data-nodeid="1168"><img src="https://s0.lgstatic.com/i/image/M00/8D/03/CgqCHl_23NqAajeeAAIH4UWV0LE467.png" alt="Drawing 2.png" data-nodeid="1280"></p>
<p data-nodeid="1169">从这张截图的结果可以看出，改进版的 deepClone 函数已经对基础版的那几个问题进行了改进，也验证了我上面提到的那四点理论。</p>
<p data-nodeid="1170">那么到这里，深拷贝的相关内容就介绍得差不多了。</p>
<h3 data-nodeid="1171">总结</h3>
<p data-nodeid="1172">这一讲，我们探讨了如何实现一个深浅拷贝。在日常的开发中，由于开发者可以使用一些现成的库来实现深拷贝，所以很多人对如何实现深拷贝的细节问题并不清楚。但是如果仔细研究你就会发现，这部分内容对于你深入了解 JS 底层的原理有很大帮助。如果未来你需要自己实现一个前端相关的工具或者库，对 JS 理解的深度会决定你能把这个东西做得有多好。</p>
<p data-nodeid="1173">其实到最后我们可以看到，自己完整实现一个深拷贝，还是考察了不少的知识点和编程能力，总结下来大致分为这几点，请看下图。</p>
<p data-nodeid="1174"><img src="https://s0.lgstatic.com/i/image2/M01/05/11/Cip5yF_7s86AOlltAAEet-x_UAc883.png" alt="图片4.png" data-nodeid="1288"></p>
<p data-nodeid="1175">可以看到通过这一个问题能考察的能力有很多，因此千万不要用最低的标准来要求自己，应该用类似的方法去分析每个问题深入考察的究竟是什么，这样才能更好地去全面提升自己的基本功。</p>
<p data-nodeid="1176">关于深浅拷贝如果你有不清楚的地方，欢迎在评论区留言，最好的建议还是要多动手，不清楚的地方自己敲一遍代码，这样才能加深印象，然后更容易地去消化这部分内容。</p>
