---
title: "TypeScript特性"
date: 2024-03-31 08:38:13
tags: [web,TypeScript]
categories: 笔记
toc: true
cover: https://cdn.thenewstack.io/media/2022/01/10b88c68-typescript-logo.png
---
# TypeScript联合类型
联合类型(Union Types)可以通过管道**|**将变量设置多种类型。赋值时可以根据设置的类型来赋值。

```
Type1|Type2|Type3
```

## 实例
这里看一个实例：
```
let a: number|string;
a = 12;
console.log(a);
a  = "hello";
console.log(a);
```

也可以将联合类型作为函数参数使用：
```
function disp(name:string|string[]) { 
        if(typeof name == "string") { 
                console.log(name) 
        } else { 
                var i; 
                for(i = 0;i<name.length;i++) { 
                console.log(name[i])
                } 
        } 
} 
disp("Runoob") 
console.log("输出数组....") 
disp(["Runoob","Google","Taobao","Facebook"])
```
## 联合类型数组
也可以将联合类型声明为一个数组，这样这个数组就可以作为多个类型的数组。

注意，数组中的元素仍然只能是相同类型。

# TypeScript接口
接口是一系列抽象方法的声明，是一些方法特征的集合，这些方法都应该是抽象的，需要由具体的类去实现，然后第三方就可以通过这组抽象方法调用，让具体的类执行具体的方法。

```
interface interface_name { 
}
```

## 实例
```
interface IPerson { 
    firstName:string, 
    lastName:string, 
    sayHi: ()=>string 
} 
 
var customer:IPerson = { 
    firstName:"Tom",
    lastName:"Hanks", 
    sayHi: ():string =>{return "Hi there"} 
} 
 
console.log("Customer 对象 ") 
console.log(customer.firstName) 
console.log(customer.lastName) 
console.log(customer.sayHi())  
 
var employee:IPerson = { 
    firstName:"Jim",
    lastName:"Blakes", 
    sayHi: ():string =>{return "Hello!!!"} 
} 
 
console.log("Employee  对象 ") 
console.log(employee.firstName) 
console.log(employee.lastName)
```

注意，这里的接口不能转换为JavaScript，其只能帮助TypeScript检查代码。

从以上实例看出，接口定义了某一种类的结构，但其中的具体实现需要在初始化对象时确定。

## 接口与数组
特别的，我们可以在接口中为数组设置**索引签名**，索引值可以是数字或者字符串。这样我们可以相在不使用**map**的情况下在一定程度上自定义索引值。

```
interface ages { 
   [index:string]:number 
} 
 
var agelist:ages; 
 // 类型正确 
agelist["runoob"] = 15  
 
// 类型错误，输出  error TS2322: Type '"google"' is not assignable to type 'number'.
// agelist[2] = "google"
```

## 接口继承
接口继承就是说接口可以通过其他接口来扩展自己。Typescript 允许接口继承多个接口。

使用关键字`extends`

```
Child_interface_name extends super_interface_name
```

```
Child_interface_name extends super_interface1_name, super_interface2_name,…,super_interfaceN_name
```
继承的各个接口使用`,`区分。

### 实例
```
interface Person { 
   age:number 
} 
 
interface Musician extends Person { 
   instrument:string 
} 
 
var drummer = <Musician>{}; 
drummer.age = 27 
drummer.instrument = "Drums" 
console.log("年龄:  "+drummer.age)
console.log("喜欢的乐器:  "+drummer.instrument)
```

# TypeScript类
TypeScript 是面向对象的 JavaScript。

类描述了所创建的对象共同的属性和方法。

TypeScript 支持面向对象的所有特性，比如 类、接口等。

注意，这里的类不同于接口，会在JavaScript中以原型链和构造器的形式进行实现。

```
class class_name { 
    // 类作用域
}
```

类可以包括**字段(成员变量)、构造函数、方法。**

## 创建类的数据成员
```
class Car { 
    // 字段 
    engine:string; 
 
    // 构造函数 
    constructor(engine:string) { 
        this.engine = engine 
    }  
 
    // 方法 
    disp():void { 
        console.log("发动机为 :   "+this.engine) 
    } 
}
```
这里其实和c++中的类十分相似。这里构造函数的名称为**constructor**。

## 创建实例化对象
使用`new`关键字来实例话对象，语法如下：
```
var object_name = new class_name([ arguments ])
```
类实例化的时候会调用构造函数，类中的属性和方法可以使用`.`来访问。

## 类的继承
类中的继承关键字和接口一样，为`extends`，子类除了不能继承父类的私有成员，其他的都能继承。

TypeScript一次只能继承一个类，但是可以继承很多代。

### 实例
```
class Shape { 
   Area:number 
   
   constructor(a:number) { 
      this.Area = a 
   } 
} 
 
class Circle extends Shape { 
   disp():void { 
      console.log("圆的面积:  "+this.Area) 
   } 
}
  
var obj = new Circle(223); 
obj.disp()
```
注意，这里没有为子类再写一个构造函数，而是直接调用的父类的构造函数，那么，若子类有新的属性需要初始化怎么办呢❓

这时，在子类中再写一个构造函数并在其中使用`super`关键字调用父类的构造函数即可。

子类会继承父类的构造函数。在TypeScript中，当你创建子类时，如果子类有自己的构造函数，它必须通过super()调用父类的构造函数来初始化父类的属性。这确保了子类实例化时父类的构造函数也被执行，父类的属性被正确初始化。

## 继承类的方法重写
类继承后，子类可以对父类的方法重新定义，这个过程称之为重写。

其中`super`关键字是对父类的直接引用，该关键字可以引用父类的属性和方法。(用于子类中有和父类同名的重写函数，这是无法通过函数名直接调用父类的成员)。

## 访问控制修饰符
TypeScript中默认的访问控制为`public`，其实他还有`protected`、`private`。

以上

## 类和接口
类可以实现接口，使用关键字`implements`，并将接口字段作为类的属性使用。
```
interface ILoan { 
   interest:number 
} 
 
class AgriLoan implements ILoan { 
   interest:number 
   rebate:number 
   
   constructor(interest:number,rebate:number) { 
      this.interest = interest 
      this.rebate = rebate 
   } 
} 
 
var obj = new AgriLoan(10,1) 
console.log("利润为 : "+obj.interest+"，抽成为 : "+obj.rebate )
```

类中必须包含接口所含有的结构，此外，还可以有自己衍生的成员。