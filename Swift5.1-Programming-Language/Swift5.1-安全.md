# Swift5.1 安全
本文围绕着“安全”的角度来解释Swift语言先进于Oc的一些特性.

## 类型安全
#### 强类型
1. 显示类型声明

        var num: Int = 0 //👌
        var str: String = "abc" //👌
        var str: String = 123 //❌编译器error，类型不匹配

2. 隐式类型声明

        var num = 0 //根据字面值推出num为Int类型
        var str = "123" //根据字面值推出str为String类型

#### 对象初始化

* 所有对象(除Option对象)声明后必须初始化

        var num: Int //👌
        //num = 3 
        print(num) //❌编译器error,使用前需要初始化
        let str: String //👌
        print(str) //❌编译器error,使用前需要初始化

* Option对象可以不需要初始化

        var str: String?
        print(num) //👌


## 语义安全
#### Option 对象
OC为什么没有Option对象.

由于OC对象为nil时，(如果不加入异常处理)并不会导致app crash. 

但风险是

1. 这个对象为nil, 可能会导致功能失效.
2. 没有异常防御性代码，OC功能失效不能回溯到问题发生点.
3. 编写OC代码，语法上不能体现出能够出现风险的code片段.

什么是Optional对象

任何Swift对象都可以称为Optional对象, 从实现角度来说，它是内部对象的一个wrapper.

        var num: Optional<Int> //Optional对象wrapper Int
        var str: String? //Optional对象wrapper String

如果开发人员声明Optional对象，在语义上说明, 该对象有可能为nil.

如果unwrapper一个nil的Optional对象为内部对象，直接导致crash.

        var str: String?
        var newStr: String = str!//❌Fatal error: Unexpectedly found nil while unwrapping an Optional value:

## 内存安全

## 访问安全

## 异常安全