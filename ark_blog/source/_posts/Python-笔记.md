---
title: Python 笔记
date: 2021-02-13 10:28:07
categories:
- 后端
tags:
- Python
---

## 安装

- 各种Python的安装包，主要提供Linux版本的后缀是 `.whl` 和 `.tar.gz` ，可以搜索相关的包
  https://pypi.org/
- 各种Python的安装包，主要提供Windows版本的后缀是 `.whl` ，可以直接查找相关的包
  https://www.lfd.uci.edu/~gohlke/pythonlibs/

离线安装方法

`.whl` 文件安装如下（以 Numpy 为例）：

- linux 版本的安装（默认文件在当前目录下）

  ```bash
  pip install numpy-1.15.0-cp27-cp27mu-manylinux1_x86_64.whl
  ```

- Windows 版本的安装（默认文件在当前目录下）

  ```bash
  pip install numpy‑1.14.5+mkl‑cp27‑cp27m‑win_amd64.whl
  ```

`.tar.gz` 文件安装如下：

1. 文件解压

   - `.tar.gz` 文件解压

        ```bash
     tar -xzvf numpy-1.15.0.tar.gz
     ```

   - `.zip` 文件解压

        ```bash
     unzip numpy-1.15.0.zip
     ```

2. 进入目录 `numpy-1.15.0`

      ```bash
   cd numpy-1.15.0
   ```

   找到 `setup.py` 文件，然后安装

      ```bash
   python setup.py install
      ```

### 离线安装外部依赖包

1. 本地环境所有依赖

   ```bash
   pip freeze > requirements.txt
   ```
   
   单个依赖下所有依赖
   
   ```bash
   pip download <package_name>
   ```

2. 离线下载安装包

   下载单个离线包

   ```bash
   pip download -d your_offline_packages <package_name>
   ```

   批量下载离线包

   ```bash
   pip download -d your_offline_packages -r requirements.txt
   ```

3. 离线安装

   安装单个离线包

   ```bash
   pip install --no-index --find-links=/your_offline_packages/ package_name
   ```

   批量安装离线包

   ```bash
   pip install --no-index --find-links=/your_offline_packages/ -r requirements.txt
   ```

### 已安装仍报错ModuleNotFoundError: No module named 'xxx'

```bash
$ pip -V
pip 20.0.2 from D:\Studio\Development\Anaconda\lib\site-packages\pip (python 3.6)

$ pip3 -V
pip 21.0.1 from d:\studio\development\python\python36\lib\site-packages\pip (python 3.6)
```

可以看到 pip3 对应的是另一个路径里 python3.6 的安装位置

```bash
$ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
$ python get-pip.py
```

现在一样了

```bash
$ pip3 -V
pip 21.0.1 from d:\studio\development\anaconda\lib\site-packages\pip (python 3.6)

$ pip -V
pip 21.0.1 from d:\studio\development\anaconda\lib\site-packages\pip (python 3.6)
```

重新安装包，之后就能正常引入了

```bash
pip3.6 install xxx
```

### 安装 mysql

```python
import mysql.connector
```

不是 `pip3 install mysql` ，而是 `pip3 install mysql-connector`

## Selenium+PhantomJS

### 安装 selenium

查看当前selenium版本：

```bash
pip3 show selenium
```

卸载selenium：

```bash
pip3 uninstall selenium
```

安装指定版本：

```bash
pip3 install selenium==2.48.0
```

### Exception

#### selenium.common.exceptions.TimeoutException

连接超时

## 路径、文件名、后缀

### os.path

#### splitext()

使用 `os.path.splitext(file)[0]` 获得文件名

使用 `os.path.splitext(file)[-1]` 获得以 `.` 为开头的文件后缀名

```python
import os.path
filePath = 'http://www.cmse.gov.cn/xwzx/zhxw/202101/t20210113_47528.html'

# 获取前缀（文件名称）
print(os.path.splitext(filePath)[0])
# 获取后缀（文件类型）
print(os.path.splitext(filePath)[-1])
print(os.path.splitext(filePath)[-1][1:])

-------运行结果--------
http://www.cmse.gov.cn/xwzx/zhxw/202101/t20210113_47528
.html
html
```

#### basename()

```python
import os.path
filePath='http://www.cmse.gov.cn/xwzx/zhxw/202101/t20210113_47528.html'
    
print(os.path.basename(filePath))

-------运行结果--------
t20210113_47528.html
```

获取文件名不加后缀

```python
import os.path
filePath='http://www.cmse.gov.cn/xwzx/zhxw/202101/t20210113_47528.html'

print(".".join((os.path.basename(filePath).split('.')[0:-1])))

-------运行结果--------
t20210113_47528
```

#### dirname()

```python
import os.path
filePath='http://www.cmse.gov.cn/xwzx/zhxw/202101/t20210113_47528.html'

print(os.path.dirname(filePath))

-------运行结果--------
http://www.cmse.gov.cn/xwzx/zhxw/202101
```

### 其它

#### split()

```python
filePath='http://www.cmse.gov.cn/xwzx/zhxw/202101/t20210113_47528.html'

print(filePath.split(’\’)[-1])

-------运行结果--------
t20210113_47528.html
```
