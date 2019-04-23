JavaScript

[大小写敏感]

数据：

- 简单数据

  1. undefined

     typeof(undefined)的返回值是undefined，可以将undefined赋值给任何变量、属性，但并不意味着清楚了该变量，反而会多了属性

  2. null

     typeof(null)返回值是object，但null并非object，具有null值的变量也并非object

  3. boolean

  4. number

     typeof(NaN)和typeof(Infinity)都返回number。NaN参与任何值计算的结构都是NaN，NaN ！= NaN，Infinity / Infinity = NaN

  5. string

- 复杂数据

  object

typeof(x)：获得数据类型

typeof(function名)：返回值function

undefined, null, " ", 0, flase转换为逻辑值时就是false。出了undefined == null之外，其他几个互不相等。

===：全等：数据值与类型值都相等

- 用eval()函数动态执行的代码并不创建新的作用域，其代码就是在当前作用域中执行的。eval()中的动态代码可以访问到当前作用域的this，arguments等对象，因此可以eval()实现一些高级的多态和动态拓展方面的应用
- arguments的caller，callee属性已作废

~~~javascript

    function Person(name)
    {
        this.name = name;
        this.SayHello= function()
        {
            alert("Hello,I'm " + this.name);
        };
    };
    function Employee(name, salary)
    {
        Person.call(this,name);
        this.salary = salary;
        this.ShowMeTheMoney = function()
        {
            alert(this.name + "$" + this.salary);
        };
    };
    
    var BillGates = new Person("Bill Gayes");
    var SteveJobs = new Employee("Steve Jobs",1234);
    BillGates.SayHello();
    SteveJobs.SayHello();
    SteveJobs.ShowMeTheMoney();
    
    alert(BillGates.constructor == Person);
    alert(SteveJobs.constructor == Employee);
    alert(BillGates.SayHello == SteveJobs.SayHello);
~~~

~~~javascript
P41
function Person(name)
{
	this.name = name;
};
Person.prototype.SayHello = function()
{
	alert("Hello,I'm" + this.name);
};
var BillGates = new Person("Bill Gates");
Person.prototype.Retire = function()
{
    alert("Poor " + this.name + ",bye bye.")
};
BillGates.SayHello();
BillGates.Retire();
//可以随时给原型对象动态添加新的属性和方法，从而动态地扩展基类的功能特性。
~~~

~~~javascript
function Person(name)
{
    this.name = name;
};
Person.prototype.SayHello = function()
{
    alert("Hello,I'm " + this.name);
}
var BillGates = new Person("Bill Gates");
var SteveJobs = new Person("Steve Jobs");

BillGates.SayHello();
SteveJobs.SayHello();
alert(BillGates.SayHello == SteveJobs.SayHello);
~~~

~~~javascript
function Person(name)
{
    this.name = name;
};
Person.prototype.SayHello = function()
{
    alert("Hello, I'm " + this.name);
};
function Employee(name,salary)
{
    Person.call(this,name);
    this.salary = salary;
};
Employee.prototype = new Person();
Employee.prototype.ShowMeTheMoney = function()
{
    alert(this.name + "$" + this.salary);
};
var BillGates = new Person("Bill Gates");
var SteveJobs = new Employee("Steve Jobs",1234);

BillGates.SayHello();
SteveJobs.SayHello();
SteveJobs.ShowMeTheMoney();
alert(BillGates.SayHello == SteveJobs.SayHello);
~~~

~~~javascript
P48
function Person(name)
{
    this.name = name;
};
Person.prototype.company = "Microsoft";//原型的属性
Person.prototype.SayHello = function()//原型的方法
{
    alert("Hello, I'm " + this.name + " of " + this.company);
};
var BillGates = new Person("Bill Gates");
BillGates.SayHello();//由于继承了原型的东西，输出Hello，I'm BillGates of Microsoft
var SteveJobs = new Person("Steve Jobs");
SteveJobs.company = "Apple";//设置自己的company属性，掩盖原型的company属性
SteveJobs.SayHello = function()//实现自己的SayHello方法，掩盖了原型的SayHello方法
{
    alert("Hi, " + this.name + "like " + this.company + ",hahaha");
};
SteveJobs.SayHello();//输出Hi，Steve Jobs like Apple，hahaha
BillGates.SayHello();//SteveJobs的覆盖没有影响原型对象，按老样子输出
//对象可以掩盖原型对象的属性和方法，一个构造对象也可以掩盖上层构造函数原型对象已有的属性和方法。
~~~

~~~javascript
P51
function Person(firstName, lastName, age)
{
    //私有变量
    var _firstName = firstName;
    var _lastName = lastName;
    //公共变量
    this.age = age;
    //方法
    this.getName = function()
    {
        return(firstName + " " + lastName);
    };
    this.SayHello = function()
    {
        alert("Hello,I'm " + firstName + " " + lastName);
    };
};
var BillGates = new Person("Bill", "Gates", 53);
var SteveJobs = new Person("Steve", "Jobs",  44);
BillGates.SayHello();
SteveJobs.SayHello();
alert(BillGates.getName() + " " + BillGates.age);
alert(BillGates._firstName);//这里不能访问到函数的私有变量
~~~

1. “闭包模型”，就是在构造函数体内定义另外的函数作为目标对象的方法函数，而这个对象的方法函数反过来引用外层函数体中的临时变量。就能间接保持原构造函数体用到的临时变量。

   存在垃圾回收方面的内存泄漏问题

2. “原型模型”，需要构造函数来定义对象的成员，方法依附在构造函数的原型上。

   prototype：原型，提供了一群同类对象共享属性和方法的机制

