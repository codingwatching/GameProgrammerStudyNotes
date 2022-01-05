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

1. **[配置输入](./Assets/DesignPatternsRevisited/CommandPattern)**：用户通过键盘、手柄以及界面按钮实现移动等操作，并可以实现撤销操作；
2. GUI中的点击等UI操作，即**交互逻辑**应该可以通过命令模式来实现具体的业务逻辑，从而进行逻辑解耦；数值更新的**表现逻辑**，可以通过**观察者模式**进行逻辑解耦*(事件的方式，进行通知)*；

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

- 责任链模式***(GOF)*、 命令模式、 中介者模式** *(GOF)*和观察者模式用于处理请求发送者和接收者之间的不同连接方式：

  - *责任链*按照顺序将请求动态传递给**一系列**的潜在接收者， 直至其中一名接收者对请求进行处理。
  - *命令*在发送者和请求者之间建立**单向连接**。
  - *中介者*清除了发送者和请求者之间的直接连接， 强制它们通过一个中介对象进行**间接沟通(双向)**。
  - *观察者*允许接收者动态地**订阅或取消**接收请求。

- 责任链的管理者可使用命令模式实现。 在这种情况下， 你可以对由请求代表的同一个上下文对象执行许多不同的操作。

  还有另外一种实现方式， 那就是请求自身就是一个*命令*对象。 在这种情况下， 你可以对由一系列不同上下文连接而成的链执行相同的操作。

- 你可以同时使用**命令**和**备忘录模式** *(GOF)*来实现 “撤销”。 在这种情况下， 命令用于对目标对象执行各种不同的操作， 备忘录用来保存一条命令执行前该对象的状态。

- 命令和策略模式看上去很像， 因为两者都能通过某些行为来参数化对象。 但是， 它们的意图有非常大的不同。

  - 你可以使用*命令*来将任何操作转换为对象。 操作的参数将成为对象的成员变量。 你可以通过转换来延迟操作的执行、 将操作放入队列、 保存历史命令或者向远程服务发送命令等。
  - 另一方面， *策略*通常可用于描述完成某件事的不同方式， 让你能够在同一个上下文类中切换算法。

- 原型模式可用于保存命令的历史记录。

- 你可以将访问者模式视为命令模式的加强版本， 其对象可对不同类的多种对象执行操作。

## 享元模式

### 定义

摒弃了在每个对象中保存所有数据的方式， 通过**共享**多个对象所共有的相同状态， 让你能在有限的内存容量中**载入更多对象**。

### 场景

1. 仅在程序必须支持**大量对象**且没有足够的内存容量时

### 案例

**[字符对象](./Assets/DesignPatternsRevisited/FlyweightPattern)**：A-Z每个字符作为一个对象，所有的A，有着相同的属性(内在属性)，例如：width、height等；也有着不同的属性(外在属性)，例如：pointSize

### 实现方式

1. 将需要改写为享元的类成员变量拆分为两个部分：
   - 内在状态： 包含不变的、 可在许多对象中重复使用的数据的成员变量。
   - 外在状态： 包含每个对象各自不同的情景数据的成员变量
2. 保留类中表示内在状态的成员变量， 并将其属性设置为不可修改。 这些变量仅可在构造函数中获得初始数值。
3. 找到所有使用外在状态成员变量的方法， 为在方法中所用的每个成员变量新建一个参数， 并使用该参数代替成员变量。
4. 你可以有选择地创建工厂类来管理享元缓存池， 它负责在新建享元时检查已有的享元。 如果选择使用工厂， 客户端就只能通过工厂来请求享元， 它们需要将享元的内在状态作为参数传递给工厂。
5. 客户端必须存储和计算外在状态 （情景） 的数值， 因为只有这样才能调用享元对象的方法。 为了使用方便， 外在状态和引用享元的成员变量可以移动到单独的情景类中。

### 优缺点

**优点**

-  如果程序中有很多相似对象， 那么你将可以节省大量内存。


**缺点**

*  你可能需要牺牲执行速度来换取内存， 因为他人每次调用享元方法时都需要重新计算部分情景数据。
*  代码会变得更加复杂。 团队中的新成员总是会问：  “为什么要像这样拆分一个实体的状态？”。

