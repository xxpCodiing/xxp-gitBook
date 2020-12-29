# javascript 设计模式
## 单例模式
`保证只有一个实例，并提供一个访问它的全局访问点`
- 实现1:对象字面量
```
var singleton = {
    attr : 1,
    method : function(){ return this.attr; }
}
var t1 = singleton ;
var t2 = singleton ;
那么很显然的， t1 === t2 。
不足之处在于没有什么封装性，所有的属性方法都是暴露的
```
- 实现2：构造函数内部判断
```
function Construct(){
    // 确保只有单例
    if( Construct.unique !== undefined ){
        return Construct.unique; 
    }
    // 其他代码
    this.name = "NYF";
    this.age="24";
    Construct.unique = this;
}
var t1 = new Construct() ;
var t2 = new Construct() ;
 那么也有的， t1 === t2 。
 无非就是提出一个属性来做判断，但是该方式也没有安全性，一旦我在外部修改了Construct的unique属性，那么单例模式也就被破坏了。
```
- 实现3：闭包方式
```
var single = (function(){
    var unique;
    function Construct(){
        // ... 生成单例的构造函数的代码
    }
    unique = new Constuct();
    return unique;
})();
var t1 = single; 
var t2 = single;
不过相对而言更安全一点，当然也不是绝对安全。
如果希望会用调用 single() 方式来使用，那么也只需要将内部的 return 改为
return function(){
    return unique;
} 
```
## 策略模式
## 代理模式
## 迭代器模式
## 观察者模式
`观察者模式又称“发布-订阅（Publish/Subscribe）模式”`
```
/**发布者**/
function Publisher(){
    this.observers = [];
    this.state = "";
}
// 新增观察者
Publisher.prototype.addOb=function(observer){
    var flag = false;
    for (var i = this.observers.length - 1; i >= 0; i--) {
        if(this.observers[i]===observer){
            flag=true;                
        }
    };
    if(!flag){
        this.observers.push(observer);
    }
    return this;
}
// 删除观察者
Publisher.prototype.removeOb=function(observer){
    var observers = this.observers;
    for (var i = 0; i < observers.length; i++) {
        if(observers[i]===observer){
            observers.splice(i,1);
        }
    };
    return this;
}
// 发布通知
Publisher.prototype.notice=function(){
    var observers = this.observers;
    for (var i = 0; i < observers.length; i++) {
            observers[i].update(this.state);
    };
}
/*********************************************************/
/**订阅者**/
function Subscribe(){
    this.update = function(data){
          console.log(data);
    };
}
/*********************************应用实现***************************************/
 var oba = new Subscribe(),
  obb = new Subscribe();
var pba = new Publisher();
pba.addOb(oba);
pba.addOb(obb);
oba.update = function(state){
  console.log(state+"hello!");
}
obb.update = function(state){
  console.log(state+"world!");
}
pba.state = "open ";
pba.notice();
```
`大家看到，我们在最后对发布者手动设置了它的内容（state）并且要求他发出通知（notice）。在实际项目中，发布者的内容可能是从后台获取的也可能是从前台某地方输入的。然而发布者每次更新内容后又要手动调用通知是不是有点多余呢？既然更新了内容那就肯定要通知别人了啊。那我们就把内容的更新与发出通知进行绑定好了.看下面的代码`
```
//发布者
function Publisher(){
    this.observers = [];
    var state = "";     //让该内容不能直接访问
    //新增两个对于state的操作 获取/更新
    this.getState=function(){
        return state;
    }
    this.setState=function(value){
        state = value;
        this.notice();
    }
}
Publisher.prototype.addOb=function(observer){
    var flag = false;
    for (var i = this.observers.length - 1; i >= 0; i--) {
        if(this.observers[i]===observer){
            flag=true;                
        }
    };
    if(!flag){
        this.observers.push(observer);
    }
    return this;
}
Publisher.prototype.removeOb=function(observer){
    var observers = this.observers;
    for (var i = 0; i < observers.length; i++) {
        if(observers[i]===observer){
            observers.splice(i,1);
        }
    };
    return this;
}
Publisher.prototype.notice=function(){
    var observers = this.observers;
    for (var i = 0; i < observers.length; i++) {
            observers[i].update(this.getState()); //获取发布者的内容
    };
}
//订阅者
function Subscribe(){
    this.update = function(data){
          console.log(data);
    };
}
//实际应用
var oba = new Subscribe(),
    obb = new Subscribe();
var pba = new Publisher();
pba.addOb(oba);
pba.addOb(obb);
oba.update = function(state){
    console.log(state+"hello!");
}
obb.update = function(state){
    console.log(state+"world!");
}
pba.setState("open "); //发布者更新了内容
```
`实际应用`
```
// 全局发布-订阅对象
let Event = (function () {
  // 缓存列表，存放订阅者的回调函数
  let clientList = {}
  let listen, trigger, remove
  // 增加订阅者
  listen = (key, fn) => {
    if (!clientList[key]) {
      clientList[key] = []
    }
    clientList[key].push(fn)
  },
  // 发布消息
  trigger = (...value) => {
    let key = Array.prototype.shift.call(value)
    let fns = clientList[key]
    // 如果没有绑定的对应的消息
    if (!fns || fns.length === 0) {
      return false
    }
    for ( let i = 0, fn; fn = fns[i++]; ) {
      fn.apply(this, value)
    }
  },
  // 取消订阅事件
  remove = (key, fn) => {
    let fns = clientList[key]
    // 如果key对应的消息没有被人订阅，则直接返回
    if (!fns) {
      return false
    }
    if (!fn) { // 如果没有传入具体的回调函数，表示需要取消key对应消息的所有订阅
      fns && (fns.length = 0)
    } else {
      for (let l = fns.length - 1; l >= 0; l--) {
        let _fn = fns[l]
        if (_fn === fn) {
          fns.splice(l, 1) // 删除订阅者的回调函数
        }
      }
    }
  }
  return {
    listen,
    trigger,
    remove
  }
})()
// 小明订阅消息
Event.listen('squareMeter88', fn1 = function (price, squareMeter) {
  console.log('小明先生：')
  console.log('price = ' + price)
  console.log('squareMeter = ' + squareMeter)
})
// 小红订阅消息
Event.listen('squareMeter88', fn2 = function (price, squareMeter) {
  console.log('小红小姐：')
  console.log('price = ' + price)
  console.log('squareMeter = ' + squareMeter)
})
// 售楼处发布消息
Event.trigger('squareMeter88', 10000, 88)
Event.remove('squareMeter88', fn1)
Event.trigger('squareMeter88', 15000, 88)
```
## 命令模式
## 组合模式
## 模板方式模式
## 享元模式
## 职责链模式
## 中介模式
## 装饰者模式
## 状态模式
## 适配器模式

## 好莱坞原则
## 依赖倒置原则
## 控制反转
## 依赖注入