~~~javascript
P52
//定义构造函数
function Person(name)
{
    this.name = name;//在构造函数中定义成员
};
//方法定义到构造函数的prototy上
Person.prototype.SayHello = function()
{
    alert("Hello,I'm " + this.name);
};
//子类的构造函数
function Employee(name,salary)
{
    Person.call(this,name);//调用上层构造函数
    this.salary = salary;//拓展的成员
};
//子类构造函数首先需要用上层构造函数来建立prototype对象，实现继承的概念
Employee.prototype = new Person()//只需要prototype方法

//子类方法也定义到构造函数之上
Employee.prototype.ShowMeTheMoney = function()
{
    alert(this.name + "$" + this.salary);
};
var BillGates = new Person("Bill Gates");
BillGates.SayHello();
//BillGates.ShowMeTheMoney();ShowMeTheMoney就是Employee的私有函数
var SteveJobs = new Employee("Steve Jobs",1234);
SteveJobs.SayHello();
SteveJobs.ShowMeTheMoney();
~~~

var anObject = new aFunction()形式创建对象分三步

1. 建立一个新的对象
2. 将该对象内置的原型对象设置为构造函数prototype引用的那个原型对象
3. 将该对象作为this参数调用构造函数，完成成员设置等初始化工作

对象建立之后，对象上的任何访问和操作都只与对象自身及其原型链上的那串对象有关，与构造函数无关

~~~javascript
P56
function New(aClass, aParams)//通用创建函数
//New就是一个“语法甘露”，不但中转了原型对象，还中转了构造函数逻辑及构造参数
{
    function new_()//定义临时的中转函数壳
    {
        aClass.Create.apply(this.aParams);
        //调用原型中定义的构造函数
        //中转构造逻辑及构造参数
    };
    new_.prototype = aClass;//准备中转原型对象
    return new new_();//返回简历最终建立的对象
};
var Person//定义的类
    {
        Create:function(name, age)
        {
            this.name = name;
            this.age = age;
        },
        SayHello:function()
        {
            alert("Hello,I'm " + this.name);
        },
        HowOld:function()
        {
            alert(this.name + " is " + this.age + " years old.");
        }
    };
//调用通用函数创建对象
//并以数组形式传递构造参数
var BillGates = New(Person,["Bill Gates", 53]);
BillGates.SayHello();
BillGates.HowOld();
alert(BillGates.constructor == Object);//输出：true
~~~

~~~javascript
P57
var object = 
{
    isA:function(aType)
    {
        var self = this;
        while(self)
        {
            if(self == aType)
                return true;
            self = self.Type;
        };
        return false;
    }
};
function Class(aBaseClass, aClassDefine)
{
    function class_()
    {
        this.Type = aBaseClass;
        for(var member in aClassDefine)
            this[member] = aClassDefine[member];
    };
    class_.prototype = aBaseClass;
    return new class_();
};

function New(aClass, aParams)
{
    function new_()
    {
        this.Type = aClass;
        if(aClass.Create)
            aClass.Create.apply(this.aParams);
    };
    new_.prototype = aClass;
    return new new_();
};
var Person = Class(object,
{
    Create:function(name, age)
    {
        this.name =name;
        this.age = age;
    },
    SayHello:function()
    {
        alert("Hello, I'm " + this.name + ", " + this.age + " yearsold.");
    }
});
var Employee = Class(Person,
{
    Create:function(name, age, salary)
    {
        Person.Create.call(this.name, age);
        this.salary = salary;
    },
    ShowMeTheMoney:function()
    {
        alert(this.name + " $ " + this.salary);
    }
});

var BillGates = New(Person, ["Bill Gates", 53]);
var SteveJobs  = New(Employee, ["Steve Jobs", 44, 1234]);
BillGates.SayHello();//hello,i'm undefined,undefined yearsold
SteveJobs.SayHello();//hello,i'm undefined,undefined yearsold
SteveJobs.ShowMeTheMoney();//undefined $ undefined

var LittleBill = New(BillGates.Type, ["Little Bill", 6]);
LittleBill.SayHello();

alert(BillGates.isA(Person));//true
alert(BillGates.isA(Employee));//false
alert(SteveJobs.isA(Person));//true
alert(Person.isA(Employee));//false
alert(Employee.isA(Person));//true
~~~

P60：精简甘露模型

~~~javascript
function Class()
//定义语法甘露Class（）
//最后一个参数是JSON表示的类定义
//如果参数数量大于1个，则第一个参数是基类
//第一个和最后一个之间的参数，将来可以表示类实现的接口
//返回值是类，类是一个构造函数
{
    var aDefine = arguments[arguments.length-1];//最后一个参数是类定义
    if(!aDefine) return;
    var aBase = arguments.length > 1 ? arguments[0] : object;//解析基类
    function prototype_(){}//构造prototype的临时函数，用于挂接原型链
    prototype_.prototype = aBase.prototype;//准备传递prototype
    var aPrototype = new prototype_();//建立类要用的prototype
    
    for(var member in aDefine)//复制类定义到当前类的prototype
    	if(member != "Create")//构造函数不用复制
            aPrototype[member] = aDefine[member];
        if(aDefine.Create)//若有构造函数
            var aType = aDefine.Create//类型即为该构造函数
        else//否则为默认构造函数
            aType = function()
            {
                this.base.apply(this, arguments);
            };
        aType.prototype = aPrototype;//设置类（构造函数）的prototype
        aType.Base = aBase;//设置类型关系
        aType.prototype.Type = aType;//为本类对象拓展一个type属性
        return aType;//返回构造函数作为类
    };
