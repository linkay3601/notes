# Awk

样本文件：*employee.txt*，记录了编号、员工姓名和职位。

```txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```

样本文件：*employee-multiple-fs.txt*，记录了编号、员工姓名、职位和薪水（字段使用了不同的字符分割）。

```txt
101,John Doe:CEO%10000
102,Jason Smith:IT Manager%5000
103,Raj Reddy:Sysadmin%4500
104,Anand Ram:Developer%4500
105,Jane Miller:Sales Manager%3000
```

样本文件：*employee-one-line.txt*，记录了编号、员工姓名（以 ==:== 分割每一条记录）。

```txt
101,John Doe:102,Jason Smith:103,Raj Reddy:104,Anand Ram:105,Jane Miller
```

样本文件：*employee-change-fs-ofs.txt*，记录了编号、员工姓名和职位（每条记录用 ==\n-\n== 分割）。

```txt
101
John Doe
CEO
-
102
Jason Smith
IT Manager
-
103
Raj Reddy
Sysadmin
-
104
Anand Ram
Developer
-
105
Jane Miller
Sales Manager
```

## 1. Awk 命令语法

### 1.1 基本语法

```bash
awk -Fs '/pattern/ {action}' input-file
(or)
awk -Fs '{action}' intput-file
```

- -F 选项，用来指定字段分离器符号
- s 在这里代表字段分离器符号
- pattern 模式匹配
- action 指 Awk 命令
  
1. 打印记录中第一个字段

```bash
$ awk -F: '/mail/ { print $1 }' /etc/passwd
```

### 1.2 Awk 脚本使用

```
awk -f my-script.awk input-file
```

- -f 用来指定 Awk 脚本
  
### 1.3 BEGIN、Body 和 END

Awk 的完整语法

```bash
$ awk 'BEGIN { FS=":"; print "---header---"} /mail/ { print $1 } END { print "---footer---"}' /etc/passwd
```

只有 Body

```bash
$ awk -F: '{ print $1 }' /etc/passwd
```

BEGIN 和 Body

```bash
$ awk -F: 'BEGIN { print "UID"} { print $3 }' /etc/passwd
```

只有 BEGIN

```bash
$ awk 'BEGIN { print "Hello World!" }'
```

> 当仅指定 BEGIN 时，指定输入文件没有意义，因为只有 Body 块才会执行输入文件的每一行记录。但是可以利用这个特性来用 Awk 做一些其他的事（前提是这个事不依赖于输入文件），比如上面的 Hello World。
> 

### 1.4. 多个输入文件

```bash
awk 'BEGIN { FS=":"; print "---header---" } \
/mail/ { print $1 } \
END { print "---footer---" }' /etc/passwd /etc/group
```

### 1.5 print 命令

```bash
$ awk '{ print }' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```

> 默认情况下，不带参数的 print 命令打印整个记录。

1. 打印记录中第 2 个字段

```bash
$ awk '{ print $2 }' employee.txt
Doe,CEO
Smith,IT
Reddy,Sysadmin
Ram,Developer
Miller,Sales
```

> 默认情况下，字段分离器字符使用==空白字符==（空格、制表符等）。

### 1.6 { print } 和 { print $0 } 区别

`{ print }` 和 `{ print $0 }` 都是输出打印整个记录，所以下面的代码的效果是相同的。

```bash
$ awk '{ print }' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

$ awk '{ print $0 }' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```

### 1.7 模式匹配

1. 匹配有 Manager 的记录并打印记录的第 2、3 个字段

```bash
$ awk -F ',' '/Manager/ { print $2, $3 }' employee.txt
Jason Smith IT Manager
Jane Miller Sales Manager
```

2. 匹配以 102 开头的记录并打印第 2 个字段

```bash
$ awk -F ',' '/^102/ { print $2 }' employee.txt
Jason Smith
```

## 2. Awk 内置变量

### 2.1 FS

