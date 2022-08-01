

# Sed

Sed 脚本遵循 REPR（Read-Execute-Print-Repeat，读取-执行-打印-重复）的执行流程。

样本文件 _employee.txt_，记录了编号、员工姓名和职位。

```tex
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```

## 1. Sed 语法和基础命令

### 1.1 基本使用

```bash
$ sed [options] {sed-commands} {input-file}
```

- options 是 Sed 命令选项
- sed-commands 指 Sed 命令
- input-file 指定的输入文件

1. 打印文件 /etc/passwd 中的内容

```bash
$ sed -n 'p' /etc/passwd
```

### 1.2 -e 选项

```bash
$ sed [options] -e {sed-command-1} -e {sed-command-2} {input-file}
```

- -e Sed 选项，可以连接多个 Sed 命令

1. 打印以 root 或 nobody 字母开头的记录

```bash
$ sed -n -e '/^root/ p' -e '/^nobody/ p' /etc/passwd
```

2. 打印以 root 或 nobody 字母开头的记录(多行写法)

```bash
$ sed -n \
-e '/^root/ p' \
-e '/^nobody/ p' \
/etc/passwd
```

### 1.3 { } 语法

```bash
$ sed [options] '{
sed-command-1
sed-command-2
}' input-file
```

- { } 是一个语法，也可以连接多个 Sed 命令

1. 打印以 root 或 nobody 字母开头的记录

```bash
$ sed -n '{
/^root/ p
/^nobody/ p
}' /etc/passwd
```

> Sed 不会修改原始文件，它总是打印结果到标准输出，如果你想要保存修改的结果，可以考虑使用重定向符号到一个新的文件（或者使用 -i 选项）
> 

## 2. p 命令

该命令会打印 Pattern Space（一个术语，在 Sed 中指**内置的临时缓存空间**，可以理解成记录或行）。

### 2.1 基本使用

1. 打印 _employee.txt_ 中的内容

```bash
$ sed -n 'p' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```

### 2. 2 地址区间

1. 打印第 2 行的内容

```bash
$ sed -n '2 p' employee.txt
102,Jason Smith,IT Manager
```

2. 打印文件第 1 行至第 4 行的内容

```bash
$ sed -n '1,4 p' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
```

> 这里的区间是一个闭区间（包含左侧并且包含右侧）

3. 打印第 2 行至最后 1 行的内容

```bash
$ sed -n '2,$ p' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```

  - $ 代表到最后一行

4. 打印从第 2 行开始往后数 2 行的内容

```bash
$ sed -n '2,+2 p' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
```

> m,+n 表示从 m 行开始往后数 n 行

5. 打印单数行的内容

```bash
$ sed -n '1~2 p' employee.txt
101,John Doe,CEO
103,Raj Reddy,Sysadmin
105,Jane Miller,Sales Manager
```

> n~m 表示从 n 行开始每次跳过 m 行
> 

### 2.3 模式匹配
1. 匹配有 Jane 的行并打印

```bash
$ sed -n '/Jane/ p' employee.txt
105,Jane Miller,Sales Manager
```

2. 从前 4 行中匹配有 Jason 的行并打印这一行以及前 4 行中余下的行

```bash
$ sed -n '/Jason/,4 p' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
```

>**这里不好理解**，如果前 4 行中没有匹配到 Jason，那么会在第 4 行之后的内容中匹配有 Jason 的行打印。

3. 匹配有 Raj 的行并打印这一行至最后一行

```bash
$ sed -n '/Raj/,$ p' employee.txt
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```

4. 匹配有 Raj 的行并从这一行开始打印至匹配有 Jane 的行

```bash
$ sed -n '/Raj/,/Jane/ p' employee.txt
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```

5. 匹配有 Jason 的行打印从这一行开始往后数 2 行

```bash
$ sed -n '/Jason/,+2 p' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
```

## 3. d 命令

该命令会删除 Pattern Space，但是注意，**Sed 并不会真正的删除（除非指定相应的选项）**。