//根类object定义
    function object(){}//定义小写的object根类，用于实现最基础的方法等
    object.prototype.isA = function(aType)//判断对象是否属于某类型
    {
        var self = this.Type;
        while(self)
        {
            if(self == aType) return true;
            self = self.Base;
        };
    return false;
};
object.prototype.base = function()//调用基类构造函数
{
    var Caller = object.prototype.base.caller;
    Caller && Caller.Base && Caller.Base.apply(this, arguments);
};
//语法甘露的应用效果：
var Person = Class//默认派生object基本类
({
    Create:function(name, age)
    {
        this.base();
        this.name = name;
        this.age = age;
    },
    SayHello:function()
    {
        alert("Hello,I'm " + this.name + ", " + this.age + " years old.");
    }
});
var Employee = Class(Person,//派生自Person类
{
    Create:function(name, age, salary)
    {
        this.base(name, age);//调用基类的构造函数
        this.salary = salary;
    },
    ShowMeTheMoney:function()
    {
        alert(this.name + " $ " + this.salary);
    }
});

var BillGates = new Person("Bill Gates", 53);
var SteveJobs  = new Employee("Steve Jobs", 44, 1234);
BillGates.SayHello();//Hello,i'm Bill Gates,53 years old
SteveJobs.SayHello();//Hello,i'm Steve Jobs, 44 yearsold
SteveJobs.ShowMeTheMoney();//Steve Jobs $ 1234

var LittleBill = new BillGates.Type("Little Bill", 6);
//用BillGates的类型建LittleBill
LittleBill.SayHello();//Hello,i'm Little Bill, 6 yearsold

alert(BillGates.isA(Person));//true
alert(BillGates.isA(Employee));//false
alert(SteveJobs.isA(Person));//true
~~~

P66：完美甘露模型

~~~javascript
function Class()
//定义语法甘露Class（）
//最后一个参数是JSON表示的类定义
//如果参数数量大于1个，则第一个参数是基类
//第一个和最后一个之间的参数，将来可以表示类实现的接口
//返回值是类，类是一个构造函数
{
    var aDefine = arguments[arguments.length-1];//最后一个参数是类定义
    if(!aDefine) return;
    var aBase = arguments.length > 1 ? arguments[0] : object;//解析基类
    function prototype_(){}//构造prototype的临时函数，用于挂接原型链
    prototype_.prototype = aBase.prototype;//准备传递prototype
    var aPrototype = new prototype_();//建立类要用的prototype
    
    for(var member in aDefine)//复制类定义到当前类的prototype
    	if(member != "Create")//构造函数不用复制
            aPrototype[member] = aDefine[member];
    
    //根据是否继承特殊属性和性能情况，可分别注释掉下列语句
    if(aDefine.toString != Object.prototype.toString)
        aPrototype.toString = aDefine.toString;
    if(aDefine.toLocaleString != Object.prototype.toLocaleString)
        aPrototype.toLocalString = aDefine.toLocalString;
    if(aDefine.valueOf != Object.prototype.valueOf)
        aPrototype.valueOf = aDefine.valueOf;
    
        if(aDefine.Create)//若有构造函数
            var aType = aDefine.Create//类型即为该构造函数
        else//否则为默认构造函数
            aType = function()
            {
                this.base.apply(this, arguments);//调用基类构造函数
            };
        aType.prototype = aPrototype;//设置类（构造函数）的prototype
        aType.Base = aBase;//设置类型关系
        aType.prototype.Type = aType;//为本类对象拓展一个type属性
        return aType;//返回构造函数作为类
    };
//根类object定义
    function object(){}//定义小写的object根类，用于实现最基础的方法等
    object.prototype.isA = function(aType)//判断对象是否属于某类型
    {
        var self = this.Type;
        while(self)
        {
            if(self == aType) return true;
            self = self.Base;
        };
    return false;
};
object.prototype.base = function()//调用基类构造函数
{
    var Base= this.Type.Base;
    if(!Base.Base)//若基类已没有基类
        Base.apply(this, arguments);
    else//若基类还有基类
    {
        this.base = MakeBase(Base);
        Base.apply(this, arguments);
        delete this.base;
};
function MakeBase(Type)
{
    var Base = Type.Base;
    if(!Base.Base) return Base;
    return function()
    {
        this.base = MakeBase(Base);
        Base.apply(this, arguments);
    };
};
};
//语法甘露的应用效果：
var Person = Class//默认派生object基本类
({
    Create:function(name, age)
    {
        this.base();
        this.name = name;
        this.age = age;
    },
    SayHello:function()
    {
        alert("Hello,I'm " + this.name + ", " + this.age + " years old.");
    },
    toString:function()//覆写toString方法
    {
        return this.name;
    }
});
var Employee = Class(Person,//派生自Person类
{
    Create:function(name, age, salary)
    {
        this.base(name, age);//调用基类的构造函数
        this.salary = salary;
    },
    ShowMeTheMoney:function()
    {
        alert(this.name + " $ " + this.salary);
    }
});

var BillGates = new Person("Bill Gates", 53);
var SteveJobs  = new Employee("Steve Jobs", 44, 1234);
alert(BillGates);
BillGates.SayHello();//Hello,i'm Bill Gates,53 years old
SteveJobs.SayHello();//Hello,i'm Steve Jobs, 44 yearsold
SteveJobs.ShowMeTheMoney();//Steve Jobs $ 1234

var LittleBill = new BillGates.Type("Little Bill", 6);
//用BillGates的类型建LittleBill
LittleBill.SayHello();//Hello,i'm Little Bill, 6 yearsold

