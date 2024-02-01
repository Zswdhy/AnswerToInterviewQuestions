# Python

##  内置方法

### ` __dict__`

```python
class Demo:
    def __init__(self):
        self.a = "hello world"
        # 获取类或者对象的属性，返回字典类型
        print(self.__dict__)
		# {'a': 'hello world'}       
```



### type 元类

* 本质

  使用 class 关键字创建类时候，python 解释器 **底层** 使用 type() 来创建类，因此可以使用 type() 手动实现动态创建类

  ```python
  class object:
      """
      The base class of the class hierarchy.
      
      When called, it accepts no arguments and returns a new featureless
      instance that has no instance attributes and cannot be given any.
      """
      def __init__(self): # known special case of object.__init__
          """ Initialize self.  See help(type(self)) for accurate signature. """
          pass
  ```


* type()方法解释

  ```python
  def __init__(cls, what, bases=None, dict=None): # known special case of type.__init__
      """
      type(object_or_name, bases, dict)
      type(object) -> the object's type
      type(name, bases, dict) -> a new type
      # (copied from class doc)
      """
      pass
  ```

  - **第一个参数**：name 表示**类名称**，字符串类型
  - **第二个参数**：bases 表示继承对象（父类），元组类型，单元素使用逗号
  - **第三个参数**：attr 表示属性，这里可以填写类属性、类方式、静态方法，采用字典格式，key为**属性名**，value为**属性值**

* 代码案例

  ```python
  def eat(self, food):
      print(f"eat {food}")


  if __name__ == '__main__':
      # 元类内第三个参数的 key 为真实的函数名
      People = type("People", (object,), {"eat": eat})
      # People = type("People", (object,), {"drink": eat})
      p = People()
      p.eat("rice")
      # p.drink("rice")
  ```


### mateclass

http://c.biancheng.net/view/2293.html



### staticmethod、classmethod

* staticmethod
  * 可是使用 类名.方法名调用
  * 不用写 self 关键字，和普通函数一样
* classmethod
  * 可以使用 类名.方法名调用
  * 需要使用 cls 关键词

### property

```
class DataSet(object):
    @property
    def method_with_property(self):
        return 15


l = DataSet()
# proper 在调用的时候不使用 ()，直接使用函数名称即可
print(l.method_with_property)
```



## list

### pop remove del 三者的区别

```python
num_list = [-1, 1, 2, 3, 4, 5, 2, 6]

# 值删除，如果存在会抛出异常，无返回值
# a = num_list.remove(2)

# index 删除，index 大于 list 长度时，会抛出越界错误，有返回值
# _ = num_list.pop(3)

# 引用删除
# index 删除，index 大于 list 长度时，会抛出越界错误
del num_list[1]

```



## 垃圾回收机制

* 引用计数

  ```python
  python是根据对象的引用计数是否为0，来进行垃圾回收，释放内存的

  引用计数增加的几种情况:
      1、对象创建被赋值给变量
      2、对象被其他的变量引用(引用它的变量赋值给了其他变量)
      3、当对象被当成参数传入函数中
      4、对象被放到其他的容器中(或者保存为其他对象的属性)

  引用计数减少的几种情况:
      1、保存对象的变量被删除
      2、引用该对象的变量被重新赋值，引用了其他的对象
      3、被当成函数参数传入函数中，当函数调用结束
      4、对象从容器中删除(保存对的属性被删除)
  ```


* 标记清除

  ```
  追踪回收（tracing GC）技术
  	它分为两个阶段：第一阶段是标记阶段，GC会把所有的『活动对象』打上标记，第二阶段是把那些没有标记的对象『非活动对象』进行回收。那么GC又是如何判断哪些是活动对象哪些是非活动对象的呢？

  	对象之间通过引用（指针）连在一起，构成一个有向图，对象构成这个有向图的节点，而引用关系构成这个有向图的边。从根对象（root object）出发，沿着有向边遍历对象，可达的（reachable）对象标记为活动对象，不可达的对象就是要被清除的非活动对象。根对象就是全局变量、调用栈、寄存器。
  ```


* 分代回收


# 设计模式

## 单例模式

```python

class Animal:
    INSTANCE = None
    IS_CREATE = True

    def __new__(cls, *args, **kwargs):
        # 使用类属性做判断，只实例化一次
        if Animal.INSTANCE is None:
            Animal.INSTANCE = super().__new__(cls)
        return Animal.INSTANCE

    def __init__(self, name, age, color):
        # 使用类属性做判断，只实例化一次
        if Animal.IS_CREATE:
            self.name = name
            self.age = age
            self.color = color
            Animal.IS_CREATE = False

    def animal_info(self):
        print(f"name:{self.name},age:{self.age},color:{self.color}")


if __name__ == '__main__':
    animal1 = Animal("非洲象", 10, "棕色")
    animal2 = Animal("中华田园犬", 5.5, "橘色")
    animal1.animal_info()
    animal2.animal_info()
```



# Mysql

## 乐观锁和悲观锁

### 锁

锁是在并发下控制多个操作的顺序执行，以此来保证数据安全的变动。

Innodb 默认使用的是**行锁**，行锁是基于**索引**，若想加行锁，需要命中目标索引，否则将使用**表锁**。

### 乐观锁

​	乐观锁的“乐观情绪”体现在，它认为数据的变动不会太频繁。因此，它允许多个事务同时数据数据进行变动。通过**版本号**或**时间戳**进行数据校验。

### 悲观锁

​	悲观锁认为被它保护的数据及其**不安全**，每时每刻都有可能**变动**，一个事务拿到悲观锁后（可以解列未一个用户），其他任何事物都不能对该数据进行修改，只能等待锁被释放才可以执行。

### 优缺点

* 悲观锁
  * 优点：悲观锁利用数据库中的锁机制来实现数据变化的顺序执行，这是最有效的办法
  * 缺点：一个使用使用悲观所对数据加锁之后，其他事务将不能对加锁的数据进行除了查询之外的所有操作，如果该事物执行时间很长，那么其他事务将一直等待，那势必影响我们系统的吞吐量。
* 乐观锁
  * 优点：乐观锁不在数据库上加锁，任何事物都可以对数据进行操作，在更新的时候才进行校验，这样避免了悲观锁造成的吞吐量下降的劣势。
  * 缺点：乐观锁因为是通过人为实现，它仅仅适用于我们自己的业务中，如果有外来事物插入，那么就可能发生错误。

### 使用场景

* 悲观所
  * 影响吞吐量，适合应用在**写**居多的场景下
* 乐观锁
  * 乐观锁是为了避免悲观锁的弊端出现，适合应用在读**居**多的场景

