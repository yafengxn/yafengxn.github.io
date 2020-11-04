## 数据获取

通过日志的方式进行数据获取，比如Mac上有```log show```来展示系统日志

## 数据操作

#### 常用数据操作shell命令：

* ```grep```, 查找内容指定存在指定内容的行
* ```less```, 用于数据的分页显示，数据量较大时使用该命令

- ```sed```, 进行文本操作的重要工具，支持正则表达式
- ```xargs```,将参数列表转换成小块分段传递给其他命令，以避免参数列表过长的问题
- ```tr```, 用于拆分字符串
- ```var=$(pwd | sed -e xxx)```, 使用$(xxx)获取表达式替换的值，传给var变量

## 正则表达式

#### 正在表达式中字符所代表含义

- `.` 除空格之外的”任意单个字符”
- ```?```匹配前面的子表达式零次或一次
- `*` 匹配前面字符零次或多次
- `+` 匹配前面字符一次或多次
- `[abc]` 匹配 `a`, `b` 和 `c` 中的任意一个
- `(RX1|RX2)` 任何能够匹配`RX1` 或 `RX2`的结果
- `^` 行首
- `$` 行尾

注意: sed的正则表达式有些时候是比较奇怪的，它需要你在这些模式前添加```\```才能使其具有特殊含义。或者，您也可以添加`-E`选项来支持这些匹配

## 数据整理

#### 常用数据整理命令：

* ```sort```, 用于数据的排序，```-n```按照数字顺序进行排序，默认是按照字典序, `-k1,1` 则表示“仅基于以空格分割的第一列进行排序”, ```-r```倒序排序
* ```uniq```, 用于数据的去重, ```-c```会把连续出现的行折叠为一行并使用出现次数作为前缀
* ```head```, 筛选头部数据, ```-n```指定显示条数

- ```tail```, 筛选尾部数据, ```-n```指定显示条数
- ```awk```, 提取空格分隔的参数, ```$0```代表整行, ```$1```代表第一个匹配的参数
- ```paste```, ```-sd,```使用```,```将传入参数连接到一起
- ```wc```, 用于数据统计
- ```bc```, 用于数值计算

#### awk是另外一种编辑器

`awk` 其实是一种编程语言，只不过它碰巧非常善于处理文本。

首先， `{print $2}` 的作用是什么？ `awk` 程序接受一个模式串（可选），以及一个代码块，指定当模式匹配时应该做何种操作。默认当模式串即匹配所有行（上面命令中当用法）。 在代码块中，`$0` 表示正行的内容，`$1` 到 `$n` 为一行中的 n 个区域，区域的分割基于 `awk` 的域分隔符（默认是空格，可以通过`-F`来修改）

不过，既然 `awk` 是一种编程语言，那么则可以这样：

```shell
BEGIN { rows = 0 }
$1 == 1 && $2 ~ /^c[^ ]*e$/ { rows += $1 }
END { print rows }
```

`BEGIN` 也是一种模式，它会匹配输入的开头（ `END` 则匹配结尾）。然后，对每一行第一个部分进行累加，最后将结果输出。

## 分析数据

想做数学计算也是可以的！例如这样，您可以将每行的数字加起来：

```
 | paste -sd+ | bc -l
```

下面这种更加复杂的表达式也可以：

```
echo "2*($(data | paste -sd+))" | bc -l
```

您可以通过多种方式获取统计数据。如果已经安装了R语言，[`st`](https://github.com/nferraz/st)是个不错的选择：

```
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | awk '{print $1}' | R --slave -e 'x <- scan(file="stdin", quiet=TRUE); summary(x)'
```

R 也是一种编程语言，它非常适合被用来进行数据分析和[绘制图表](https://ggplot2.tidyverse.org/)。这里`summary` 可以打印统计结果。我们通过输入的信息计算出一个矩阵，然后R语言就可以得到我们想要的统计数据。

如果您希望绘制一些简单的图表， `gnuplot` 可以帮助到您：

```
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | sort -nk1,1 | tail -n10
 | gnuplot -p -e 'set boxwidth 0.5; plot "-" using 1:xtic(2) with boxes'
```

## 利用数据整理来确定参数

有时候您要利用数据整理技术从一长串列表里找出你所需要安装或移除的东西。我们之前讨论的相关技术配合 `xargs` 即可实现：

```shell
rustup toolchain list | grep nightly | grep -vE "nightly-x86" | sed 's/-x86.*//' | xargs rustup toolchain uninstall
```

这里我们将整理好的数据调用```xargs```，相当于对每条数据调用一次```rustup toochain uninstall```

## 整理二进制数据

虽然到目前为止我们的讨论都是基于文本数据，但对于二进制文件其实同样有用。例如我们可以用 ffmpeg 从相机中捕获一张图片，将其转换成灰度图后通过SSH将压缩后的文件发送到远端服务器，并在那里解压、存档并显示。

```shell
ffmpeg -loglevel panic -i /dev/video0 -frames 1 -f image2 -
 | convert - -colorspace gray -
 | gzip
 | ssh mymachine 'gzip -d | tee copy.jpg | env DISPLAY=:0 feh -'
```