alert(BillGates.isA(Person));//true
alert(BillGates.isA(Employee));//false
alert(SteveJobs.isA(Person));//true
~~~

甘露模型：基于JavaScript的原型机制，构造了一个面向对象的类机制。



document：文档

DOM（document object model）：文档对象模型

- 为了方便处理HTML和XML文档

- 表现是一份文档，即网页

- 其内部是由一个个可操控的对象构成的

- 是一个树形结构，是一个可编程的模型

- 除了window和document对象之外，其他对象都是可以动态创建的

- 动态创建DOM对象的标准方法是调用document的creatElement方法

  如：document.createElement("div");

  需将其附加到DOM树的某个节点，将div元素附加到document对象的body节点上

append：附加

~~~html
<!DOCTYPE html>
<html xmlns = "http://www.w3.org/1999/xhtml">
<html>
<head>
<meta charset="utf-8"/>
<title>禅棋传说</title>
    <style type="text/css">
        div{
            position: absolute;
            width: 23px;
            height: 23px;
        }
        .B0{background-image: url('C:/Users/ss/Desktop/禅棋传说/B0.gif');}
        .B1{background-image: url('C:/Users/ss/Desktop/禅棋传说/B1.gif');}
        .B2{background-image: url('C:/Users/ss/Desktop/禅棋传说/B2.gif');}
        .B3{background-image: url('C:/Users/ss/Desktop/禅棋传说/B3.gif');}
        .B4{background-image: url('C:/Users/ss/Desktop/禅棋传说/B4.gif');}
        .B5{background-image: url('C:/Users/ss/Desktop/禅棋传说/B5.gif');}
        .B6{background-image: url('C:/Users/ss/Desktop/禅棋传说/B6.gif');}
        .B7{background-image: url('C:/Users/ss/Desktop/禅棋传说/B7.gif');}
        .B8{background-image: url('C:/Users/ss/Desktop/禅棋传说/B8.gif');}
        .BX{background-image: url('C:/Users/ss/Desktop/禅棋传说/BX.gif');}
        .C0{background-image: url('C:/Users/ss/Desktop/禅棋传说/C0.gif');}
        .C1{background-image: url('C:/Users/ss/Desktop/禅棋传说/C1.gif');}
        .D0{background-image: url('C:/Users/ss/Desktop/禅棋传说/D0.gif');}
        .D1{background-image: url('C:/Users/ss/Desktop/禅棋传说/D1.gif');}
    </style>
</head>
<body>
    <script type="text/javascript">
        Array.prototype.indexOf = function(item)
        {
            for(var i = 0; i < this.length; i++)
            {
                if(this[i] == item)
                {
                    return i;
                }
            }
            return -1;
        }
        var Site
        {
            Create:function(x, y)
            {
                var me = document.createElement("div");
                document.body.appendChild(me);
                me.x = x;
                me.y = y;
                me.style.left = x * 23 + "px";
                me.style.top = y * 23 + "px";
                var s = ((x-9)%9?0:(x-9)/9+1+(((y-9)%9?0:(y-9)/9)+1)*3);
                me._backStyle = "B" + ((s==4&&(x/3)%2==1&&(y/3)%2==1)?"X":s);
                me.Fill = this.Fill;
                me.Tight = this.Tight;
                me.kill = this.kill;
                me.onclick = this.Play;
                me.Fill();
                return me;
            },
                Fill:function(dot, going)
            {
                if(dot==undefined)
                {
                    this.className = this._backStyle;
                }else{
                    this.className = (going?"C":"D")+dot;
                }
                this.dot = dot;
            },
                Play:function()
            {
                if(this.dot==undefined)
                {
                    var deads = this.kill(current^1);
                    if(deads.length==1&&this==rob)
                        return;
                    for(var i = 0; i < deads.length; i++)
                    {
                        deads[i].Fill();
                    }
                    if(i==1)
                    {
                        rob = deads[0];
                    }else if(i>0||!this.Tight(current))
                    {
                        rob = nulll;
                    }else
                    {
                        return;
                    }
                    sound.play();
                    var step = Tracks[Tracks.length-1];
                    if(step)
                    {
                        step.site.Fill(step.site.dot);
                    }
                    this.Fill(current, true);
                    Tracks.push(new Step(this, deads));
                    current^=1;
                };
            },
                Tight:function(dot)
            {
                var life = this.dot ==undefined?this:undefined;
                dot = dot == undefined?this.dot:dot;
                if(dot==undefined)
                {
                    return undefined;
                }
                var block = this.dot == undefined?[]:[this];
                var i = this.dot == undefined?0:1;
                var site = this;
                while(true)
                {
                    for(var dx=-1; dx<=1; dx++) for(var  dy=-1; dy<=1; dy++) if(!dx^!dy)
                    {
                        link = GetSite(site.x + site.y+dy);
                         if(link)
                             if(link.dot != undefined)
                             {
                                  if(link.dot==dot&&block.indexOf(link)<0)
                                       block.push(link);  
                             }
                        else if(life)
                            life = link;
                        else if(life != link)
                            return undefined;
                    };
                    if(i>=block.length) break;
                    site = block[i];
                    i++;
                };
                return block;
            },
                kill:function(dot)
            {
                var deads = [];
                for(var dx=-1;dx<=1;dx++) for(var dy=-1;dy<=-1;dy++) if(!dx^!dy)
                    {
                        var site = GetSite(this.x+dx,this.y+dy);
                        if(site&[site.dot==dot])
                            {
                                var block = site.Tigh();
                                if(block) deads=deads.concat(block);
                            };
                    };
                return deads;
            };
        };
        var Board = new Array(19);
        var Tracks = [];
        var current = 0;
        var rob = null;
        for(var x=0;x<19;x++)
        {
            Board[x] = new Array(19);
            for(var y=0;y<19;y++)
            {
                Board[x][y] = Site.Create(x, y);
            }
        };
        if(navigator.userAgent.indexOf('MSIE')>-1)
        {
            var sound  = document.body.appendChild(document.createElement("bgsound"));
            sound.play = function(){this.src = "play.wav"};
        }
        else
        {
            var sound = document.body.appendChild(document.createElement("span"));
            sound.play = function(){this.innerHTML =  "<bgsound src= 'play.wav'>"};
        };
        document.body.oncontextmenu = function()
        {
            var step = Tracks.pop();
            if(step)
            {
                step.site.Fill();
                for(var i=0;i<step.deads.length;i++)
                    step.deads[i].Fill(current);
                step = Tracks[Tracks.length-1];
                if(step) step.site.Fill(current, true)
                current^=1;
            };
            return false;
        };
        function GetSite(x,y)
        {
            if(x>=0 && x<19 && y>=0 && y<19)
                return Board[x][y];
        };
        function Step(site, deads)
        {
            this.site = site;
            this.deads = deads;
        };
        document.onkeypress  = function(event)
        {
            var k = (window.event?window.event.keyCode:event.which)-49;
            if(k<0||k>1) return;
            for(var x=0;x<19;x++) for(var y=0;y<19;y++) Board[x][y].Fill();
            Tracks.length = 0;
            current = 0;
            with(goes[k]) for(var i=0;i<length;i+=3)
                Board[charCodeAt(i+1)-65] [charCodeAt(i)-65].Fill(charCodeAt(i+2)-48);
        };
    </script>