FS 内置变量在 Awk 中的作用是作为==输入字段分离器==（另一个选择是 `-F` 选项）。

### 2.2 基本用法

1. 打印以 ==,== 分割的第 2、3 个字段

```bash
$ awk 'BEGIN { FS="," } { print $2, $3 }' employee.txt
John Doe CEO
Jason Smith IT Manager
Raj Reddy Sysadmin
Anand Ram Developer
Jane Miller Sales Manager
```

2. BEGIN 块中存在多语句的情况（多了头信息和结束信息）

```bash
$ awk 'BEGIN { FS=","; \
print "-------------\nName\tTitle\n-------------" } \
{ print $2,"\t",$3; } \
END { print "-------------" }' employee.txt

-------------
Name    Title
-------------
John Doe         CEO
Jason Smith      IT Manager
Raj Reddy        Sysadmin
Anand Ram        Developer
Jane Miller      Sales Manager
-------------
```

### 2.3 正则表达式

1. 输出（打印）以 ==, :==或者 ==%== 分割的第 2、3 个字段

```bash
$ awk 'BEGIN { FS="[,:%]" } { print $2, $3 }' employee-multiple-fs.txt
John Doe CEO
Jason Smith IT Manager
Raj Reddy Sysadmin
Anand Ram Developer
Jane Miller Sales Manager
```

### 2.4 OFS

OFS 内置变量在 Awk 中的作用是作为==输出字段分离器==（与之对应的是 ==FS 输入字段分离器==）。默认情况下 Awk 把==空格==作为输出字段分离器，在每个输出字段之间都用==空格==分开。

1. 不使用 OFS 的情况

```bash
$ awk -F ',' '{ print $2, ":", $3 }' employee.txt  # : 前后有空格
John Doe : CEO
Jason Smith : IT Manager
Raj Reddy : Sysadmin
Anand Ram : Developer
Jane Miller : Sales Manager
```

2. 使用 OFS 的情况

```bash
$ awk -F ',' 'BEGIN { OFS=":" } { print $2, $3 }' employee.txt  # : 前后没有空格
John Doe:CEO
Jason Smith:IT Manager
Raj Reddy:Sysadmin
Anand Ram:Developer
Jane Miller:Sales Manager
```

### 2.5 RS

RS 内置变量在 Awk 中的作用是作为==输入记录分离器==。 记录在 Awk 中代表 Body 块每次处理的内容，默认情况下 Awk 把 ==\n== 作为输入记录分离器，把整个输入文件的内容用 ==\n== 分开交给 Body 块处理。

1. 将内容以 ==\n== 分割作为记录处理

```bash
$ awk -F, '{print $2}' employee-one-line.txt
John Doe:102
```

2. 将内容以 ==:== 分割作为记录处理

```bash
$ awk -F, 'BEGIN { RS=":" } { print $2 }' employee-one-line.txt
John Doe
Jason Smith
Raj Reddy
Anand Ram
Jane Miller
```

3. 将内容以 ==-\n== 分割作为记录处理（联合 FS 和 OFS 一起使用）

```bash
$ awk 'BEGIN { FS="\n"; RS="-\n"; OFS=":" } { print $2, $3 }' employee-change-fs-ofs.txt
John Doe:CEO
Jason Smith:IT Manager
Raj Reddy:Sysadmin
Anand Ram:Developer
Jane Miller:Sales Manager
```

### 2.6 ORS

ORS 内置变量在 Awk 中的作用是作为==输出记录分离器==（与之对应的是==RS 输入记录分离器==）。 记录在 Awk 中代表 Body 块每次处理的内容，默认情况下 Awk 把 ==\n== 作为输出记录分离器，在打印时每一条记录以 ==\n== 分割。

1. 打印时以 ==\n---\n== 分割每一条记录

```bash
$ awk 'BEGIN { FS=","; ORS="\n---\n" } { print $2, $3 }' employee.txt
John Doe CEO
---
Jason Smith IT Manager
---
Raj Reddy Sysadmin
---
Anand Ram Developer
---
Jane Miller Sales Manager
---
```

