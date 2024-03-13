# SCons 教程概述

## 1. 基本使用

- SConstruct 文件是与 Makefile 功能相似的配置文件，但采用 Python 脚本书写。SCons 读取这个文件时，会执行其中的 Python 脚本命令，具体的编译过程则由 SCons 根据情况确定。
- 示例代码：
  - `Program('hello.c')`：编译单个源文件。
  - `Program('new_hello', 'hello_c')`：编译单个源文件，显式指定目标文件。
  - `Program(['prog.c', 'file1.c', 'file2.c'])`：编译多个源文件。
  - `Program('program', ['prog.c', 'file1.c', 'file2.c'])`：指定目标文件，编译多个源文件。
  - `Program('program', Glob('*.c'))`：使用 Glob 模式匹配源文件。
  - `Program('program', Split('main.c file.c file2.c'))`：使用 Split 更清晰地指定多个源文件。
  - 分割字符串指定多个源文件：
    ```python
    src_files = Split("""main.c
    file1.c
    file2.c""")
    Program(target='program', source=src_files)
    ```

## 2. 编译多个目标文件

- 多次调用 `Program()` 以编译多个目标文件。

## 3.8 多个程序共享源文件

- 示例：
  - 使用 Split 函数共享源文件：
    ```python
    Program(Split('foo.c common1.c common2.c'))
    Program('bar', Split('bar1.c bar2.c common1.c common2.c'))
    ```
  - 使用列表添加共享源文件：
    ```python
    common = ['common1.c', 'common2.c']
    foo_files = ['foo.c'] + common
    bar_files = ['bar1.c', 'bar2.c'] + common
    Program('foo', foo_files)
    Program('bar', bar_files)
    ```

## 4. 库编译和链接

### 4.1 库编译

- `Library('foo', ['f1.c', 'f2.c', 'f3.c'])`：编译库文件。
- `Library('foo', ['f1.c', 'f2.o', 'f3.c', 'f4.o'])`：混合使用源文件和 `.o` 文件。
- `StaticLibrary('foo', ['f1.c', 'f2.c', 'f3.c'])`：显式编译静态库。
- `SharedLibrary('foo', ['f1.c', 'f2.c', 'f3.c'])`：编译动态库。

### 4.2 库链接

- 示例：`Program('prog.c', LIBS=['foo', 'bar'], LIBPATH='.')`，不需指明库的前缀和后缀。

### 4.3 找到库：`$LIBPATH` 变量

- 示例：`Program('prog.c', LIBS = 'm', LIBPATH = ['/usr/lib', '/usr/local/lib'])`

## 5. 节点对象（Nodes）

- SCons 将所有文件和目录表示为节点（Node）。
- Builder 方法返回节点对象列表，这允许进行列表操作。

## 6. 依赖关系

- 使用 `Decider` 函数确定文件是否被修改，可以基于 MD5 或时间戳。
- `$CPPPATH` 变量添加编译时的 `-I` 包含路径，实现隐含依赖。
- 显式依赖和总是编译的设置。

## 7. 环境

- 创建环境，配置编译器和编译选项。
- 获取和打印环境变量。

## 13. 安装

- 将编译好的程序安装到指定目录。
- 支持将多个文件安装到同一目录，或以不同名称安装单个文件。

## 14. 平台独立的文件系统操作

- 使用 `Command` 进行文件复制。

## 16. 多层目录构建

- 使用 `SConscript` 文件在不同目录层级组织构建配置。
- `SConscript` 文件可包含其他 `SConscript` 文件，实现层次化构建管理。