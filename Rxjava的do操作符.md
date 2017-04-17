##Do
### Do操作符就是给Observable的生命周期的各个阶段加上一系列的回调监听，当Observable执行到这个阶段的时候，这些回调就会被触发，在Rxjava实现了很多的doXXX操作符。
### DoOnEach可以给Observable加上这样的一个回调：Observable每发射一个数据的时候就会触发这个回调，不仅包括onNext还包括onError和onCompleted。
### DoOnNext则只是onNext的时候才会被触发。

![](http://reactivex.io/documentation/operators/images/doOnEach.png)

![](http://reactivex.io/documentation/operators/images/doOnNext.png)

### doOnSubscribe和 doOnUnSubscribe则会在Subscriber进行订阅和反订阅的时候触发回调。当一个Observable通过OnError或者OnCompleted结束的时候，会反订阅所有的Subcriber。
