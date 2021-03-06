# 创建操作

## Create

使用一个函数从头开始创建一个Observable

![create][image-1]

你可以使用`Create`操作符从头开始创建一个Observable，给这个操作符传递一个接受观察者作为参数的函数，编写这个函数让它的行为表现为一个Observable--恰当的调用观察者的onNext，onError和onCompleted方法。

一个形式正确的有限Observable必须尝试调用观察者的onCompleted正好一次或者它的onError正好一次，而且此后不能再调用观察者的任何其它方法。

![create][image-2]

RxJava将这个操作符实现为 `create` 方法。

建议你在传递给`create`方法的函数中检查观察者的`isUnsubscribed`状态，以便在没有观察者的时候，让你的Observable停止发射数据或者做昂贵的运算。

示例代码：

```java

Observable.create(new ObservableOnSubscribe<Integer>() {
            @Override
            public void subscribe(@NonNull ObservableEmitter<Integer> observableEmitter) {
                try {
                    if (!observableEmitter.isDisposed()) {
                        for (int i = 1; i < 5; i++) {
                            observableEmitter.onNext(i);
                        }
                        observableEmitter.onComplete();
                    }
                } catch (Exception e) {
                    observableEmitter.onError(e);
                }
            }
        }).subscribe(new Observer<Integer>() {
            @Override
            public void onSubscribe(@NonNull Disposable d) {

            }

            @Override
            public void onNext(@NonNull Integer integer) {
                System.out.println("Next: " + integer);
            }

            @Override
            public void onError(@NonNull Throwable e) {
                System.err.println("Error: " + e.getMessage());

            }

            @Override
            public void onComplete() {
                System.out.println("Sequence complete.");
            }
        });~~ 
```

输出：

```
Next: 1
Next: 2
Next: 3
Next: 4
Sequence complete.
``` 

`create`方法默认不在任何特定的调度器上执行。

* Javadoc: [`create(OnSubscribe)`][1]

[1]:	http://reactivex.io/RxJava/javadoc/rx/Observable.html#create(rx.Observable.OnSubscribe)

[image-1]:	../images/operators/create.c.png
[image-2]:	../images/operators/create.png