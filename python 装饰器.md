# python 装饰器

## 函数

原始函数

```python
def func():
    print("我是函数1")
```

给函数 `func` 增加新功能，记录函数的执行日志：

- 直接改写原来的函数

    ```python
    def func():
        print("我是函数1")
        logging.info("函数1运行中.......")
    ```

    - 所有函数`func1, func2, func3 ......`都要重新改写

- 用函数指针的方法

    ```python
    def use_logging(func):
        logging.info("函数1运行中.......")
        func()
    ```

    - 对于所有函数 `func1, func2, func3, ......` 都可以用 `use_logging()` 改写	
        - `use_logging(func2)`
        - `use_logging(func3)`
    - 但是所有调用 `func1, func2, ......` 的地方都要改成 `use_logging(func1), ......`

## 装饰器

有没有不该写原有函数的调用形式，就能新增功能的方法呢？

### 简单装饰器

实现：把函数指针传入函数，在通过语法糖 `@` 实现不修改原来函数的调用形式

- 创建接收函数指针的装饰器函数

    ```python
    def use_logging(func):
        
        def wrapper(*args, **kwargs):
            logging.info("{} 正在运行......".format(func.__name__))
            return func(*args, **kwargs)
       	return wrapper
    ```

- 通过装饰器函数返回一个和原函数同名的新函数

    ```python
    func1 = use_logging(func1)
    ```

- 按照原来的形式调用函数

    ```py
    func1()
    ```

- 以上的两步可以通过 `@` 语法糖简化

    ```python
    @use_logging
    def func2():
        print("我是函数2")
        
    func2()
    ```

### 带参数的装饰器

调用修饰器还可以带参数

- 带参数的修饰器

    ```python
    def use_logging(level):
        def decorator(func):
            def wrapper(*args, **kwargs):
                if level == "info":
                    logging.info("{} 正在运行 ......".format(func.__name__))
                return func(*args)
            return wrapper
        
        return decorator
    ```

    - `wrapper` 和 `decorator` 都可以看成是闭包函数
        - `wrapper` 
            - `wrapper` 的参数是 `*args, **kwargs`
            - 抓取的是外部变量 `func`
        - `decorator` `
            - `decorator` 的参数是 `func`
            - 抓取的外部变量是 `leve`

- 通过带参数的装饰器调用原函数`func1`

    ```python
    @use_logging(level="info")
    def func1(name = "函数1"):
        print("我是{}".format(name))
        
    func1()
    ```

### 类装饰器

使用类装饰器可以依靠内部的 `__call__` 方法，当使用 `@` 形式把装饰器加到函数上时，就会调用 `__call__` 方法

- 类装饰器

    ```python
    class cls_dcrt:
        def __init__(self, func):
            self._func = func
        
        def __call__(self):
            print("类装饰器正在运行......")
            self._func()
            print("类装饰器运行结束")
    ```

- 安装类装饰器

    ```python
    @Foo
    def func1():
        print("函数1")
    ```

- 调用函数

    ```python
    func1()
    ```

    ```bash
    类装饰器运行中......
    函数1内打印
    类装饰器运行结束
    ```

### 函数的元信息

通过装饰器修饰的函数，会丢失原函数的源信息，比如：`docstirng, __name__, args`

```python
def logged(func):
    def with_logging(*args, **kwargs):
        """函数[whith_logging]"""
        print("当前函数：{}".format(func.__name__))
        return func(*args, **kwargs)
    return with_logging

@logged
def add_mul(x):
    """函数[add_mul]"""
    return x+ x*x

print("func(10) = {}".format(add_mul(10)))
print("{}".format(add_mul.__doc__))
```

结果：

```bash
当前函数：add_mul
func(10) = 110
函数[whith_logging]  # 函数名有问题
```

增加 `functools.wraps` 后

```python
from functools import wraps

def logged(func):
    #@wraps(func)
    def with_logging(*args, **kwargs):
        """函数[whith_logging]"""
        print("当前函数：{}".format(func.__name__))
        return func(*args, **kwargs)
    return with_logging

@logged
def add_mul(x):
    """函数[add_mul]"""
    return x+ x*x

print("func(10) = {}".format(add_mul(10)))
print("{}".format(add_mul.__doc__))
```

结果：

```bash
当前函数：add_mul
func(10) = 110
函数[add_mul]
```

