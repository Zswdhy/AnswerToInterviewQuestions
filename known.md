# Python

##  内置方法

### ` __dict__`

```python
class Demo:
    def __init__(self):
        self.a = "hello world"
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