</body>
</html>
~~~

P94 静态绑定，动态绑定

~~~html
静态绑定：
<html>
    <body>
        <div onclick="alert('this is a' + this.tagName)">Click Me!</div>
    </body>
</html>

动态绑定：
<html>
    <body>
        <div id="aDiv">
            Click Me!
        </div>
        <script>
            aDiv.onclick = ClickMe;
            function ClickMe()
            {
                alert('this is a' + this.tagName);
            };
        </script>
    </body>
</html>
~~~

~~~html
<html>
<body>
<span onclick="sound.play()">Click to play sound</span>
<script>
	if(navigator.userAgent.indexOf('MSIE')>-1)
	{
		var sound = document.body.appendChild(document.createElement("bgsound"));
		sound.play = function()
		{
			this.src = "bigbang-if+you.mp3";
		};
	}
	else
	{
		var sound =  document.body.appendChild(document.createElement("span"));
		sound.play = function(mp3)
		{
			this.innerHTML = "<bgsound src='bigbang-if+you.mp3'>";
		};
	};
</script>
</body>
</html>
~~~

~~~html
<html>
    <body>
        <span id="sound"></span>
        <span onclick="playsound()">Click to play sound</span>
        <script>
            function playsound()
            {
                sound.innerHTML = "<bgsound src='bigbang-if+you.mp3'/>";
            };
        </script>
    </body>
</html>
~~~

~~~html
<html>
    <body>
        <bgsound id="sound"/>
        <span onclick="sound.src='bigbang-if+you.mp3'">Click to play sound</span>
    </body>
</html>
~~~

~~~javascript
if(this.dot == undefined)
{
    var deads = this.Kill(current^1);
    if(deads.length == 1 && this == rob) return;
    for(var i=0;i<deads.length;i++)
        deads[i].Fill();
    if(i==1)
        rob = deads[0]
    else if(i>0 || this.Tight(current))
        rob = null;
    else return;
    sound.play();
    var step = Tracks[Tracks.length-1];
    if(step) step.site.Fill(step.site.dot);
    this.Fill(current, true);
    Tracks.push(new Step(this, deads));
    current ^= 1;
};
~~~

~~~html
<!DOCTYPE html>
<html xmlns = "http://www.w3.org/1999/xhtml">
<html>
<head>
<meta charset="utf-8"/>
<title>禅棋传说测试</title>
    <style type="text/css">
        div{
            position: absolute;
            width: 30px;
            height: 30px;
			margin: 0;
			padding: 0;
			border: 0;
        }
        .B0{background-image: url('C:/Users/ss/Desktop/禅棋传说/B0.gif');}
        .B1{background-image: url('C:/Users/ss/Desktop/禅棋传说/B1.gif');}
        .B2{background-image: url('C:/Users/ss/Desktop/禅棋传说/B2.gif');}
        .B3{background-image: url('C:/Users/ss/Desktop/禅棋传说/B3.gif');}
        .B4{background-image: url('C:/Users/ss/Desktop/禅棋传说/B4.gif');}
        .B5{background-image: url('C:/Users/ss/Desktop/禅棋传说/B5.gif');}
        .B6{background-image: url('C:/Users/ss/Desktop/禅棋传说/B6.gif');}
        .B7{background-image: url('C:/Users/ss/Desktop/禅棋传说/B7.gif');}
        .B8{background-image: url('C:/Users/ss/Desktop/禅棋传说/B8.gif');}
        .BX{background-image: url('C:/Users/ss/Desktop/禅棋传说/BX.gif');}
        .C0{background-image: url('C:/Users/ss/Desktop/禅棋传说/C0.gif');}
        .C1{background-image: url('C:/Users/ss/Desktop/禅棋传说/C1.gif');}
        .D0{background-image: url('C:/Users/ss/Desktop/禅棋传说/D0.gif');}
        .D1{background-image: url('C:/Users/ss/Desktop/禅棋传说/D1.gif');}
    </style>
