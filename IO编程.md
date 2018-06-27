#  IO

异步IO 效率高，难度大，同步IO效率低，简单

## 文件读写

### 读文件

```python
# 使用Python内置的open()函数，传入文件名和标识符
f=open('路径','r')---read
# 读数据
f.read()
#读完必须要关闭文件流
f.close()
#这里关闭有一个问题，就是当读取失败的时候，f.close()就不会被调用，所以为了保证这个流被关闭，可以使用try..finally来实现

try:
    f=open()
    f.read()
finally:
    if f:
        f.close()
        
        
#这样写太繁琐，所以Python引入了with语句来自动帮我们调用close()方法
with open('路径','r') as f:
    print(f.read())
    
#read(size)可以控制每次读取size个字节的内容，另外调用readline()可以每次读取一行的内筒，按行返回list

for line in f.readlines():
    print(line.strip())
    
# 读二进制文件,使用rb模式打开文件
f=open('路径','rb')
f.read()

#字符编码
f=open('路径','r',encoding='gbk')
f.read()

#忽略一些编码错误
f=open('路径','r',encoding='gbk',errors='ignore')
```

### 写文件

还是使用open，传入的标识符是`w`或者`wb`表示文本文件或者二进制文件

```python
f=open('路径','w')
f.write('文件内筒')
f.close()

#同样有打开四百多问题，使用with来保险一点
with open('路劲','w') as f:
    f.write('hello world!')

# 也可以传入encoding参数，进行自定编码转换
# 本来是覆盖原来数据写入，如果要追加，要加入一个a，用append模式

```

## StringIO

在内存中读写str

要把str写入StringIO.需要创建一个StringIO，然后像写文件一样写入就好了

```python
from io import StringIO
f=StringIO()
f.write('hello')
f.write(' ')
f.write('world!')
f.getvalue()
hello world!
#getvalue()方法用于获得写入后的str
```

读取StringIO,像读文件一样读取

```python
>>> while True:
...     s=f.readline()
...     if s=='':
...             break
...     print(s.strip())
...
```

**BytesIO**:用来操作二进制数据

+ 写入bytes

```python
from io import BytesIO
f=BytesIO()
>>> f.write('中文'.encode('utf-8'))
6
>>> f.getvalue()
b'\xe4\xb8\xad\xe6\x96\x87'

```

+ 读取bytes

```python
from io import BytesIO
f=BytesIO('b\xe4\xb8\xad\xe6\x96\x87')
f.read()

```

## 操作文件和目录

Python内置的`os`模块可以执行这些目录和文件对的操作

```python
>>> import os
>>> os.name
'nt'--代表windows

#环境变量
os.environ
`各种环境变量`
# 获得某个环境变量
os.environ.get('PATH')

```

操作文件和目录

一部分函数在`os`模块下，一部分在`os.path`下

```python
# 查看当前目录的绝对路径
>>> import os
>>> os.path.abspath('.')
'C:\\Users\\xian'
>>> os.path.abspath('')
'C:\\Users\\xian'

#假如想新建一个目录
# step1
os.path.join('\test','name')
#step 2创建一个目录
os.mkdir('\test\name')
# step3 删除一个目录
os.rmdir('\test\name')

#对于路径合并
os.path.join('','')
#对于路径拆分
>>> os.path.split('\sdjnf\dsf\sdf/file.text')
('\\sdjnf\\dsf\\sdf', 'file.text')

# 路径拆分获得文件拓展名
>>> os.path.splitext('/path/to/file.text')
('/path/to/file', '.text')
>>>
#文件重命名
os.rename('old','new')
#删除文件
os.remove('name')

#复制文件
1.可以用文件流实现复制
2.也可以用 *shutil*模块提供的copyfile()函数

#小练习
#当前文件夹下所有文件夹
>>> [x for x in os.listdir() if os.path.isdir(x)]
['.android', '.codeintel', '.config', '.eclipse', '.gradle', '.idea', '.idlerc', '.IntelliJIdea2017.2', '.jrebel', '.npminstall_tarball', '.oracle_jre_usage', ···， 'Recent', 'Saved Games', 'Searches', 'SendTo', 'source', 'Templates', 'Videos', '「开始」菜单', '新建文件夹']
#当前文件夹下的python文件
>>> [x for x in os.listdir() if os.path.isdir(x) and os.path.splitext(x)[1]=='.py']
[]
```

`练习1`:利用os模块编写一个能实现dir -1输出的程序

`练习2`:能在当前目录及当前目录的所有子目录下查找文件名包含自定字符串的文件，并打印相对路径

```python
#1
#2
import os
def findfile(path,filename):
    for x in os.listdir(path):
        x=os.path.join(path,x)
        if os.path.isdir(x):
            findfile(x,filename)
        else:
            if filename in os.path.split(x)[1]:
                print(x)
        return 
    
>>> findfile(os.path.abspath(''),'test')
>>> findfile(os.path.abspath(''),'a')
C:\Users\xian\.android\adbkey
>>> findfile(os.path.abspath(''),'b')
C:\Users\xian\.android\adbkey
>>> findfile(os.path.abspath(''),'an')
>>> findfile(os.path.abspath(''),'n')
>>> findfile(os.path.abspath(''),'a')
C:\Users\xian\.android\adbkey
>>>
```

## 序列化

**序列化：**把变量从内存中变为可存储可传输的过程，pickling

**反序列化：**把变量内容重新读取到内存的过程，unpickling

**JSON**：各种语言之间通用的交换格式

```python
#1 python对象转换为JSON
>>> import json
>>> d=dict(name='weberlee',age=10,score=90)
>>> json.dumps(d)
'{"name": "weberlee", "age": 10, "score": 90}'
#2 反序列化为Python对象
>>> json_str='{"name": "weberlee", "age": 10, "score": 90}'
>>> json.loads(json_str)
{'name': 'weberlee', 'age': 10, 'score': 90}
```

**对于class或者类的序列化：**

+ `json.dumps(对象，default=fun)`:传入一个default参数，返回固定格式

```python
def obj2dict(std):
    return {
        'name':std.name,
        'age':std.age,
        'score':std.score
    }

json.dumps(s,default=obj2stu)
```

+ 对于任意类，可以使用`default=lambda obj:obj.__dict__`

**任意对象的反序列化**：

+ 需要定义一个函数`dict2obj`:

```python
def dict2obj(d):
    return Student(d['name'],d['age'],d['score'])
```

