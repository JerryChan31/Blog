- [每个开发者都应该知道的SOLID原则](#%E6%AF%8F%E4%B8%AA%E5%BC%80%E5%8F%91%E8%80%85%E9%83%BD%E5%BA%94%E8%AF%A5%E7%9F%A5%E9%81%93%E7%9A%84solid%E5%8E%9F%E5%88%99)
  - [单一职责原则（SRP）](#%E5%8D%95%E4%B8%80%E8%81%8C%E8%B4%A3%E5%8E%9F%E5%88%99srp)
    - [它为什么违反了 SRP？](#%E5%AE%83%E4%B8%BA%E4%BB%80%E4%B9%88%E8%BF%9D%E5%8F%8D%E4%BA%86-srp)
    - [这种设计将来会带来什么问题？](#%E8%BF%99%E7%A7%8D%E8%AE%BE%E8%AE%A1%E5%B0%86%E6%9D%A5%E4%BC%9A%E5%B8%A6%E6%9D%A5%E4%BB%80%E4%B9%88%E9%97%AE%E9%A2%98)
  - [开闭原则（OCP）](#%E5%BC%80%E9%97%AD%E5%8E%9F%E5%88%99ocp)
    - [如何使它（AnimalSound）符合 OCP？](#%E5%A6%82%E4%BD%95%E4%BD%BF%E5%AE%83animalsound%E7%AC%A6%E5%90%88-ocp)
  - [里氏替换原则（LSP）](#%E9%87%8C%E6%B0%8F%E6%9B%BF%E6%8D%A2%E5%8E%9F%E5%88%99lsp)
  - [接口隔离原则（ISP）](#%E6%8E%A5%E5%8F%A3%E9%9A%94%E7%A6%BB%E5%8E%9F%E5%88%99isp)
  - [依赖倒置原则（DIP）](#%E4%BE%9D%E8%B5%96%E5%80%92%E7%BD%AE%E5%8E%9F%E5%88%99dip)
  - [小结](#%E5%B0%8F%E7%BB%93)

# 每个开发者都应该知道的SOLID原则

> 转载自[此](https://mp.weixin.qq.com/s/GFABaqzGKloPDZZu-Tc1jQ)，原作者为 Chidume Nnamdi。



面向对象编程带来了新的软件开发设计方法。它使得开发人员能够将具有相同作用 / 功能的数据组合到一个类中，实现唯一的目的，而不管整个应用程序如何。

但是，这种面向对象的编程并不能防止难以理解或不可维护的程序。因此，Robert C. Martin 制定了五项指导原则，使开发人员很容易创建出可读性强且可维护的程序。这五项原则被称为 **S.O.L.I.D 原则**（这种缩写是由 Michael Feathers 提出的）：

 - **S：单一职责原则**
 - **O：开闭原则**
 - **L：里氏替换原则**
 - **I：接口隔离原则**
 - **D：依赖倒置原则**

下面我们将展开详细的讨论。
注意：本文中的大多数示例可能不能满足实际情况或不能应用于实际的应用程序。这完全取决于你自己的设计和场景。最重要的是**理解并知道如何应用 / 遵循这些原则**。
提示：SOLID 原则是为**构建模块化、可扩展和可组合的封装组件**而设计的。

## 单一职责原则（SRP）
一个类只应该负责一件事。如果一个类有多个职责，那么它变成了耦合的。对一个职责的修改会导致对另一个职责的修改。
注意：这个原则不仅适用于类，也适用于软件组件和微服务。
例如，考虑下面的设计：
```JavaScript
class Animal {
    constructor(name: string){ }
    getAnimalName() { }
    saveAnimal(a: Animal) { }
}
```
上面的 Animal 就违反了单一职责原则（SRP）。

### 它为什么违反了 SRP？
SRP 指出，类应该有一个职责，在这里，我们可以得出两个职责：动物数据库管理和动物属性管理。构造函数和 getAnimalName 管理动物属性，而 saveAnimal 管理 Animal 在数据库中的存储。
### 这种设计将来会带来什么问题？
如果应用程序的修改影响了数据库管理功能，使用 Animal 属性的类就必须修改和重新编译，以适应这种新的变化。这个系统就有点像多米诺骨牌，触碰一张牌就会影响到其他牌。

为了使这个类符合 SRP，我们创建了另一个类，它负责将动物存储到数据库中这个单独的职责：
```JavaScript
class Animal {
    constructor(name: string){ }
    getAnimalName() { }
}
class AnimalDB {
    getAnimal(a: Animal) { }
    saveAnimal(a: Animal) { }
}
```
在设计我们的类时，我们应该把相关的特性放在一起，这样，每当它们需要改变的时候，它们都是因为同样的原因而改变。如果它们因不同的原因而改变，我们就应该尝试将它们分开。——Steve Fenton

恰当运用这条原则，我们的应用程序就会变成高内聚的。

## 开闭原则（OCP）
软件实体（类、模块、函数）应该对扩展开放，对修改关闭。
让我们继续以 Animal 类为例。
```JavaScript
class Animal {
    constructor(name: string){ }
    getAnimalName() { }
}
```
我们希望遍历一个动物列表，发出它们的声音。
```JavaScript
//...
const animals: Array<Animal> = [
    new Animal('lion'),
    new Animal('mouse')
];
function AnimalSound(a: Array<Animal>) {
    for(int i = 0; i <= a.length; i++) {
        if(a[i].name == 'lion')
            log('roar');
        if(a[i].name == 'mouse')
            log('squeak');
    }
}
AnimalSound(animals);
```
函数 AnimalSound 不符合开闭原则，因为它不能对新的动物关闭。如果我们添加一种新的动物蛇：
```JavaScript
//...
const animals: Array<Animal> = [
    new Animal('lion'),
    new Animal('mouse'),
    new Animal('snake')
]
//...
```
我们就不得不修改 AnimalSound 函数：
```JavaScript
//...
function AnimalSound(a: Array<Animal>) {
    for(int i = 0; i <= a.length; i++) {
        if(a[i].name == 'lion')
            log('roar');
        if(a[i].name == 'mouse')
            log('squeak');
        if(a[i].name == 'snake')
            log('hiss');
    }
}
AnimalSound(animals);
```
如你所见，对于每一种新的动物，一段新的逻辑会被添加到 AnimalSound 函数。这是一个非常简单的例子。当应用程序变得庞大而复杂时，你会看到，每添加一种新动物，if 语句就得在 AnimalSound 函数中重复一遍。
### 如何使它（AnimalSound）符合 OCP？

```JavaScript
class Animal {
        makeSound();
        //...
}
class Lion extends Animal {
    makeSound() {
        return 'roar';
    }
}
class Squirrel extends Animal {
    makeSound() {
        return 'squeak';
    }
}
class Snake extends Animal {
    makeSound() {
        return 'hiss';
    }
}
//...
function AnimalSound(a: Array<Animal>) {
    for(int i = 0; i <= a.length; i++) {
        log(a[i].makeSound());
    }
}
AnimalSound(animals);
```

Animal 现在有了一个虚方法 makeSound。我们让每一种动物扩展 Animal 类并实现 makeSound 方法。
每一种动物都加入自己的发声方法（makeSound）实现。AnimalSound 遍历动物数组并调用每种动物的 makeSound 方法。
现在，如果我们添加一种新动物，AnimalSound 不需要修改。我们需要做的就是把新动物加入到动物数组中。
AnimalSound 方法符合 OCP 原则了。
再举个例子。假如你有一家商店，你使用下面的类给自己最喜欢的客户 20% 的折扣：
```JavaScript
class Discount {
    giveDiscount() {
        return this.price * 0.2
    }
}
```
当你决定给 VIP 客户双倍的折扣（40%）时，你可能会这样修改这个类：
```JavaScript
class Discount {
    giveDiscount() {
        if(this.customer == 'fav') {
            return this.price * 0.2;
        }
        if(this.customer == 'vip') {
            return this.price * 0.4;
        }
    }
}
```
这就违反了 OCP 原则。OCP 禁止这样做。如果想给不同类型的客户一个新的折扣百分比，就得添加一段新的逻辑。
为了使它遵循 OCP 原则，我们将新建一个类来扩展 Discount。在这个新类中，我们将重新实现它的行为：
```JavaScript
class VIPDiscount: Discount {
    getDiscount() {
        return super.getDiscount() * 2;
    }
}
```
如果你决定给超级 VIP 客户 80% 的折扣，那么代码是下面这个样子：
```JavaScript
class SuperVIPDiscount: VIPDiscount {
    getDiscount() {
        return super.getDiscount() * 2;
    }
}
```
就是这样，扩展而不修改。

## 里氏替换原则（LSP）
子类必须可以替换它的超类。
这个原则的目的是确保子类可以替换它的超类而没有错误。如果你发现自己的代码在检查类的类型，那么它一定违反了这个原则。
让我们以 Animal 为例。
```JavaScript
//...
function AnimalLegCount(a: Array<Animal>) {
    for(int i = 0; i <= a.length; i++) {
        if(typeof a[i] == Lion)
            log(LionLegCount(a[i]));
        if(typeof a[i] == Mouse)
            log(MouseLegCount(a[i]));
        if(typeof a[i] == Snake)
            log(SnakeLegCount(a[i]));
    }
}
AnimalLegCount(animals);
```
上述方法违反了 LSP 原则（也违反了 OCP 原则）。它必须知道每一种 Animal 类型，并调用相应的数腿函数。
每次创建一个新的动物类，都得修改这个函数：
```JavaScript
//...
class Pigeon extends Animal {

}
const animals[]: Array<Animal> = [
    //...,
    new Pigeon();
]
function AnimalLegCount(a: Array<Animal>) {
    for(int i = 0; i <= a.length; i++) {
        if(typeof a[i] == Lion)
            log(LionLegCount(a[i]));
        if(typeof a[i] == Mouse)
            log(MouseLegCount(a[i]));
         if(typeof a[i] == Snake)
            log(SnakeLegCount(a[i]));
        if(typeof a[i] == Pigeon)
            log(PigeonLegCount(a[i]));
    }
}
AnimalLegCount(animals);
```
为了使这个函数符合 LSP 原则，我们将遵循 Steve Fenton 提出的 LSP 要求：
如果超类（Animal）有一个方法接受超类类型（Anima）的参数，那么它的子类（Pigeon）应该接受超类类型（Animal 类型）或子类类型（Pigeon 类型）作为参数。
如果超类返回一个超类类型（Animal）, 那么它的子类应该返回一个超类类型（Animal 类型）或子类类型（Pigeon 类型）。
现在，我们可以重新实现 AnimalLegCount 函数了：
```JavaScript
function AnimalLegCount(a: Array<Animal>) {
    for(let i = 0; i <= a.length; i++) {
        a[i].LegCount();
    }
}
AnimalLegCount(animals);
```
AnimalLegCount 函数并不关心传递的动物类型，它只管调用 LegCount 方法。它只知道参数必须是 Animal 类型，要么是 Animal 类，要么是它的子类。
现在，Animal 类必须实现 / 定义一个 LegCount 方法：
```JavaScript
class Animal {
    //...
    LegCount();
}
```
而它的子类必须实现 LegCount 方法：
```JavaScript
//...
class Lion extends Animal{
    //...
    LegCount() {
        //...
    }
}
//...
```
当它被传递给 AnimalLegCount 函数时，它会返回一头狮子的腿数。
如你所见，AnimalLegCount 不需要知道动物的类型就可以返回它的腿数，它只调用了 Animal 类型的 LegCount 方法，因为根据约定，Animal 类的一个子类必须实现 LegCount 函数。

## 接口隔离原则（ISP）
创建特定于客户端的细粒度接口。不应该强迫客户端依赖于它们不使用的接口。
这个原则是为了克服实现大接口的缺点。让我们看看下面的 IShape 接口：
```JavaScript
interface IShape {
    drawCircle();
    drawSquare();
    drawRectangle();
}
```
这个接口可以绘制正方形、圆形、矩形。实现 IShape 接口的类 Circle、Square 和 Rectangle 必须定义方法 drawCircle()、drawSquare()、drawRectangle()。
```JavaScript
class Circle implements IShape {
    drawCircle(){
        //...
    }
    drawSquare(){
        //...
    }
    drawRectangle(){
        //...
    }    
}
class Square implements IShape {
    drawCircle(){
        //...
    }
    drawSquare(){
        //...
    }
    drawRectangle(){
        //...
    }    
}
class Rectangle implements IShape {
    drawCircle(){
        //...
    }
    drawSquare(){
        //...
    }
    drawRectangle(){
        //...
    }    
}
```
上面的代码很有趣。类 Rectangle 实现了它没有使用的方法 drawCircle 和 drawSquare，同样，Square 实现了 drawCircle 和 drawRectangle，Circle 实现了 drawSquare 和 drawRectangle。
如果我们向 IShape 接口添加另一个方法，比如 drawTriangle()：
```JavaScript
interface IShape {
    drawCircle();
    drawSquare();
    drawRectangle();
    drawTriangle();
}
```
那么，这些类就必须实现新方法，否则就会抛出错误。
我们看到，不可能实现这样一种形状类，它可以画圆，但不能画矩形、正方形或三角形。我们在实现方法时可以只抛出一个错误，表明操作无法执行。
ISP 反对 IShape 接口的这种设计。客户端（这里是 Rectangle、Circle 和 Square）不应该被迫依赖于它们不需要或不使用的方法。另外，ISP 指出，接口应该只执行一个任务（就像 SRP 原则一样），任何额外的行为都应该抽象到另一个接口中。
在这里，我们的 IShape 接口执行了应该由其他接口独立处理的操作。为了使 IShape 接口符合 ISP 原则，我们将对不同接口的操作进行隔离：
```JavaScript
interface IShape {
    draw();
}
interface ICircle {
    drawCircle();
}
interface ISquare {
    drawSquare();
}
interface IRectangle {
    drawRectangle();
}
interface ITriangle {
    drawTriangle();
}
class Circle implements ICircle {
    drawCircle() {
        //...
    }
}
class Square implements ISquare {
    drawSquare() {
        //...
    }
}
class Rectangle implements IRectangle {
    drawRectangle() {
        //...
    }    
}
class Triangle implements ITriangle {
    drawTriangle() {
        //...
    }
}
class CustomShape implements IShape {
   draw(){
      //...
   }
}
```
ICircle 接口仅处理圆的绘制，IShape 处理任何形状的绘制，ISquare 只处理正方形的绘制，IRectangle 处理矩形的绘制。
或者，类（Circle、Rectangle、Square、Triangle）必须继承 IShape 接口，并实现自己的绘制行为。
```JavaScript
class Circle implements IShape {
    draw(){
        //...
    }
}

class Triangle implements IShape {
    draw(){
        //...
    }
}

class Square implements IShape {
    draw(){
        //...
    }
}

class Rectangle implements IShape {
    draw(){
        //...
    }
}  
```                                          
然后，我们可以使用 I- 接口创建具体的形状，如半圆、直角三角形、等边三角形、钝边矩形等。
## 依赖倒置原则（DIP）
依赖应该是抽象的，而不是具体的。
高级模块不应该依赖于低级模块。两者都应该依赖于抽象。
抽象不应该依赖于细节。细节应该依赖于抽象。
在软件开发中，我们的应用程序最终主要是由模块组成。当这种情况出现时，我们必须使用依赖注入来解决。高级组件依赖于低级组件发挥作用。
```JavaScript
class XMLHttpService extends XMLHttpRequestService {}
class Http {
    constructor(private xmlhttpService: XMLHttpService) { }
    get(url: string , options: any) {
        this.xmlhttpService.request(url,'GET');
    }
    post() {
        this.xmlhttpService.request(url,'POST');
    }
    //...
}
```
这里，Http 是高级组件，而 HttpService 是低级组件。这种设计违反了 DIP A：高级模块不应该依赖于低级模块。它应该依赖于它的抽象。
该 Http 类被迫依赖于 XMLHttpService 类。如果我们要修改 Http 连接服务，也许我们想通过 Nodejs 连接到互联网，甚至模拟 http 服务。我们将不得不费力地遍历所有 Http 实例来编辑代码，这违反了 OCP 原则。
Http 类不应该关心使用的 Http 服务的类型。我们做了一个 Connection 接口：
```JavaScript
interface Connection {
    request(url: string, opts:any);
}
```
Connection 接口有一个 request 方法。有了这个接口，我们就可以向 Http 类传递一个 Connection 类型的参数：
```JavaScript
class Http {
    constructor(private httpConnection: Connection) { }
    get(url: string , options: any) {
        this.httpConnection.request(url,'GET');
    }
    post() {
        this.httpConnection.request(url,'POST');
    }
    //...
}
```
因此，无论传递给 Http 类的 Http 连接服务是什么类型，它都可以轻松地连接到网络，而无需知道网络连接的类型。
现在，我们重新实现 XMLHttpService 类来实现 Connection 接口：
```JavaScript
class XMLHttpService implements Connection {
    const xhr = new XMLHttpRequest();
    //...
    request(url: string, opts:any) {
        xhr.open();
        xhr.send();
    }
}
```
我们可以创建许多 Http 连接类型，并将其传递给 Http 类，而不必担心错误。
```JavaScript
class NodeHttpService implements Connection {
    request(url: string, opts:any) {
        //...
    }
}
class MockHttpService implements Connection {
    request(url: string, opts:any) {
        //...
    }    
}
```
现在，我们可以看到，高级模块和低级模块都依赖于抽象。Http 类（高级模块）依赖于 Connection 接口（抽象），而 Http 服务类型（低级模块）也依赖于 Connection 接口（抽象）。
此外，DIP 原则会强制我们遵循里氏替换原则：Connection 类型 Node-XML-MockHttpService 可以替换它们的父类型连接。

## 小结
本文介绍了每个软件开发人员都必须遵守的五个原则。首先，要遵守所有这些原则可能会令人生畏，但是随着不断的实践和坚持，它们会成为我们的一部分，并将对应用程序的维护产生巨大的影响。
关于这些原则，如果你觉得有什么需要添加、纠正或删除，请在下面的评论区留言，我非常乐意与你讨论！
英文原文：
https://blog.bitsrc.io/solid-principles-every-developer-should-know-b3bfa96bb688