</head>
<body>
    <script type="text/javascript">
		 Array.prototype.indexOf = function(item)
        {
            for(var i = 0; i < this.length; i++)
            {
                if(this[i] == item)
                {
                    return i;
                }
            }
            return -1;
        };
		
        var Site =//定义一个棋位类
        {
            Create:function(x, y)//棋位类的构造函数
            {
                var me = document.createElement("div");//建一个div对象，将其扩展并封装成棋位
                document.body.appendChild(me);//附加到DOM树，实现棋位的呈现
                me.x = x;//记录棋位的X坐标
                me.y = y;//记录棋位的Y坐标
                me.style.left = x * 30 + "px";//设置棋位水平方向的绝对位置
                me.style.top = y * 30 + "px";//设置棋位竖直方向的绝对位置
				
				//计算并准备棋位的背景样式
                var s = ((x-9)%9?0:(x-9)/9)+1+(((y-9)%9?0:(y-9)/9)+1)*3;
                me._backStyle = "B" + ((s==4&&(x/3)%2==1&&(y/3)%2==1)?"X":s);
				
                me.Fill = this.Fill;//关联一个填充棋位的方法
                me.Tight = this.Tight;//关联计算紧气方法
                me.Kill = this.Kill;//关联计算死子方法
                me.Fill();//初始填充空棋子
				
				me.onclick = this.Play;//绑定onclick事件到play方法
                return me;//返回棋位对象，其实是一个封装了的div对象
            },
			
                Fill:function(dot, going)//填充棋子的方法
            {
                if(dot == undefined)
                {
                    this.className = this._backStyle;//如果无子，就设置为背景样式
                }else{
                    this.className = (going?"C":"D")+dot;//有子，就设置为相应的样式
                }
                this.dot = dot;//保存棋子状态，0：黑子，1：白子
            },
			
 Play:function()//行棋方法，由onclick事件触发
            {
                if(this.dot == undefined)
				{
					var deads = this.Kill(current^1);
					if(deads.length == 1 && this == rob) return;
					for(var i=0;i<deads.length;i++)
						deads[i].Fill();
					if(i==1)
						rob = deads[0]
					else if(i>0 || this.Tight(current))
						rob = null;
					else return;
					sound.play();
					var step = Tracks[Tracks.length-1];
					if(step) step.site.Fill(step.site.dot);
					this.Fill(current, true);
					Tracks.push(new Step(this, deads));
					current ^= 1;
				};
            },
                Tight:function(dot)//计算紧气的块
            {
                var life = this.dot == undefined?this:undefined;//当前位无子则算一口气
                dot = dot == undefined?this.dot:dot;
                if(dot==undefined)
                {
                    return undefined;
                }
                var block = this.dot == undefined?[]:[this];
                var i = this.dot == undefined?0:1;
                var site = this;
                while(true)
                {
                    for(var dx=-1; dx<=1; dx++) for(var dy=-1; dy<=1; dy++) if(!dx^!dy)
                    {
                        link = GetSite(site.x + site.y + dy);
                         if(link)//有位
                             if(link.dot != undefined)//有子
                             {
                                  if(link.dot==dot && block.indexOf(link)<0)
                                       block.push(link);  
                             }
                        else if(!life)
                            life = link
                        else if(life != link)
                            return undefined;//如果有两口气以上则无须再算
                    };
                    if(i>=block.length) break;
                    site = block[i];
                    i++;
                };
                return block;
            },
                Kill:function(dot)//计算杀死的子
            {
                var deads = [];
                for(var dx=-1;dx<=1;dx++) for(var dy=-1;dy<=-1;dy++) if(!dx^!dy)
                    {
                        var site = GetSite(this.x+dx, this.y+dy);
                        if(site&(site.dot==dot))
                            {
                                var block = site.Tigh();
                                if(block) deads=deads.concat(block);
                            };
                    };
                return deads;
            }
        };
        var Board = new Array(19);//全局的board数组，表示棋盘
		var Tracks = [];//行棋线索数组，数组元素是Step对象
        var current = 0;//当前要下的子，0表示黑子，1表示白子，相互交替
        var rob = null;
        for(var x=0;x<19;x++)
        {
            Board[x] = new Array(19);
            for(var y = 0; y < 19; y++)
            {
                Board[x][y] = Site.Create(x, y);//按位置创建棋位对象
            }
        };
        var current = 0;
		//播放声音
		var sound =  document.body.appendChild(document.createElement("span"));
		sound.play = function()
		{
			this.innerHTML = "<embed src='bigbang-if+you.mp3'>";
		}
		
		document.body.oncontextmenu = function()//悔棋事件
        {
            var step = Tracks.pop();
            if(step)
            {
                step.site.Fill();
                for(var i=0;i<step.deads.length;i++)
                    step.deads[i].Fill(current);
                step = Tracks[Tracks.length-1];
                if(step) step.site.Fill(current, true)
                current^=1;//反转黑白棋子
            };
            return false;//不弹出菜单
        };
        function GetSite(x,y)//从棋盘取棋位的函数，越界不抛出异常
        {
            if(x>=0 && x<19 && y>=0 && y<19)
                return Board[x][y];
        };
        function Step(site, deads)//棋步类，记录每一步棋的状态
        {
            this.site = site;//记录棋步的位置
            this.deads = deads;//记录被当前棋步杀死的棋子集合
        };
        document.onkeypress  = function(event)
        {
            var k = (window.event?window.event.keyCode:event.which)-49;
            if(k<0 || k>1) return;
            for(var x=0;x<19;x++) for(var y=0;y<19;y++) Board[x][y].Fill();
            Tracks.length = 0;
            current = 0;
            with(goes[k]) for(var i=0;i<length;i+=3)
                Board[charCodeAt(i+1)-65][charCodeAt(i)-65].Fill(charCodeAt(i+2)-48);
        };
    </script>
