### bash 的 IO 重定向和管道

#### IO 重定向

`>`  覆盖重定向  目标文件内的原有内容会被清除

`>>`追加重定向  新内容会追加至文件尾部

- 禁止将内容覆盖输出到已有文件中  只对当前 shell 进程有效

```bash
 ➜ root@localhost  ~  set -C
```

- 强制覆盖输出

  `>`转变为 `>|`

```bash
➜ root@localhost  ~  set +C
```

- 错误输出流
```bash
➜ root@localhost  ~  cat  /xxx/xxx 2> /yyy/yyy
```

  `2>`		覆盖重定向错误输出数据流

  `2>>`		追加重定向错误输出数据流

- 标准输出和错误输出各自定向到不同的位置

  ```bash
  COMMAND > /path/to/file.out   2>   /path/to/error.out
  ```

- 合并标准输出和错误输出为同一个数据流进行重定向

  ```bash
  &> 覆盖重定向
  &>> 追加重定向
  -------------------------------
  COMMAND > /path/to/file.out 2> &1
  COMMAND >> /path/to/file.out 2>> &1
  ```

`<`输入重定向    改变标准输入流 

```bash
➜ root@localhost  ~  tr 'a-z' 'A-Z' < /etc/fstab
```

- 补充

  `<<` 		HERE  Documentation     ————  此处文档

  ```bash
  ➜ root@localhost  ~ cat >> /tmp/test.out <<EOF
  ```

  上述代码将接下来进行键盘输入的字符定向到指定文件 `/tmp/test.out` 中   追加 or 覆盖都可以  

  EOF  为特定字符串  此之前所有字符都将被重定向

#### 管道

​	COMMAND-1 | COMMAND-2 | COMMAND-3    ......    

 Note : 最后一个命令会在当前 shell 进程的子 shell 进程中进行

将前一个命令的输出 做为下一个命令的输入进行处理    ————>   完成了多个命令的协作

```bash
tee 命令 -- 从标准输入读取输入  产生两路输出————>文件 and 屏幕/管道   ！！！覆盖输出 ！！！

tee [OPTION]...[FILE]
```

#### 文件处理工具

- `wc` 命令

  - ```bash
    wc [OPTION] ...[FILE]...
    
    wc -l lines
    wc -w words
    wc -c characters
    ```

  - 统计命令 

- `cut` 命令

  ``` bash
  cut [OPTION] ... [FILE]...
  	-d DELIMITER : 指明分隔符
  	-f FILEDS ：
  		# ： 精确地第#个字段
  		#，#[,#] 离散的多个字段  例如  1,3,6
  		#-# 连续的多个字段  例如 1-6
  		混合使用
  		1-3,7
  	--output-delimter = STRING
  ```

- `sort` 命令

  ```ba
  sort [OPTION]...[FILE]...
  
  	-f 忽略字符大小写
  	-r 逆序
  	-t DELIMITER 字段分隔符
  	-k # 以指定字段为标准排序
  	-n 以数值大小进行排序
  	-u uniq 排序后去重
  ```

- `uniq` 命令

  ```bash
  uniq [OPTION]...[FILE]...
  Note:连续且完全相同方为重复
  -c 显示每行重复出现的次数
  -d 仅显示重复过的行
  -u 仅显示不重复的行
  ```

    
