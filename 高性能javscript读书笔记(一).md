# 从今天起2017-06-29开始每天都读这本书。

### (一) 加载和执行（loading and execution）。：略

### (二)  数据访问（data access）2017/6/30

  2.1 js中数据存储位置对访问速度有很大影响，js中有四种存储类型：1.直接量。2. 变量。3.数组项。4.对象成员。
  
  2.2 作用域scope，作用域链chain，标识符identifier resolution，活动对象activation（运行期上下文execution context）
  
  2.3 闭包cluster 着重理解和外部函数，既作用域链的关系和理解，常驻内存，因为一旦函数运行完，释放作用域链，但闭包的作用域链还在引用，这不会释放，直到闭       包运行完，（闭包运行时候，又会创造一个一级的活动对象active Object,搜索过程是自己的active bject ,然后外部函数的第一级active Object,然后三级全       局变量的active Object
  2.4 原型和原型链
  
### (三) DOM访问 （2017/7/3）

### (四) 算法和流程控制 （algorithms and  flow control） 
  4.1 js中有四种loop for while do-while for-in 
  4.2 js 中基于函数的迭代 forEach是面向数组对象的
  ```javascript
  items.forEach(function(value,index,array){
  process(value);
  });
  ```
  等价于jquer中的each函数
  
  ```javascript
  JQuery.each(items,function(index,value){
  process(value);
  });
  ```
  4.3 条件表达式（conditionals）
  
     4.3.1 查找表（lookup table）
     
```javascript
var results=[result1,result2,result3,...,result11];
return results[value];
```
     
  4.4 recurson递归和迭代
  
  合并排序
  
```javascript
function merge(left,right){
  var result=[];
  while(left.length>0&&right.length>0){
    if(left[0]<right[0]){
      result.push(left.shift());      
    }else{
      result.push(right.shift());
    }
  }
  return result.concat(left).concat(right);
}

function mergeSort(items){
  if(items.length==1){
    return items;
  }else{
    var middle=Math.floor(items.length/2);
    var left=items.slice(0,middle);
    var right=items.slice(middle);
    return merge(mergeSort(left),mergeSort(right));
  }
};
var array=[123,22,44,1213,444,990,12,9];
mergeSort(array);
0:9
1:12
2:22
3:44
4:123
5:444
6:990
7:1213
```
  4.5 memoization (制表)默记
  制表法写factorial函数
```javascript
  function memoizationFactorial(n){
    if(!memoizationFactorial.cache){
      memoizationFactorial.cache={
        "0":1,
        "1":1
      };
    }
    if(!memoizationFactorial.cache.hasOwnProperty(n)){
      memoizationFactorial.cache[n]=n*memoizationFactorial(n-1);
    }
    return memoizationFactorial.cache[n];
  }
```
```javascript

function memorization(fundamental, cache) {
	var cache = cache || {};
	var shell = function(arg) {
		if (!cache.hasOwnProperty(arg)) {
			cache[arg] = fundamental(arg);
		}
		return cache[arg];
	};
	return shell;
}
// memorizal function
var memFactorial = memorization(factorial, {
	"0" : 1,
	"1" : 1
});
memFactorial(6);
memFactorial(5);
memFactorial(4);
// call the new function
function factorial(n) {
	if (n == 0) {
		return 1;
	} else {
		return n * factorial(n - 1);
	}
}
```
### （五） 字符串和正则表达式
### （六） 响应接口（responsive interfaces）
### （七） Ajax 异步javascript和XML