</body>
</html>
~~~



创建canvas元素：

~~~html
创建canvas元素：(HTMl中)
<canvas id="myCanvas" width="200" height="100"></canvas>
通过JavaScript来绘制
<script type="text/javascript">
	var c=document.getElementById("myCanvas");//使用id来寻找canvas元素
    var cxt=c.getContext("2d");//然后创建context对象
    //getContext("2d")对象是内建的HTML5对象，拥有多种绘制路径，矩形，原型，字符以及图像的方法
    cxt.fillStyle="FF0000";
    cxt.fillRect(0,0,150,75);
</script>
~~~

canvas画布画棋盘：

~~~html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>菜鸡五子棋</title>
        <style type="text/css">
            .chessboard{
                display: block;
                margin: 0 auto;
            }
        </style>
        <script>
            function drawChessBoard(){
                var c = document.getElementById("myCanvas");
                cxt = c.getContext("2d");
                //画出棋盘
                for(var i = 0; i < 15; i++)
                {
                    cxt.lineWidth=2;
                    //画出竖直的线
                    cxt.moveTo(0, i * 30);
                    cxt.lineTo(30 * 14, i * 30);
                    cxt.stroke();
                    //画出横线
                    cxt.moveTo(i * 30, 0);
                    cxt.lineTo(i * 30, 30 * 14);
                    cxt.stroke();
                }
            }
            document.getElementById("myCanvas").onclick = dunction(){
                var coordX = document.getBoundingClientRect().top;
            var coordY = document.getBoundingClientRect().left();
            alert(coordX, coordY);
            }
        </script>
    </head>
    <body onload="drawChessBoard()">
        <canvas id="myCanvas"
                width="500px"
                height="500px"
                class="chessboard"
    </body>
</html>
~~~

~~~html
window.onload = function()
			{
				cav1 = document.getElementById("myCanvas");
				cav1.onclick = function(e)
				{
					var coordX = this.getBoundingClientRect().left+document.documentElement.scrollLeft;
					var coordY = this.getBoundingClientRect().top+document.documentElement.scrollTop;

					alert(coordX, coordY);
				}
			}
~~~

~~~~html
function getCanvasPos(canvas,e)  
	{//获取鼠标在canvas上的坐标  
	    var rect = canvas.getBoundingClientRect(); 
	    var leftB = parseInt(getStyles(canvas).borderLeftWidth);//获取的是样式，需要转换为数值
	    var topB = parseInt(getStyles(canvas).borderTopWidth);
	    return {   
	     x: (e.clientX - rect.left) - leftB,  
	     y: (e.clientY - rect.top) - topB 
	   };  
	} 
--------------------- 
作者：dxylilac 
来源：CSDN 
原文：https://blog.csdn.net/u010484625/article/details/46047695 
版权声明：本文为博主原创文章，转载请附上博文链接！
~~~~

~~~javascript
 function drawChessBoard()
{
    var c = document.getElementById("myCanvas");
    var cxt = c.getContext("2d");
	var x=10;
	var y= 10;
	cxt.moveTo(x, y);
   	//画出棋盘
    for(var i = 0; i < 15; i++)
    {
        cxt.lineWidth=2;
        //画出竖直的线
        cxt.moveTo(x, i * 30 + x);
        cxt.lineTo(30 * 14 + y, i * 30 + y);
        cxt.stroke();
        //画出横线
        cxt.moveTo(y + i * 30, y);
        cxt.lineTo(y + i * 30, y + 30 * 14);
        cxt.stroke();
    }
    window.onload = function()
    {
        cav1 = document.getElementById("myCanvas");
		cav1.onclick = function(e)
        {
            alert(e.screenX);//获取鼠标点击地的x坐标
			alert(e.screenY);//获取鼠标点击地的y坐标
        }
    }
}
~~~



# JAVASCRIPT.INFO#

## 简介、JS基础知识##

javascript的最初目的是“赋予网页生命”，这种编程语言我们称为脚本。它可以写在HTML中，在页面加载的时候会自动执行

脚本作为纯文本存在和执行，不需要特殊的准备或编译即可运行。

JS不仅在浏览器内执行，也可以在任意搭载了js引擎的环境中执行。

浏览器中嵌入JS引擎，有时也称作JS虚拟机

如：

- V8 - Chorme和Opera的JS引擎
- SpiderMonkey - Firefox的JS引擎
- "Trident", "Chakra" - IE
- "ChakraCore" - Microsoft Edge
- "Nitro"(硝基)， "SquirrelFish" - Safari

==注释热键==

==单行：ctrl+/==

==多行：ctrl+shift+/==

==注释不支持嵌套==

使用ES6规范："use strict", 'use strict'放在脚本顶部

声明变量：let，var

变量命名：

1. 必须包含字母，数字，$，_
2. 首字符必须非数字
3. 使用camelCase
4. 大小写敏感

声明常量：const 大写字母+_

JS中变量的类型可以任意改变：允许这种操作的编程语言称为"动态类型"(dynamically typed)的编程语言

