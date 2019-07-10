# 控制流

Swift 提供了多种流程控制结构，包括可以多次执行任务的 while 循环，基于特定条件选择执行不同代码分支的 if、guard 和 switch 语句，还有控制流程跳转到其他代码位置的 break 和 continue 语句。

Swift 还提供了 for-in 循环，用来更简单地遍历数组（Array），字典（Dictionary），区间（Range），字符串（String）和其他序列类型。

Swift 的 switch 语句比许多类 C 语言要更加强大。case 还可以匹配很多不同的模式，包括范围匹配，元组（tuple）和特定类型匹配。switch 语句的 case 中匹配的值可以声明为临时常量或变量，在 case 作用域内使用，也可以配合 where 来描述更复杂的匹配条件

## For-In 循环

你可以使用 for-in 循环来遍历一个集合中的所有元素，例如数组中的元素、范围内的数字或者字符串中的字符。

以下例子使用 for-in 遍历一个数组所有元素：

    let names = ["Anna", "Alex", "Brian", "Jack"]
    for name in names {
        print("Hello, \(name)!")
    }
    // Hello, Anna!
    // Hello, Alex!
    // Hello, Brian!
    // Hello, Jack!

你也可以通过遍历一个字典来访问它的键值对。遍历字典时，字典的每项元素会以 (key, value) 元组的形式返回，你可以在 for-in 循环中使用显式的常量名称来解读 (key, value) 元组。下面的例子中，字典的键声明会为 animalName 常量，字典的值会声明为 legCount 常量：

    let numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
    for (animalName, legCount) in numberOfLegs {
        print("\(animalName)s have \(legCount) legs")
    }
    // cats have 4 legs
    // ants have 6 legs
    // spiders have 8 legs

字典的内容理论上是无序的，遍历元素时的顺序是无法确定的。将元素插入字典的顺序并不会决定它们被遍历的顺序。关于数组和字典的细节，参见 集合类型。

for-in 循环还可以使用数字范围。下面的例子用来输出乘法表的一部分内容：

    for index in 1...5 {
        print("\(index) times 5 is \(index * 5)")
    }
    // 1 times 5 is 5
    // 2 times 5 is 10
    // 3 times 5 is 15
    // 4 times 5 is 20
    // 5 times 5 is 25

例子中用来进行遍历的元素是使用闭区间操作符（...）表示的从 1 到 5 的数字区间。index 被赋值为闭区间中的第一个数字（1），然后循环中的语句被执行一次。在本例中，这个循环只包含一个语句，用来输出当前 index 值所对应的乘 5 乘法表的结果。该语句执行后，index 的值被更新为闭区间中的第二个数字（2），之后 print(_:separator:terminator:) 函数会再执行一次。整个过程会进行到闭区间结尾为止。

上面的例子中，index 是一个每次循环遍历开始时被自动赋值的常量。这种情况下，index 在使用前不需要声明，只需要将它包含在循环的声明中，就可以对其进行隐式声明，而无需使用 let 关键字声明。

如果你不需要区间序列内每一项的值，你可以使用下划线（_）替代变量名来忽略这个值：

    let base = 3
    let power = 10
    var answer = 1
    for _ in 1...power {
        answer *= base
    }
    print("\(base) to the power of \(power) is \(answer)")
    // 输出“3 to the power of 10 is 59049”

这个例子计算 base 这个数的 power 次幂（本例中，是 3 的 10 次幂），从 1（3 的 0 次幂）开始做 3 的乘法， 进行 10 次，使用 1 到 10 的闭区间循环。这个计算并不需要知道每一次循环中计数器具体的值，只需要执行了正确的循环次数即可。下划线符号 _ （替代循环中的变量）能够忽略当前值，并且不提供循环遍历时对值的访问。

在某些情况下，你可能不想使用包括两个端点的闭区间。想象一下，你在一个手表上绘制分钟的刻度线。总共 60 个刻度，从 0 分开始。使用半开区间运算符（..<）来表示一个左闭右开的区间。有关区间的更多信息，请参阅 区间运算符。

    let minutes = 60
    for tickMark in 0..<minutes {
        // 每一分钟都渲染一个刻度线（60次）
    }


一些用户可能在其 UI 中可能需要较少的刻度。他们可以每 5 分钟作为一个刻度。使用 stride(from:to:by:) 函数跳过不需要的标记。

    let minuteInterval = 5
    for tickMark in stride(from: 0, to: minutes, by: minuteInterval) {
        // 每5分钟渲染一个刻度线（0, 5, 10, 15 ... 45, 50, 55）
    }

可以在闭区间使用 stride(from:through:by:) 起到同样作用：

    let hours = 12
    let hourInterval = 3
    for tickMark in stride(from: 3, through: hours, by: hourInterval) {
        // 每3小时渲染一个刻度线（3, 6, 9, 12）
    }

## While 循环

while 循环会一直运行一段语句直到条件变成 false。这类循环适合使用在第一次迭代前，迭代次数未知的情况下。Swift 提供两种 while 循环形式：

* while 循环，每次在循环开始时计算条件是否符合；
* repeat-while 循环，每次在循环结束时计算条件是否符合。


## 检测 API 可用性