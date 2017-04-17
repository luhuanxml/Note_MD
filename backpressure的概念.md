# RxJava中backpressure这个概念的理解

## backpressure
### 在rxjava中会经常遇到一种情况就是被观察者发送消息十分迅速以至于它的操作符或者订阅者不能及时的响应这些消息。那么问题来了，要怎么处理这些慢慢堆积起来的消息呢？
### 举个例子，使用zip操作符把两个无限大(假设)的被观察者压缩在一起，其中一个被观察者发送消息的速度是另外一个的两倍。一个比较天真(不科学)的做法就是把发送比较快的消息缓存起来，当比较慢的观察者发送消息的时候取出来将他们结合在一起。但是这样会使得rxJava变得笨重且十分占用系统资源。
### 在rxJava中有多重控制流以及反压力(backpressure)策略以应对当一个快速发送消息的被观察者遇到一个处理消息缓慢的观察者。本页面将会解释说明这些坑以及向你展示你应当怎么设计属于你自己的被观察者和操作符去应对流量控制(flow control)

## Hot and cold Observables,and multicasted(多路广播)
### Observables
### 一个cold Observable在它的订阅者订阅他的时候发送完整的数据序列，不管它的观察者们什么时候订阅它，或者观察者们以什么速率去消耗这个消息，都不会扰乱observable发送的完整性。例如把一个静态的迭代器(iterable)对象转换成了一个observable，这个observable将会对后来每个与它发生订阅关系的观察者发送通用的序列。cold observable的例子可能包括数据库查询的结果，文件序列，或者网络请求。
### Hot observable不管有没有订阅者订阅，他们创建后就开始发射数据流。一个比较好的示例就是鼠标事件。不管系统有没有订阅者监听鼠标事件，鼠标事件一直在发生，当有订阅者订阅后，从订阅后的事件开始发送给这个订阅者，之前的事件这个订阅者是接受不到的;如果订阅者取消订阅了，鼠标事件依然继续发射。
### 当一个cold observable是multicast的时候，为了应对反压力，应该把一个cold observable转换成hot observable。
### cold observable 相当于响应式拉(就是observer处理完了一个事件就从observable拉取下一个时间)。
### hot observable 通常不能很好的处理响应式拉模型，但是它却是更好的处理本页讨论的流量控制问题的候选人，例如使用onBackpressureBuffer or onBackpressureDrop operators, throttling, buffers, or windows。