1. number

   - `Infinity`：无穷大

     ~~~javascript
     alert( 1 / 0);// Infinity
     alert( Infinity );// Infinity
     ~~~

   - `NaN`：代表一个计算错误，它是对一个不对的或未定义的数字操作所得到的结果

     ~~~javascript
     alert( "not a number" / 2);//NaN，这样的除法是错误的
     NaN是粘性的，任何对NaN的进一步操作都会给NaN
     alert( "not a number" / 2 + 5);//NaN
     ~~~

     所以在数学表达式中有一个NaN，会被传播到最终结果

     ~~~javascript
     let apple = "2";
     let banana = "3";
     //在二元运算符加号起作用之前，所有的值都转为数字
     alert( +apple+ +orange);//5 
     ~~~

     

2. string

   - 单引号：简单引用无差别

   - 双引号：简单引用无差别

   - 反引号：**功能扩展**，允许通过`${...}`，将变量和表达式嵌入到字符串中

     ~~~javascript
     let name = "John";
     // embed a variable
     alert( `Hello, ${name}!` );//Hello,John!
     // embed an expression
     alert( `the result is ${1 + 2}` );//the result is 3
     ~~~

   `alert( 2 + 2 + '1' );//从左到右，41`

3. boolean

4. "null"值

   null不属于任何一种类型，是一个独立的类型

   相比较其他语言，JS中的null不是"一个对不存在对象的引用"或者"null指针"

   仅仅是一个含义为 "无" 、"空" 或 "值未知" 的特殊值

   `typeof null //返回"object"，是语言中的一个错误`

5. "undefined"值

   含义：未被赋值

   如果变量被声明，而未被赋值，那么值就是“undefined”

6. object

   对象

7. symbol

   学完对象后学习symbol

强制类型转换：

ToString：String(value)

ToNumber：Number(value)

ToBoolean：Boolean(value)

运算符：

幂运算符：a ** b，a与自己相乘b次

​	a ** 3：a的三次方

​	a ** (1/3)：a的1/3次方，即a开3次方

==比较：==

​	null === undefined; //flase

​	==null == undefined; //true==

null VS 0：

~~~javascript
alert( null > 0 ); //false
alert( null == 0 ); //false
alert( null >= 0 ); //true
~~~

undefined不应该参与任何值的比较：

~~~javascript
alert( undefined > 0 ); //false
alert( undefined < 0 ); //false
alert( undefined == 0 ); //false

let name = prompt("What is your name?", "");
alert(name);
~~~

交互：:computer:

1. alert：显示信息
2. prompt：显示信息要求用户输入文本。点击确定返回文本，点击取消或按下`Esc`键返回`null`
3. confirm：显示信息等待用户点击确定或取消。点击确定返回true，点击取消或Esc返回false

这些方法都是模态的，它们暂停脚本执行，并且不允许用户与该页面的其余部分交互，知道消息被解除。有两个限制：

- 模态窗口的确切位置由浏览器决定，通常位于页面中心
- 窗口的确切外观也取决于浏览器，不能修改

函数命名：:happy:

- get... ：返回值, showMessage
- calc... ：计算, calcSum
- create...：创建, createForm
- check...：检查并返回boolean值, checkPermission

函数总结：

- 函数是值，可以在任何地方分配，复制或声明
- 如果函数在主代码流中为单独的语句，就称为函数声明，在代码块中随处调用
- 如果函数作为表达式的一部分创建，就称为函数表达式，在执行流程到时创建

函数声明：

~~~javascript
//Function Declaration
function sum(a, b){
    return a + b;
}
~~~



函数表达式：

~~~javascript
//Function Expression
let sum = function(a, b){
    return a + b;
};
//需要；
~~~



箭头函数：

~~~javascript
let func = (arg1, arg2, ...argN) => expression
//等同于
let func = function(arg1, arg2, ...argN){
    return expression;
}
~~~

箭头函数非常适合单行调用：

- 没有大括号：`(...args) => expression`，右侧是一个表达式，该函数对其进行运行并返回结果
- 有大括号：`(...args) => { body }`，括号允许我们在函数中写入多个语句，但是我们需要显示的return来返回一些东西



JavaScript特性

- 自动分号插入

- 严格模式，"use strict"; 'use strict';

- 声明变量：let，const，var

- 7种数据类型：number，string，boolean，null，undefined，object，symbol

- `typeof null == "object" //error in the language`

  `typeof function(){} == "function" //函数特殊`

- 交互：alert，prompt，confirm

- 创建函数的三种方法：

  1. 函数声明
  2. 函数表达式
  3. 箭头函数



## 代码质量##

代码风格示例：

~~~javascript
functin pow(x, n) {
    let result = 1;
    
    for (let i = 0; i < n; i++) {
        result *= x;
    }
    
    return result;
}

let x = prompt("x?", "");
let n = prompt("n?", "");

if(n < 0) {
    alert(`Power ${n} is not supported,
	please enter an integer number greater 		than zero`);
} else {
    alert( pow(x, n) );
}
~~~



~~~javascript
let codes = {
    "+49": "Germany",
    "+41": "Switzerland",
    "+44": "Great Britain",
    "+1": "USA",
};

for( let code in codes) {
    alert( +code );
}

let user = { name: "John" };
Object.assign(user, { name: "Peter", isAdmin: true });
for( let key in user ) {
    alert( key );
    alert( user[key] );
}

let user = { name: "John" };
let p1 = { canView: true };
let p2 = { canEdit: false };

Object.assign(user, p1, p2);
for( let m in user ) {
    alert( m );
    alert( user[m] );
}

let user = {
    name: "John",
    age: 30,
};

let clone = Object.assign({}, user);

for( let n in clone ) {
    alert( n );
    alert( clone[n] );
}
~~~







































