### 与其他设计模式的关系

- 你可以使用**享元模式**实现**组合模式***(GOF)*树的共享叶节点以节省内存。

- 享元展示了如何生成大量的小型对象， **外观模式*****(GOF)*则展示了如何用一个对象来代表整个子系统。
- 如果你能将对象的所有共享状态简化为一个享元对象， 那么**享元**就和**单例模式**类似了。 但这两个模式有两个根本性的不同。
  1. 只会有一个单例实体， 但是*享元*类可以有多个实体， 各实体的内在状态也可以不同。
  2. *单例*对象可以是可变的。 享元对象是不可变的。

## 观察者模式

### 定义

允许你定义一种订阅机制， 可在对象事件发生时通知多个 “观察” 该对象的其他对象。

### 场景

1. **当一个对象状态的改变需要改变其他对象****，** **或实际对象是事先未知的或动态变化的时**
2.  **当应用中的一些对象必须观察其他对象时****，** **可使用该模式****。** **但仅能在有限时间内或特定情况下使用**

### 案例

1. [消息系统](./Assets/DesignPatternsRevisited/ObserverPattern)
2. 数据绑定

### 实现方式

1. 仔细检查你的业务逻辑， 试着将其拆分为两个部分： 独立于其他代码的核心功能将作为发布者； 其他代码则将转化为一组订阅类。

2. 声明订阅者接口。 该接口至少应声明一个 `update`方法。

3. 声明发布者接口并定义一些接口来在列表中添加和删除订阅对象。 记住发布者必须仅通过订阅者接口与它们进行交互。

4. 确定存放实际订阅列表的位置并实现订阅方法。 通常所有类型的发布者代码看上去都一样， 因此将列表放置在直接扩展自发布者接口的抽象类中是显而易见的。 具体发布者会扩展该类从而继承所有的订阅行为。

   但是， 如果你需要在现有的类层次结构中应用该模式， 则可以考虑使用组合的方式： 将订阅逻辑放入一个独立的对象， 然后让所有实际订阅者使用该对象。

5. 创建具体发布者类。 每次发布者发生了重要事件时都必须通知所有的订阅者。

6. 在具体订阅者类中实现通知更新的方法。 绝大部分订阅者需要一些与事件相关的上下文数据。 这些数据可作为通知方法的参数来传递。

   但还有另一种选择。 订阅者接收到通知后直接从通知中获取所有数据。 在这种情况下， 发布者必须通过更新方法将自身传递出去。 另一种不太灵活的方式是通过构造函数将发布者与订阅者永久性地连接起来。

7. 客户端必须生成所需的全部订阅者， 并在相应的发布者处完成注册工作。

### 优缺点

**优点**

-  *开闭原则*。 你无需修改发布者代码就能引入新的订阅者类 （如果是发布者接口则可轻松引入发布者类）。
-  你可以在运行时建立对象之间的联系。

**缺点**

-  订阅者的通知顺序是随机的。

### 与其他设计模式的关系

- **责任链模式** * *(GOF)*、 **命令模式**、 **中介者模式**(GOF)*和观察者模式用于处理请求发送者和接收者之间的不同连接方式：

  - *责任链*按照顺序将请求动态传递给一系列的潜在接收者， 直至其中一名接收者对请求进行处理。
  - *命令*在发送者和请求者之间建立单向连接。
  - *中介者*清除了发送者和请求者之间的直接连接， 强制它们通过一个中介对象进行间接沟通。
  - *观察者*允许接收者动态地订阅或取消接收请求。

- **中介者** *(GOF)*和**观察者**之间的区别往往很难记住。 在大部分情况下， 你可以使用其中一种模式， 而有时可以同时使用。 让我们来看看如何做到这一点。

  *中介者*的主要目标是消除一系列系统组件之间的相互依赖。 这些组件将依赖于同一个中介者对象。 *观察者*的目标是在对象之间建立动态的单向连接， 使得部分对象可作为其他对象的附属发挥作用。

  有一种流行的中介者模式实现方式依赖于*观察者*。 中介者对象担当发布者的角色， 其他组件则作为订阅者， 可以订阅中介者的事件或取消订阅。 当*中介者*以这种方式实现时， 它可能看上去与*观察者*非常相似。

  当你感到疑惑时， 记住可以采用其他方式来实现中介者。 例如， 你可永久性地将所有组件链接到同一个中介者对象。 这种实现方式和*观察者*并不相同， 但这仍是一种中介者模式。

  假设有一个程序， 其所有的组件都变成了发布者， 它们之间可以相互建立动态连接。 这样程序中就没有中心化的中介者对象， 而只有一些分布式的观察者。

