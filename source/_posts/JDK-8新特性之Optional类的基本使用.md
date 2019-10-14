---
title: JDK 8新特性之Optional类的基本使用
date: 2019-08-19 17:59:44
tags: [JDK]
---

## 学习笔记 : JDK 8新特性之Optional类的基本使用

### 简介
*到目前为止,臭名昭著的空指针异常是导致Java应用程序失败的最常见原因. 以前为了解决空指针异常,Google公司著名的Guava项目引入了`Optional类`,Guava通过使用检查空值的方法来防止代码受污染,它鼓励程序员写更干净的代码~ 受到Google Guava的启发,Option类已经成为Java 8类库的一部分. `java.util.Optional<T>`是一个容器类,它可以保存类型为T的值,代表这个值存在. 或者仅仅保存null,表示这个值不存在. 原来用null表示一个值不存在,现在Optional可以更好的表达这个概念,并且可以避免空指针异常~*

- *Optional类的javadoc对其的描述为 : 这是一个可以为null的容器对象,如果值存在则`isPresent()`方法会返回true,调用`get()`方法会返回该对象*


### 类方法
1. `static <T> Optional<T> empty()` : 返回空的 Optional 实例

2. `boolean equals(Object obj)` : 判断其他对象是否等于 Optional

3. `Optional<T> filter(Predicate<? super <T> predicate)` : 如果值存在,并且这个值匹配给定的 predicate,返回一个Optional用以描述这个值,否则返回一个空的Optional

4. `<U> Optional<U> flatMap(Function<? super T,Optional<U>> mapper)` : 如果值存在,返回基于Optional包含的映射方法的值,否则返回一个空的Optional

5. `T get()` : 如果在这个Optional中包含这个值,返回值,否则抛出异常:NoSuchElementException

6. `int hashCode()` : 返回存在值的哈希码,如果值不存在则返回 0

7. `void ifPresent(Consumer<? super T> consumer)` : 如果值存在则使用该值调用 consumer,否则不做任何事情

8. `boolean isPresent()` : 如果值存在则方法会返回true,否则返回 false

9. `<U>Optional<U> map(Function<? super T,? extends U> mapper)` : 如果有值,则对其执行调用映射函数得到返回值. 如果返回值不为 null,则创建包含映射返回值的Optional作为map方法返回值,否则返回空Optional

10. `static <T> Optional<T> of(T value)` : 返回一个指定非null值的Optional

11. `static <T> Optional<T> ofNullable(T value)` : 如果为非空,返回 Optional 描述的指定值,否则返回空的 Optional

12. `T orElse(T other)` : 如果存在该值,返回值,否则返回 other

13. `T orElseGet(Supplier<? extends T> other)` : 如果存在该值,返回值,否则触发 other,并返回 other 调用的结果

14. `<X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier)` : 如果存在该值,返回包含的值，否则抛出由 Supplier 继承的异常

15. `String toString()` : 返回一个Optional的非空字符串,用来调试