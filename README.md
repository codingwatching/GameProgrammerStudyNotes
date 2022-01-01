# Game Programming Patterns 游戏编程模式
Unity实现《游戏编程模式》

# 重访设计模式
## 命令模式

### 定义

将一个请求封装为**一个对象**，从而使你可用不同的请求对客户进行参数化； 对请求排队或记录请求日志，以及支持可**撤销**的操作。

### 场景

1. 通过操作来参数化对象
2. 将操作放入队列中、操作的执行或者远程执行操作
3. 实现操作回滚功能

### 案例

**[配置输入](./Assets/DesignPatternsRevisited/CommandPattern)**：用户通过键盘、手柄以及界面按钮实现移动等操作，并可以实现撤销操作；

### 实现方式

1. 声明仅有一个执行方法的[命令接口](./Assets/DesignPatternsRevisited/CommandPattern/Command.cs)。
2. 抽取请求并使之成为实现命令接口的[具体命令类](./Assets/DesignPatternsRevisited/CommandPattern/MoveCommand.cs)。 每个类都必须有一组成员变量来保存请求参数和对于实际接收者对象的引用。 所有这些变量的数值都必须通过命令构造函数进行初始化。
3. 找到担任[*发送者*职责的类](./Assets/DesignPatternsRevisited/CommandPattern/InputHandler.cs)。 在这些类中添加保存命令的成员变量。 发送者只能通过命令接口与其命令进行交互。 发送者自身通常并不创建命令对象， 而是通过客户端代码获取。
4. 修改发送者使其执行命令， 而非直接将请求发送给接收者。
5. 客户端必须按照以下顺序来初始化对象：
   - 创建接收者。
   - 创建命令， 如有需要可将其关联至接收者。
   - 创建发送者并将其与特定命令关联。

### 优缺点

**优点**

-  *单一职责原则*。 你可以解耦[触发](./Assets/DesignPatternsRevisited/CommandPattern/MoveCommand.cs)和[执行](./Assets/DesignPatternsRevisited/CommandPattern/MoveCommandReceiver.cs)操作的类。

-  *开闭原则*。 你可以在不修改已有客户端代码的情况下在程序中创建新的命令。

-  你可以实现撤销和恢复功能。

-  你可以实现操作的延迟执行。

-  你可以将一组简单命令组合成一个复杂命令。

- 运行时切换连接至发送者的命令对象， 以此改变发送者的行为。

  ```c#
  MoveCommand moveCommand = new MoveCommand(moveCommandReciever, direction, moveDistance, objectToMove);
  JumpCommand jumpCommand = new JumpCommand(jumpCommandReciever, direction, jumpDistance, objectToMove);
  Command command;
  if (isMove){
      command = moveCommand；
  } else {
      command = jumpCommand；
  }
  command.Execute();
  ```

**缺点**

* 代码可能会变得更加复杂， 因为你在发送者和接收者之间增加了一个全新的层次。*(全新层次指本来只需要直接调用方法来实现操作就可以，现在需要通过命令类来实现操作)*

### 与其他设计模式的关系

- 责任链模式(Gang of Four Patterns)、 命令模式、 中介者模式(Gang of Four Patterns)和观察者模式用于处理请求发送者和接收者之间的不同连接方式：

  - *责任链*按照顺序将请求动态传递给**一系列**的潜在接收者， 直至其中一名接收者对请求进行处理。
  - *命令*在发送者和请求者之间建立**单向连接**。
  - *中介者*清除了发送者和请求者之间的直接连接， 强制它们通过一个中介对象进行**间接沟通(双向)**。
  - *观察者*允许接收者动态地**订阅或取消**接收请求。

- 责任链的管理者可使用命令模式实现。 在这种情况下， 你可以对由请求代表的同一个上下文对象执行许多不同的操作。

  还有另外一种实现方式， 那就是请求自身就是一个*命令*对象。 在这种情况下， 你可以对由一系列不同上下文连接而成的链执行相同的操作。

- 你可以同时使用命令和备忘录模式(Gang of Four Patterns)来实现 “撤销”。 在这种情况下， 命令用于对目标对象执行各种不同的操作， 备忘录用来保存一条命令执行前该对象的状态。

- 命令和策略模式看上去很像， 因为两者都能通过某些行为来参数化对象。 但是， 它们的意图有非常大的不同。

  - 你可以使用*命令*来将任何操作转换为对象。 操作的参数将成为对象的成员变量。 你可以通过转换来延迟操作的执行、 将操作放入队列、 保存历史命令或者向远程服务发送命令等。
  - 另一方面， *策略*通常可用于描述完成某件事的不同方式， 让你能够在同一个上下文类中切换算法。

- 原型模式可用于保存命令的历史记录。

- 你可以将访问者模式视为命令模式的加强版本， 其对象可对不同类的多种对象执行操作。

## 享元模式
## 观察者模式
## 原型模式
## 单例模式
## 状态模式
# 序列模式
## 双缓冲模式
## 游戏循环
## 更新方法
# 行为模式
## 字节码
## 子类沙箱
## 类型对象
# 解耦模式
## 组件模式
## 事件队列
## 服务定位器
# 优化模式
## 数据局部性
## 脏标识模式
## 对象池模式
## 空间分区

