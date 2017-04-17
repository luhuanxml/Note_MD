# MVP 学习心得

###mvp在使用时有model中有一个监听回调，用来处理数据请求遇到的各种情况，成功或者失败或者其他情况。
###引入Rxjava后，这个监听回调可以省略，让model的数据请求方法直接返回Observable《Data》 的Observable对象，所带泛型为我们需要的数据的类型。然后在presenter中modle对象调用了数据请求方法后返回Observable《Data》对象，后跟线程调度后，subscribe()方法订阅，然后再customer thrown action中去处理各种情况。

### 可以尝试将同一页面的数据请求和显示，使用同一个presenter类，提供多个数据请求的present方法。同一页面没有必要非要一个请求写一个presenter。