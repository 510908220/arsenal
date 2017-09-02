# traceback

异常信息通常使用`sys.exc_info()`返回,这里介绍一下返回值:
- type:  异常的类型, `BaseException`的子类
- value:  异常实例
- traceback: `traceback object `, 异常发生时调用栈信息, 是由一些列`Frame`组成的.




##  常用函数

这里介绍的`traceback`模块主要用来提取、格式化、打印调用栈信息. 下面简单介绍一下异常操作的函数, 都是基于下面例子:
```python
import sys
import traceback

def get_ip(line):
    ip, _ = line.split(' ')
    return ip

def main():
    lines = [
        '192.168.0.1 xiaoming',
        '172.17.3.1'
    ]
    ips = []
    for line in lines:
        ips.append(get_ip(line))
    return ips

if __name__ == '__main__':
    try:
        main()
    except:
        etype, value, tb = sys.exc_info()
        # 函数
```



- `traceback.print_tb(tb, limit=None, file=None)` 执行`traceback.print_tb(tb)`可以看到如下输出:

  ```python
    File "tc.py", line 24, in <module>
      main()
    File "tc.py", line 17, in main
      ips.append(get_ip(line))
    File "tc.py", line 6, in get_ip
      ip, _ = line.split(' ')
  ```

  将异常的每一个调用栈打印出来了. 说一下剩余两个参数的含义:

  - limit:  控制打印调用栈的个数,对于上面个数就是3,如果改`traceback.print_tb(tb,1)`,那么只会输出:

    ```python
      File "tc.py", line 24, in <module>
        main()
    ```

- `traceback.extract_tb(tb, limit=None)`
  返回异常时调用栈的一个列表. 类型为`FrameSummary`
  执行输出:
  ```
  [<FrameSummary file tc.py, line 20 in <module>>, 
  <FrameSummary file tc.py, line 15 in main>, 
  <FrameSummary file tc.py, line 5 in get_ip>]
  ```


- `traceback.print_exception(etype, value, tb, limit=None, file=None, chain=True)` 

  这个函数会比`print_tb`打印更多信息,如下:

  ```python
  Traceback (most recent call last):
    File "tc.py", line 24, in <module>
      main()
    File "tc.py", line 17, in main
      ips.append(get_ip(line))
    File "tc.py", line 6, in get_ip
      ip, _ = line.split(' ')
  ValueError: not enough values to unpack (expected 2, got 1)
  ```

- `traceback.print_exc(limit=None, file=None, chain=True)`
  打印异常信息,等于`print_exception(*sys.exc_info(), limit, file, chain)`

- ​`traceback.format_exc(limit=None, chain=True)`
  和` print_exc(limit)`很像,只是返回的是个字符串.

- ​`traceback.extract_stack(f=None, limit=None)`
  获取调用栈列表:
  ```python
  import traceback

  def get_ip(log):
      for stack in traceback.extract_stack():
          print('#:', stack)

      return log.split(' ')[0]

  def main():
      ip = get_ip('192.168.0.1 linux')
  main()
  ```
  输出为:
  ```python
  #: <FrameSummary file tc.py, line 13 in <module>>
  #: <FrameSummary file tc.py, line 12 in main>
  #: <FrameSummary file tc.py, line 5 in get_ip>
  ```
   `FrameSummary`表示调用栈的某一层.

- `traceback.print_stack(f=None, limit=None, file=None)  `
  打印出调用栈,如图:
  ```python
    import traceback
    def get_ip(log):
        traceback.print_stack()
        return log.split(' ')[0]
    def main():
        ip = get_ip('192.168.0.1 linux')
    main()
  ```
  输出为
   ```python
  File "tc.py", line 12, in <module>
    main()
  File "tc.py", line 11, in main
    ip = get_ip('192.168.0.1 linux')
  File "tc.py", line 5, in get_ip
    traceback.print_stack()
   ```




## FrameSummary
`FrameSummary`是 python 3.5出现的,表示调用栈的某一层. 

其中`traceback.extract_tb`和`traceback.extract_stack`都会返回调用栈的列表,我理解区别就是`traceback.extract_tb`是获取发生异常时调用栈的列表.



## 异常处理

当我们再处理异常的时候, 可以使用者两种方式:

- `traceback.print_exc()`: 不使用日志时
- `logging.exception('')`: 使用日志时

这两个模块的方法都会打印这样的异常信息:

```python
Traceback (most recent call last):
  File "tc.py", line 25, in <module>
    main()
  File "tc.py", line 18, in main
    ips.append(get_ip(line))
  File "tc.py", line 7, in get_ip
    ip, _ = line.split(' ')
ValueError: not enough values to unpack (expected 2, got 1)
```

看到这样的异常信息, 只能知道个大概, 不足以定位问题,  因为我们看不到相关的**值**,对于这个例子相对还好看些,如果一个异常的调用栈很长, 缺少很多上下文信息是很难定位问题的.  



