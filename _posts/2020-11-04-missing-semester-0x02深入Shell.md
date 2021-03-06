## Shell脚本

```shell
// 单引号、双引号区别：单引号直接引用变量；双引号引用变量内容
 ~  foo=bar
 ~  echo $foo
bar
 ~  echo "This is $foo"
Thi is bar
 ~  echo 'This is $foo'
This is $foo
```

### 函数

```shell
mcd () {
    mkdir -p "$1"
    cd "$1"
}
```

- `$0` - 脚本名
- `$1` 到 `$9` - 脚本到参数。 `$1` 是第一个参数，依此类推。
- `$@` - 所有参数
- `$#` - 参数个数
- `$?` - 前一个命令到返回值(非0标识有错误发生)
- `$$` - 当前脚本到进程识别码
- `!!` - 完整到上一条命令，包括参数。常见应用：当你因为权限不足执行命令失败时，可以使用 `sudo !!`再尝试一次。
- `$_` - 上一条命令的最后一个参数。如果你正在使用的是交互式shell，你可以通过按下 `Esc` 之后键入 . 来获取这个值

### 条件判断

```shell
 ~/Desktop/Code/ShellDemo/mcd  false || echo "Oops, fail"
Oops, fail
 ~/Desktop/Code/ShellDemo/mcd  true || echo "Will not printed"
 ~/Desktop/Code/ShellDemo/mcd  true && echo "Things went well"
Things went well
 ~/Desktop/Code/ShellDemo/mcd  false && echo "Will not be printed"
 ✘  ~/Desktop/Code/ShellDemo/mcd  false ; echo "This will always run"
This will always run
```

比较两个命令执行结果的区别

```shell
# 首先，分别执行 ls foo 和 ls bar命令，然后将结果分别保存到2个临时文件中，最后调用diff比较2个文件区别
diff <(ls foo) <(ls bar)
```

### 重定向标识

```shell
> file 重定向标准输出到文件
1> file 重定向标准输出到文件

2> file 重定向标准错误到文件

&> file 重定向标准输出和标准错误到文件
> file 2>&1 重定向标准输出和标准错误到文件

/dev/null 空设备文件(黑洞文件
```

### 通配符

- 使用 ? 和 * 来匹配一个或任意个字符
- 使用 {} 可以自动展开括号中内容

```shell
convert image.{png,jpg}
# 会展开为
convert image.png image.jpg

cp /path/to/project/{foo,bar,baz}.sh /newpath
# 会展开为
cp /path/to/project/foo.sh /path/to/project/bar.sh /path/to/project/baz.sh /newpath

# 也可以结合通配使用
mv *{.py,.sh} folder
# 会移动所有 *.py 和 *.sh 文件

mkdir foo bar

# 下面命令会创建foo/a, foo/b, ... foo/h, bar/a, bar/b, ... bar/h这些文件
touch {foo,bar}/{a..h}
touch foo/x bar/y
# 显示foo和bar文件的不同
diff <(ls foo) <(ls bar)
# 输出
# < x
# ---
# > y
```

### Shell语法检查工具

```shell
brew install shellCheck
```

### 使用其他脚本语言

```python
#!/usr/bin/env python 
import sys
for arg in reversed(sys.argv[1:]):
  	print(arg)
```

### shell函数和脚本的不同点

- 脚本文件首行 “#!/usr/bin/env python"叫做shebang，可以自动从env中寻找语言执行文件进行脚本文件的执行

- 函数仅在定义时被加载，脚本会在每次被执行时加载。这让函数的加载比脚本略快一些，但每次修改函数定义，都要重新加载一次。

- 函数会在当前的shell环境中执行，脚本会在单独的进程中执行。因此，函数可以对环境变量进行更改，比如改变当前工作目录，脚本则不行。脚本需要使用 [`export`](http://man7.org/linux/man-pages/man1/export.1p.html) 将环境变量导出，并将值传递给环境变量。

- 与其他程序语言一样，函数可以提高代码模块性、代码复用性并创建清晰性的结构。shell脚本中往往也会包含它们自己的函数定义。

  ### shell示例可参考

  -  [TLDR pages](https://tldr.sh/)，是一个很不错的替代品，它提供了一些案例，可以帮助您快速找到正确的选项

    

  ### 查找文件

  ```shell
  # 查找所有名称为src的文件夹
  find . -name src -type d
  # 查找所有文件夹路径中包含test的python文件
  find . -path '**/test/**/*.py' -type f
  # 查找前一天修改的所有文件
  find . -mtime -1
  # 查找所有大小在500k至10M的tar.gz文件
  find . -size +500k -size -10M -name '*.tar.gz'
  ```

  ### 处理查找到的文件

  ```she
  # Delete all files with .tmp extension
  find . -name '*.tmp' -exec rm {} \;
  # Find all PNG files and convert them to JPG
  find . -name '*.png' -exec convert {} {.}.jpg \;
  ```

  其他相关命令：fd可以取代find；locate维护一个数据库，每日更新，可以提高查询效率

  ### 查找代码

  ```shell
  # grep 与改进后的 grep
  # 查找所有使用了 requests 库的文件
  rg -t py 'import requests'
  # 查找所有没有写 shebang 的文件（包含隐藏文件）
  rg -u --files-without-match "^#!"
  # 查找所有的foo字符串，并打印其之后的5行
  rg foo -A 5
  # 打印匹配的统计信息（匹配的行和文件的数量）
  rg --stats PATTERN
  ```

  ### 查找shell命令

  ```shell
  1. history命令
  2. 使用 Ctrl+R 命令对命令历史进行回溯
  3. Ctrl+R 还可以和 fzf 使用
  ```

  ### 文件夹导航

  - 使用[`fasd`](https://github.com/clvv/fasd)可以查找最常用和/或最近使用的文件和目录
  - 目录结构可使用tree、broot
  - 文件管理器可使用 nnn 或 ranger

  

  