2. 打印时以 ==\n---\n== 分割每一条记录（联合 FS 和 OFS 一起使用）

```bash
$ awk 'BEGIN { FS=","; OFS="\n";ORS="\n---\n" } { print $1, $2, $3 }' employee.txt
101
John Doe
CEO
---
102
Jason Smith
IT Manager
---
103
Raj Reddy
Sysadmin
---
104
Anand Ram
Developer
---
105
Jane Miller
Sales Manager
---
```

### 2.7 NR
NR 内置变量在 Awk 中的作用是作为==记录数（Number of Records）==。在 Body 块中使用表示：第几个记录，而在 END 块中使用表示：记录总数。

```bash
$ awk 'BEGIN { FS="," } \
{ print "Emp Id of record number",NR,"is", $1 } \
END { print "Total number of records:", NR }' employee.txt

Emp Id of record number 1 is 101
Emp Id of record number 2 is 102
Emp Id of record number 3 is 103
Emp Id of record number 4 is 104
Emp Id of record number 5 is 105
Total number of records: 5
```

### 2.8 FILENAME

FILENAME 内置变量在 Awk 中的作用是在处理文件时，作为该文件的名字。**主要用在有多个输入文件的情况下**。
```bash
$ awk '{ print FILENAME }' employee.txt employee-multiple-fs.txt
employee.txt
employee.txt
employee.txt
employee.txt
employee.txt
employee-multiple-fs.txt
employee-multiple-fs.txt
employee-multiple-fs.txt
employee-multiple-fs.txt
employee-multiple-fs.txt
```

1. 当输入来自标准输入和管道 `|` 时，FILENAME 表示为：==-==

```bash
$ awk '{ print "Last name:", $2; print "Filename:", FILENAME }'  # 标准输入
$ echo "John Doe" |awk '{ print "Last name:", $2; print "Filename:", FILENAME }'  # 管道输入
Last name: Doe
Filename: -
```

> 这里假设输入为：John Doe

### 2.9 FNR

FNR 内置变量在 Awk 中的作用是作为==文件记录数（File Number of Records）==。因为当存在多个输入文件时，NR 会造成混乱（主要表现为**在跨文件处理的时候，NR 记录的数不会重置为 1**），所以才有了 FNR 来解决这个问题。

1. 使用 NR 的情况

```bash
$ awk 'BEGIN { FS="," } \
{ print FILENAME ": record number", NR, "is", $1 } \
END { print "Total number of records:", NR }' \
employee.txt employee-multiple-fs.txt

employee.txt employee-multiple-fs.txt
employee.txt: record number 1 is 101
employee.txt: record number 2 is 102
employee.txt: record number 3 is 103
employee.txt: record number 4 is 104
employee.txt: record number 5 is 105
employee-multiple-fs.txt: record number 6 is 101
employee-multiple-fs.txt: record number 7 is 102
employee-multiple-fs.txt: record number 8 is 103
employee-multiple-fs.txt: record number 9 is 104
employee-multiple-fs.txt: record number 10 is 105
Total number of records: 10
```

2. 使用 FNR 的情况

```bash
$ awk 'BEGIN { FS="," } \
{ print FILENAME ": record number", FNR, "is", $1 } \
END { print "Total number of records:", NR }' \
employee.txt employee-multiple-fs.txt

employee.txt employee-multiple-fs.txt
employee.txt: record number 1 is 101
employee.txt: record number 2 is 102
employee.txt: record number 3 is 103
employee.txt: record number 4 is 104
employee.txt: record number 5 is 105
employee-multiple-fs.txt: record number 1 is 101
employee-multiple-fs.txt: record number 2 is 102
employee-multiple-fs.txt: record number 3 is 103
employee-multiple-fs.txt: record number 4 is 104
employee-multiple-fs.txt: record number 5 is 105
Total number of records: 10
```