> 当有多个 Sed 命令时，如果遇到 d 命令，那么整个匹配到的行将会被删除，并且在被删除的行上将不再执行任何 Sed 命令。

1. 删除文件 *employee.txt* 的内容

```bash
$ sed 'd' employee.txt

```

2. 删除第 2 行

```bash
$ sed '2 d' employee.txt
101,John Doe,CEO
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```

3. 删除第 1 行至第 4 行

```bash
$ sed '1,4 d' employee.txt
105,Jane Miller,Sales Manager
```

4. 删除从第 2 行开始至最后 1 行

```bash
$ sed '2,$ d' employee.txt
101,John Doe,CEO
```

5. 删除单数行

```bash
$ sed '1~2 d' employee.txt
102,Jason Smith,IT Manager
104,Anand Ram,Developer
```

6. 删除匹配有 Manager 的行
```bash
$ sed '/Manager/ d' employee.txt
101,John Doe,CEO
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
```

7. 从前 4 行匹配有 Jason 的行并删除这一行以及前 4 行中余下的行

```bash
$ sed '/Jason/,4 d' employee.txt
101,John Doe,CEO
105,Jane Miller,Sales Manager
```

> **这里不好理解**，如果前 4 行中没有匹配到 Jason，那么会在第 4 行之后的内容中匹配有 Jason 的行并删除。

8. 匹配有 Raj 的行并删除这一行至最后一行

```bash
$ sed '/Raj/,$ d' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
```

9. 匹配有 Raj 的行并从这一行开始删除至匹配有 Jane 的行

```bash
$ sed '/Raj/,/Jane/ d' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
```

10. 匹配有 Jason 的行并删除从这一行开始往后数 2 行

```bash
$ sed '/Jason/,+2 d' employee.txt
101,John Doe,CEO
105,Jane Miller,Sales Manager
```

### 3.1 小技巧

1. 删除所有的空行

```bash
$ sed '/^$/ d' employee.txt
```

2. 删除所有注释行（假设注释是以 `#` 开头）

```bash
$ sed '/^#/ d' employee.txt
```

## 4. w 命令

该命令会将 Pattern Space 写入到指定的文件中，可以实现筛选备份等功能。

> 你可能不会频繁的使用到 `w` 命令，可以考虑用重定向符号 `>` 来代替。

1. 输出文件 *employee.txt* 的内容到 *output.txt*

```bash
$ sed -n 'w output.txt' employee.txt
```

2. 输出第 2 行内容到文件

```bash
$ sed -n '2 w output.txt' employee.txt
```

3. 输出第 1 行至第 4 行到文件

```bash
$ sed -n '1,4 w output.txt' employee.txt
```

4. 输出从第 2 行开始至最后 1 行到文件

```bash
$ sed -n '2,$ w output.txt' employee.txt
```

5. 输出单数行到文件

```bash
$ sed -n '2,$ w output.txt' employee.txt
```

6. 输出匹配有 Jane 的行到文件

```bash
$ sed -n '/Jane/ w output.txt' employee.txt
```

7. 从前 4 行匹配有 Jason 的行并输出这一行以及前 4 行中余下的行到文件

```bash
$ sed -n '/Jason/,4 w output.txt' employee.txt
```

> **这里不好理解**，如果前 4 行中没有匹配到 Jason，那么会在第 4 行之后的内容中匹配有 Jason 的行并输出到文件。

8. 匹配有 Raj 的行并输出这一行至最后一行到文件

```bash
$ sed -n '/Raj/,$ w output.txt' employee.txt
```

9. 匹配有 Raj 的行并从这一行开始输出至匹配有 Jane 的行到文件

```bash
$ sed -n '/Raj/,/Jane/ w output.txt' employee.txt
```

10. 匹配有 Jason 的行并输出从这一行开始往后数 2 行到文件
```bash
$ sed -n '/Raj/,/Jane/ w output.txt' employee.txt
```