## 原型模式

### 定义

使用特定原型实例来创建特定种类的对象，并且通过**拷贝**原型来创建新的对象

### 场景

1. 你需要复制一些对象，同时又希望代码独立于这些对象所属的具体类
2. 子类的区别仅在于其对象的初始化方式，那么你可以使用该模式来减少子类的数量。别人创建这些子类的目的可能是为了创建特定类型的对象。

### 案例

[克隆对象](./Assets/DesignPatternsRevisited/PrototypePattern)

### 实现方式

1. 创建原型接口， 并在其中声明 `克隆`方法。 如果你已有类层次结构， 则只需在其所有类中添加该方法即可。

2. 原型类必须另行定义一个以该类对象为参数的构造函数。 构造函数必须复制参数对象中的所有成员变量值到新建实体中。 如果你需要修改子类， 则必须调用父类构造函数， 让父类复制其私有成员变量值。

   如果编程语言不支持方法重载， 那么你可能需要定义一个特殊方法来复制对象数据。 在构造函数中进行此类处理比较方便， 因为它在调用 `new`运算符后会马上返回结果对象。

3. 克隆方法通常只有一行代码： 使用 `new`运算符调用原型版本的构造函数。 注意， 每个类都必须显式重写克隆方法并使用自身类名调用 `new`运算符。 否则， 克隆方法可能会生成父类的对象。

4. 你还可以创建一个中心化原型注册表， 用于存储常用原型。

   你可以新建一个工厂类来实现注册表， 或者在原型基类中添加一个获取原型的静态方法。 该方法必须能够根据客户端代码设定的条件进行搜索。 搜索条件可以是简单的字符串， 或者是一组复杂的搜索参数。 找到合适的原型后， 注册表应对原型进行克隆， 并将复制生成的对象返回给客户端。

   最后还要将对子类构造函数的直接调用替换为对原型注册表工厂方法的调用。

### 优缺点

**优点**

- 你可以克隆对象， 而无需与它们所属的具体类相耦合。
-  你可以克隆预生成原型， 避免反复运行初始化代码。
-  你可以更方便地生成复杂对象。
-  你可以用继承以外的方式来处理复杂对象的不同配置。

**缺点**

* 克隆包含循环引用的复杂对象可能会非常麻烦。

### 与其他设计模式的关系

- 在许多设计工作的初期都会使用**工厂方法模式** *(GOF)*（较为简单， 而且可以更方便地通过子类进行定制）， 随后演化为使用**抽象工厂模式** *(GOF)*、 **原型模式**或**生成器模式** *(GOF)* （更灵活但更加复杂）。
- **抽象工厂模式** *(GOF)*通常基于一组**工厂方法** *(GOF)*， 但你也可以使用**原型模式**来生成这些类的方法。
- [原型](https://refactoringguru.cn/design-patterns/prototype)可用于保存[命令模式](https://refactoringguru.cn/design-patterns/command)的历史记录。
- 大量使用**组合模式** *(GOF)*和**装饰模式** *(GOF)*的设计通常可从对于**原型**的使用中获益。 你可以通过该模式来复制复杂结构， 而非从零开始重新构造。
- **原型**并不基于继承， 因此没有继承的缺点。 另一方面， *原型*需要对被复制对象进行复杂的初始化。 **工厂方法** *(GOF)*基于继承， 但是它不需要初始化步骤。
- 有时候**原型**可以作为**备忘录模式** *(GOF)*的一个简化版本， 其条件是你需要在历史记录中存储的对象的状态比较简单， 不需要链接其他外部资源， 或者链接可以方便地重建。
- **抽象工厂** *(GOF)*、 **生成器** *(GOF)*和**原型**都可以用**单例模式**来实现。

## 单例模式

### 定义

确保一个类只有一个实例，并为其提供一个全局访问入口

### 场景

1. 程序中的某个类对于所有客户端只有一个可用的实例
2. 你需要更加严格地控制全局变量

### 案例

[单例](./Assets/DesignPatternsRevisited/SingletonPattern)

### 实现方式

1. 在类中添加一个私有静态成员变量用于保存单例实例。
2. 声明一个公有静态构建方法用于获取单例实例。
3. 在静态方法中实现"延迟初始化"。 该方法会在首次被调用时创建一个新对象， 并将其存储在静态成员变量中。 此后该方法每次被调用时都返回该实例。
4. 将类的构造函数设为私有。 类的静态方法仍能调用构造函数， 但是其他对象不能调用。
5. 检查客户端代码， 将对单例的构造函数的调用替换为对其静态构建方法的调用。

### 优缺点

**优点**

-  你可以保证一个类只有一个实例。
-  你获得了一个指向该实例的全局访问节点。
-  仅在首次请求单例对象时对其进行初始化。

**缺点**

-  违反了_单一职责原则_。 该模式同时解决了两个问题*(在一个方法中进行了创建类和提供类对象的操作)*。
-  单例模式可能掩盖不良设计， 比如程序各组件之间相互了解过多等。
-  该模式在多线程环境下需要进行特殊处理， 避免多个线程多次创建单例对象。
-  单例的客户端代码单元测试可能会比较困难， 因为许多测试框架以基于继承的方式创建模拟对象。 由于单例类的构造函数是私有的， 而且绝大部分语言无法重写静态方法， 所以你需要想出仔细考虑模拟单例的方法。 要么干脆不编写测试代码， 或者不使用单例模式。

### 与其他设计模式的关系

- **外观模式** *(GOF)*类通常可以转换为单例模式类， 因为在大部分情况下一个**外观对象** *(GOF)*就足够了。
- 如果你能将对象的所有共享状态简化为一个享元对象， 那么**享元模式**就和单例类似了。 但这两个模式有两个根本性的不同。
  1. 只会有一个单例实体， 但是*享元*类可以有多个实体， 各实体的内在状态也可以不同。
  2. *单例*对象可以是可变的。 享元对象是不可变的。
- **抽象工厂模式** *(GOF)*、 **生成器模式** *(GOF)*和**原型模式**都可以用单例来实现。

## 状态模式

### 定义

能在一个对象的内部状态变化时改变其行为， 使其看上去就像改变了自身所属的类一样。

### 场景

1. 对象需要根据自身当前状态进行不同行为， 同时状态的数量非常多且与状态相关的代码会频繁变更
2. 某个类需要根据成员变量的当前值改变自身行为，从而需要使用大量的条件语句
3. 当相似状态和基于条件的状态机转换中存在许多重复代码时

### 案例

[有限状态机](./Assets/DesignPatternsRevisited/StatePattern)

### 实现方式

1. 确定哪些类是上下文。 它可能是包含依赖于状态的代码的已有类； 如果特定于状态的代码分散在多个类中， 那么它可能是一个新的类。

2. 声明状态接口。 虽然你可能会需要完全复制上下文中声明的所有方法， 但最好是仅把关注点放在那些可能包含特定于状态的行为的方法上。

3. 为每个实际状态创建一个继承于状态接口的类。 然后检查上下文中的方法并将与特定状态相关的所有代码抽取到新建的类中。

   在将代码移动到状态类的过程中， 你可能会发现它依赖于上下文中的一些私有成员。 你可以采用以下几种变通方式：

   - 将这些成员变量或方法设为公有。
   - 将需要抽取的上下文行为更改为上下文中的公有方法， 然后在状态类中调用。 这种方式简陋却便捷， 你可以稍后再对其进行修补。
   - 将状态类嵌套在上下文类中。 这种方式需要你所使用的编程语言支持嵌套类。

4. 在上下文类中添加一个状态接口类型的引用成员变量， 以及一个用于修改该成员变量值的公有设置器。

5. 再次检查上下文中的方法， 将空的条件语句替换为相应的状态对象方法。

6. 为切换上下文状态， 你需要创建某个状态类实例并将其传递给上下文。 你可以在上下文、 各种状态或客户端中完成这项工作。 无论在何处完成这项工作， 该类都将依赖于其所实例化的具体类。

### 优缺点

**优点**

-  *单一职责原则*。 将与特定状态相关的代码放在单独的类中。
-  *开闭原则*。 无需修改已有状态类和上下文就能引入新状态。
-  通过消除臃肿的状态机条件语句简化上下文代码。

**缺点**

* 如果状态机只有很少的几个状态， 或者很少发生改变， 那么应用该模式可能会显得小题大作。

### 与其他设计模式的关系

- **桥接模式** *(GOF)*、 **状态模式**和**策略模式** *(GOF)* （在某种程度上包括**适配器模式** *(GOF)*） 模式的接口非常相似。 实际上， 它们都基于[组合模式](https://refactoringguru.cn/design-patterns/composite)——即将工作委派给其他对象， 不过也各自解决了不同的问题。 模式并不只是以特定方式组织代码的配方， 你还可以使用它们来和其他开发者讨论模式所解决的问题。
- **状态**可被视为**策略** *(GOF)*的扩展。 两者都基于组合机制： 它们都通过将部分工作委派给 “帮手” 对象来改变其在不同情景下的行为。 *策略*使得这些对象相互之间完全独立， 它们不知道其他对象的存在。 但*状态*模式没有限制具体状态之间的依赖， 且允许它们自行改变在不同情景下的状态。

# 序列模式
## 双缓冲模式

### 定义

用序列的操作模拟瞬间或者同时发生的事情

### 场景

- 我们需要维护一些被增量修改的状态。
- 在修改到一半的时候，状态可能会被外部请求。
- 我们想要防止请求状态的外部代码知道内部的工作方式。
- 我们想要读取状态，而且不想等着修改完成。

### 案例

[图形系统](./Assets/SequencingPatterns/DoubleBuffer)

### 实现方式

定义*缓冲类*封装了**缓冲**：一段可改变的状态。 这个缓冲被增量地修改，但我们想要外部的代码将修改视为单一的原子操作。 为了实现这点，类保存了*两个*缓冲的实例：**下一缓冲**和**当前缓冲**。

当信息*从*缓冲区中读取，它总是读取*当前的*缓冲区。 当信息需要写*到*缓存，它总是在*下一*缓冲区上操作。 当改变完成后，一个**交换**操作会立刻将当前缓冲区和下一缓冲区交换， 这样新缓冲区就是公共可见的了。旧的缓冲区成为下一个重用的缓冲区。

### 优缺点

**优点**

* 双缓冲模式位于底层，它对代码库的其他部分影响较小

**缺点**

* 交换本身需要时间
* 保存两个缓冲区,增加了内存的使用

### 与其他设计模式的关系

独立的设计模式，需要它时自然会想起的模式

## 游戏循环

### 定义

在游玩中不断运行。 每一次循环，它无阻塞地处理玩家输入，更新游戏状态，渲染游戏。 它追踪时间的消耗并控制游戏的速度

### 场景

如果你使用游戏引擎，你不需要自己编写，但是它还在那里。

### 案例

[Unity内置游戏循环](./Assets/SequencingPatterns/GameLoop)

### 实现方式

[Unity内置游戏循环](./Assets/SequencingPatterns/GameLoop)

### 优缺点

- **使用平台的事件循环：**
  - *简单*。你不必担心编写和优化自己的游戏核心循环。
  - *平台友好。* 你不必明确地给平台一段时间让它处理它自己的事件，不必缓存事件，不必管理任何平台输入模型和你的不匹配之处。
  - *你失去了对时间的控制。* 平台会在它方便时调用代码。 如果这不如你想要的那样平滑或者频繁，太糟了。 更糟的是，大多数应用的事件循环并未为游戏设计，通常*是*又慢又卡顿。
- **使用游戏引擎的循环：**
  - *不必自己编写。* 编写游戏循环非常需要技巧。 由于是每帧都要执行的核心代码，小小的漏洞或者性能问题就对游戏有巨大的影响。 稳固的游戏循环是使用现有引擎的原因之一。
  - *不必自己编写。* 当然，硬币的另一面是，如果引擎无法满足你*真正的*需求，你也没法获得控制权。
- **自己写：**
  - *完全的控制。* 你可以做任何想做的事情。你可以为游戏的需求订制开发。
  - *你需要与平台交互。* 应用框架和操作系统通常需要时间片去处理自己的事件和其他工作。 如果你拥有应用的核心循环，平台就没有这些时间片了。 你得显式定期检查，保证框架没有挂起或者混乱。

### 与其他设计模式的关系

独立的设计模式，需要它时自然会想起的模式

## 更新方法

### 定义

通过每次处理一帧的行为模拟一系列独立对象；

### 场景

- 你的游戏有很多对象或系统需要同时运行。
- 每个对象的行为都与其他的大部分独立。
- 对象需要跟着时间进行模拟。

### 案例

[对象的更新行为](./Assets/SequencingPatterns/UpdateMethod)

### 实现方式

* **游戏世界**管理**对象集合**。 

* 每个对象实现一个**更新方法**模拟对象在**一帧**内的行为。

* 每一帧，游戏循环更新集合中的每一个对象。

### 优缺点

**优点**

* 对象行为独立于游戏世界

**缺点**

* 增加系统复杂度

### 与其他设计模式的关系

- 这个模式，以及**游戏循环**模式和**组件模式**，是构建游戏引擎核心的三位一体。
- 当你关注在每帧中更新实体或组件的缓存性能时，**数据局部性模式**可以让它跑到更快。
- [Unity](http://unity3d.com/)框架在多个类中使用了这个模式，包括 [`MonoBehaviour`](http://docs.unity3d.com/Documentation/ScriptReference/MonoBehaviour.Update.html)。

# 行为模式
## 字节码

### 定义

将行为编码为虚拟机器上的指令，赋予其数据的灵活性

### 场景

* 应当用在你有许多行为需要定义，例如：一系列的法术技能

### 案例

[法术技能](./Assets/BehavioralPatterns/Bytecode)

### 实现方式

* **指令集** 定义了可执行的底层操作。

* 一系列的指令被编码为**字节序列**。 

* **虚拟机** 使用 **中间值栈** 依次执行这些指令。

* 通过组合指令，可以定义复杂的高层行为。

### 优缺点

**优点**

* 可扩展性比较好，灵活。
* 增加了新的解释表达式的方式。
* 易于实现简单文法。

**缺点**

- 过于底层，繁琐易错。
- 编译慢或者其他工具因素导致迭代缓慢。
- 安全性依赖编程者。如果想保证行为不会破坏游戏，你需要将其与代码的其他部分隔开。

### 与其他设计模式的关系

- 这一章节的近亲是GoF的[解释器模式](http://en.wikipedia.org/wiki/Interpreter_pattern)。两种方式都能让你用**数据组合行为**。

  事实上，最终你两种模式*都*会使用。你用来构造字节码的工具会有内部的对象树。这也是**解释器模式**所能做的。

  为了编译到字节码，你需要递归回溯整棵树，就像用**解释器模式**去解释它一样。 *唯一的* 不同在于，**不是立即执行一段行为**，而是生成整个字节码再执行。

## 子类沙箱

### 定义

用一系列由基类提供的操作定义子类中的行为。

### 场景

- 你有一个能推导很多子类的基类。
- 基类可以提供子类需要的所有操作。
- 在子类中有行为重复，你想要更容易地在它们间分享代码。
- 你想要最小化子类和程序的其他部分的耦合。

### 案例

[超级英雄](./Assets/BehavioralPatterns/SubclassSandbox)

### 实现方式

* **基类**定义抽象的**沙箱方法**和几个**提供的操作**。

* 将操作标为protected，表明它们只为子类所使用。

* 每个推导出的**沙箱子类**用提供的操作实现了沙箱函数。

### 优缺点

**优点**

* 通过超级父类可以快速构建只有部分功能的子类

**缺点**

* 会有很多冗余代码。
* 游戏引擎中的每一部分都会与这些类耦合。
* 当外部代码需要改变时，一些随机超能力代码有很大几率会损坏。

### 与其他设计模式的关系

- 当你使用**更新模式**时，你的更新函数通常也是沙箱方法。
- 这个模式与**模板方法** *(GOF)*正相反。 两种模式中，都使用一系列受限操作实现方法。 使用子类沙箱时，方法在推导类中，受限操作在基类中。 使用模板方法时，*基类* 有方法，而受限操作在*推导*类中。
- 你也可以认为这个模式是**外观模式**  *(GOF)*的变形。 外观模式将一系列不同系统藏在简化的API后。使用子类沙箱，基类起到了在子类前隐藏整个游戏引擎的作用。

## 类型对象

### 定义

创造一个类A来允许灵活地创造新“类型”，类A的每个实例都代表了不同的对象类型。

### 场景

- 你不知道你后面还需要什么类型。（举个例子，如果你的游戏需要支持资料包，而资料包有新的怪物品种呢？）
- 想不改变代码或者重新编译就能修改或添加新类型，即达到子类的名字、基本属性都是可以通过Json配置的目的

### 案例

[多类型的怪物](./Assets/BehavioralPatterns/TypeObject)

### 实现方式

* 定义**类型对象**类和**有类型的对象**类。

* 每个类型对象实例代表一种不同的逻辑类型。 

* 每种有类型的对象保存**对描述它类型的类型对象的引用**。

* 实例相关的数据被存储在有类型对象的实例中，被同种类分享的数据或者行为存储在类型对象中。 

* 引用同一类型对象的对象将会像同一类型一样运作。 

*这让我们在一组相同的对象间分享行为和数据，就像子类让我们做的那样，但没有固定的硬编码子类集合。*

### 优缺点

**优点**

* 可以快速、便捷的添加新的类型

**缺点**

* 更难为每种类型定义行为
* 需要手动追踪类型对象

### 与其他设计模式的关系

- 这个模式处理的高层问题是在多个对象间分享数据和行为。 另一个用另一种方式解决了相同问题的模式是**原型模式**。

- 类型对象是**享元模式**的近亲。 两者都让你在实例间**分享代码**。使用享元，意图是**节约内存**，而分享的数据也许不代表任何概念上对象的“类型”。 使用类型对象模式，焦点在组织性和灵活性。

- 这个模式和**状态模式**有很多相似之处。 两者都**委托对象的部分定义给另外一个对象**。 通过**类型对象**，我们通常委托了对象*是*什么：不变的数据概括描述对象。 通过**状态**，我们委托了对象*现在是什么*：暂时描述对象当前状态的数据。

  当我们讨论对象改变它的类型时，你可以认为类型对象起到了和状态相似的职责。

# 解耦模式
## 组件模式

### 定义

允许一个单一的实体跨越多个不同域而不会导致耦合。

### 场景

- 你有一个涉及多个域的类，但是你希望这些域保持相互解耦。
- 一个类越来越庞大，越来越难以开发。
- 你希望定义许多共享不同能力的对象，但采用继承的办法却无法令你精确地重用代码。

### 案例

[RPG游戏](./Assets/DecouplingPatterns/Component)

### 实现方式

* 单一实体跨越了多个领域。

* 为了保持领域之间相互分离，将每部分代码放入**各自的组件类**中。

* 实体被简化为*组件的容器*。

### 优缺点

**优点**

* 各个功能模块进行解耦

**缺点**

* 增加了组件之间的通信开销

### 与其他设计模式的关系

- 这种模式与GoF的策略模式类似。 两种模式都是将对象的行为取出，划入单独的重述对象。 与对象模式不同的是，分离的策略模式通常是无状态的——它封装了算法，而没有数据。 它定义了对象*如何*行动，但没有定义对象*是*什么。

  组件更加重要。它们经常保存了对象的状态，这有助于确定其真正的身份。 但是，这条界限很模糊。有一些组件也许根本没有任何状态。 在这种情况下，你可以在不同的容器对象中使用相同的组件*实例*。这样看来，它的行为确实更像一种策略。

## 事件队列

### 定义

事件队列在队列中按先入先出的顺序存储一系列通知或请求。 发送通知时，将请求放入队列并返回。 处理请求的系统之后稍晚从队列中获取请求并处理。 这解耦了发送者和接收者，既静态又及时。

### 场景

1. [通信](./Assets/DecouplingPatterns/EventQueue)

### 案例

[通信](./Assets/DecouplingPatterns/EventQueue)

### 实现方式

1. 制定消息类型，内容格式
2. 创建消息队列
3. 添加监听事件
4. 传递事件
5. 激活对应方法

### 优缺点

**优点**

* 解耦发送者和接收者

**缺点**

### 与其他设计模式的关系

- 我在之前提到了几次，很大程度上， 这个模式是广为人知的**观察者模式**的异步实现。

- 就像其他很多模式一样，事件队列有很多别名。 其中一个是“消息队列”。这通常指代一个更高层次的实现。 事件队列在应用*中*，消息队列通常在应用*间*交流。

  另一个术语是“发布/提交”，有时被缩写为“pubsub”。 就像“消息队列”一样，这通常指代更大的分布式系统，而不是现在关注的这个模式。

- 很像GoF的**状态模式**，需要一个输入流。如果想要**异步响应**，可以考虑用队列存储它们。

  当你有一对状态机相互发送消息时，每个状态机都有一个小小的未处理队列（被称为一个*信箱*）， 然后你需要重新发明[actor model](http://en.wikipedia.org/wiki/Actor_model)。

## 服务定位器

### 定义

提供服务的全局接入点，避免使用者和实现服务的具体类耦合。

### 场景

* 全局只有一个；建议和单例一样少用

### 案例

[全局声音管理](./Assets/DecouplingPatterns/ServiceLocator)

### 实现方式

* **服务** 类定义了一堆操作的抽象接口。

* 具体的**服务提供者**实现这个接口。 

* 分离的**服务定位器**提供了通过查询获取服务的方法，同时隐藏了服务提供者的具体细节和定位它的过程。

### 优缺点

**优点**

-  你可以保证一个类只有一个实例。
-  你获得了一个指向该实例的全局访问节点。
-  仅在首次请求对象时对其进行初始化。

**缺点**

-  违反了_单一职责原则_。 该模式同时解决了两个问题*(在一个方法中进行了创建类和提供类对象的操作)*。
-  单例模式可能掩盖不良设计， 比如程序各组件之间相互了解过多等。
-  该模式在多线程环境下需要进行特殊处理， 避免多个线程多次创建单例对象。
-  客户端代码单元测试可能会比较困难， 因为许多测试框架以基于继承的方式创建模拟对象。 由于构造函数是私有的， 而且绝大部分语言无法重写静态方法， 所以你需要想出仔细考虑模拟单例的方法。 要么干脆不编写测试代码， 或者不使用此模式。

### 与其他设计模式的关系

- **服务定位模式**在很多方面是**单例模式**的兄弟，在应用前值得看看哪个更适合你的需求。

# 优化模式
## 数据局部性

### 定义

合理组织数据，充分使用CPU的缓存来加速内存读取

### 场景

1. 性能问题确实由缓存不命中引起

### 案例

[更新大量相同组件](./Assets/OptimizationPatterns/DataLocality)

### 实现方式

* 现代的CPU有**缓存来加速内存读取**。

* 它可以**更快地读取最近访问过的内存的毗邻内存**。 

* 通过**提高内存局部性**来提高性能——保证数据**以处理顺序排列在连续内存上**。

### 优缺点

**优点**

1. 提高缓存利用率
2. 加快数据读写速度

**缺点**

* 系统更加复杂，更加缺乏灵活性

### 与其他设计模式的关系

- 这一模式几乎完全得益于同类对象的连续存储数组。 随着时间的推移，你也许需要向那个数组增加或删除对象。 **对象池模式**正是关于这一点。
- 这一章大部分围绕着**组件模式**。 这种模式的数据结构绝对是为缓存优化的最常见例子。事实上，使用组件模式让这种优化变得容易了。 由于实体是按“领域”（AI，物理，等等）更新的，将它们划出去变成组件，更容易将它们保存为对缓存友好的合适大小。但是这不意味你*只能*为组件使用这个模式！ 任何需要接触很多数据的关键代码，考虑数据局部性都是很重要的。

## 脏标识模式

### 定义

### 场景

### 案例

### 实现方式

### 优缺点

**优点**

**缺点**

### 与其他设计模式的关系

## 对象池模式

### 定义

### 场景

### 案例

### 实现方式

### 优缺点

**优点**

**缺点**

### 与其他设计模式的关系

## 空间分区

### 定义

### 场景

### 案例

### 实现方式

### 优缺点

**优点**

**缺点**

### 与其他设计模式的关系

