---
title: 使用XHProf查找PHP性能瓶颈
date: 2018-08-07 19:19:17
tags:
 - php
 - Linux
 - XHProf
categories:
 - php

---


XHProf是facebook 开发的一个测试php性能的扩展，本文记录了在PHP应用中使用XHProf对PHP进行性能优化，查找性能瓶颈的方法。

看完很多博客，整理了一下，以及自己踩得坑。

### 1.安装Xhprof扩展

```angular2html
$ wget http://pecl.php.net/get/xhprof-0.9.4.tgz
$ tar -zxvf xhprof-0.9.4.tgz 
$ cd xhprof-0.9.4$ cd extension/
$ phpize
$ ./configure
$ make
$ sudo make install
```
<!--more-->
### 2.修改php.ini
```angular2html
[xhprof]
extension=xhprof.so


xhprof.output_dir=/tmp
```
> 配置中xhprof.output_dir指定了生成的profile文件存储的位置，我将其指定为/tmp。**

### 3.对PHP进行性能分析
在XHProf扩展中，一共提供了四个函数用于对PHP进行性能分析。

<front color='red'> xhprof_enable/xhprof_sample_enable </front>函数用于开始XHProf性能分析，区别在于前者功能更加强大，而后者则是是以简单模式启动性能分析（简单记录了函数的调用栈信息），开销比较小。

xhprof_disable/xhprof_sample_disable函数用于停止性能分析，并返回分析的数据。

需要特别说明的函数是xhprof_enable，其他函数都是不需要提供参数的，而该函数则可以接受两个可选的参数，用于改变该工具的行为。

```angular2html
void xhprof_enable ([ int $flags = 0 [, array $options ]] )
```

- flags 该参数用于为剖析结果添加额外的信息，该参数的值使用以下宏，如果需要提供多个值，使用|进行分隔。

- XHPROFFLAGSNO_BUILTINS 跳过所有的内置函数

- XHPROFFLAGSCPU 添加对CPU使用的分析

- XHPROFFLAGSMEMORY 添加对内存使用的分析

- options 数组形式提供可选参数，在此处提供ignored_functions选项需要忽略的函数

比如下面的例子，同时对内存和CPU进行分析，并且忽略对call_user_func和call_user_func_array函数的分析。
```angular2html
xhprof_enable(
    XHPROF_FLAGS_MEMORY|XHPROF_FLAGS_CPU,
    [
        'ignored_functions'    => ['call_user_func','call_user_func_array']
    ]
);

- 这里是PHP代码，比如业务逻辑实现等要被分析的代码部分....$xhprofData = xhprof_disable();
- $xhprofData是数组形式的分析结果print_r($xhprofData);
```

>注意，如果使用XHPROF_FLAGS_CPU选项对CPU占用也进行分析，在Linux环境下，会造成比较高的系统负载，因此不建议使用，而推荐只使用XHPROF_FLAGS_MEMORY，对内存的分析不会对系统造成太多负载。

### 4.形象化的查看分析结果
使用xhprof_disable完成性能分析并且获取到分析结果之后，我们通常不会直接输出结果，因为这样的结果是以数组形式组织的，看起来并不直观，幸运的是，xhprof提供了基于web的图形界面对分析结果进行查看。

数组输出的结果：

```php
array (size=5)
  'LiuqhController::foreachAction==>var_dump' => 
    array (size=2)
      'ct' => int 7
      'wt' => int 647
  'main()==>LiuqhController::foreachAction' => 
    array (size=2)
      'ct' => int 7
      'wt' => int 1400101
  'main()==>time' => 
    array (size=2)
      'ct' => int 1
      'wt' => int 3
  'main()==>xhprof_disable' => 
    array (size=2)
      'ct' => int 1
      'wt' => int 1
  'main()' => 
    array (size=2)
      'ct' => int 1
      'wt' => int 1400233
      .....
```
直接输出结果中的含义：
```
ct 函数调用次数，
wt 花费的时间，
cpu 花费的 CPU 时间(微秒即百万分之一秒)，
mu 使用的内存(bytes)，
pmu 使用的内存峰值(bytes)

```
*在使用之前，请先确保服务器安装了graphviz工具，否则在生成监控图表的时候回出现以下错误:*
```
failed to execute cmd: " dot -Tpng". stderr: `sh: dot: command not found '
```
这里提示找不到dot命令，所以需要先安装graphviz

解决方法：
```
$ sudo yum install graphviz
```
---

### 5.graphviz安裝

- Graphviz 支援 Windows、Mac OS X、FreeBSD、Solaris、Linux 等多種作業系統。

- 若您是 Linux 使用者，基於這款軟體的名氣，您的套件管理器中幾乎一定會有，從套件庫中安裝吧！倘若真找不到，請看官網下載頁面，試試原始碼。

- 若您是 Windows 用戶，請前往這裡下載安裝檔：http://www.graphviz.org/Download_windows.php

- Mac OS X 的使用者請往這邊走：http://www.graphviz.org/Download_macos.php

---
由于分析结果的查看工具是基于web的，因此，我们需要将xhprof安装包中的xhprofhtml和xhproflib目录放到服务器的web目录下，让xhprof_html目录中的内容对外可以访问。

比如我的测试服务器环境是使用vagrant搭建的Cent OS，我见过这两个目录放到/vagrant/xhprof目录下：

```
[vagrant@localhost xhprof]$ pwd/vagrant/xhprof
[vagrant@localhost xhprof]$ ls
xhprof_html  xhprof_lib
```

web服务器使用的是Nginx，因此，在Nginx的配置文件nginx.conf中的配置如下：

```
server {
    listen       80;
    server_name  _;
    root /vagrant;
    ...
    
```

web服务器的根目录是/vagrant，因此访问地址为http://localhost/xhprof/xhprof_html/index.php.

当然，配置好环境之后，我们还是获取不到分析结果的，因为我们在代码中并没有将分析结果保存到xhprof.output_dir指定的目录中。

因此，我们需要修改我们的代码，是其能够将分析结果存放到xhprof.output_dir指定的目录中。

```$xslt

....$xhprofData = xhprof_disable();
require '/vagrant/xhprof/xhprof_lib/utils/xhprof_lib.php';
require '/vagrant/xhprof/xhprof_lib/utils/xhprof_runs.php';
$xhprofRuns = new XHProfRuns_Default();
$runId = $xhprofRuns->save_run($xhprofData, 'xhprof_test');


echo 'http://localhost/xhprof/xhprof_html/index.php?run=' . $runId . '&source=xhprof_test';
```
- 变量$runId是本次请求生成分析结果的id，最后我们输出了一个链接地址，使用改地址就可以看到本次请求的分析结果。

- funciton name ： 函数名

- calls: 调用次数

- Incl. Wall Time (microsec 微妙)： 函数运行时间（包括子函数）

- IWall%：函数运行时间（包括子函数）占比

- Excl. Wall Time(microsec 微妙)：函数运行时间（不包括子函数）

- EWall%：函数运行时间（不包括子函数）

下面是一些参数说明

<p>
Inclusive Time                 包括子函数所有执行时间。

Exclusive Time/Self Time  函数执行本身花费的时间，不包括子树执行时间。

Wall Time                        花去了的时间或挂钟时间。

CPU Time                        用户耗的时间+内核耗的时间

Inclusive CPU                  包括子函数一起所占用的CPU

Exclusive CPU                  函数自身所占用的CPU

所有参数说明

Function Name 函数名

   Calls 调用次数

   Calls% 调用百分比

   Incl. Wall Time (microsec) 调用的包括子函数所有花费时间 以微秒算(一百万分之一秒)

   IWall% 调用的包括子函数所有花费时间的百分比

   Excl. Wall Time (microsec) 函数执行本身花费的时间，不包括子树执行时间,以微秒算(一百万分之一秒)

   EWall% 函数执行本身花费的时间的百分比，不包括子树执行时间

   Incl. CPU(microsecs) 调用的包括子函数所有花费的cpu时间。减Incl. Wall Time即为等待cpu的时间

   减Excl. Wall Time即为等待cpu的时间

   ICpu% Incl. CPU(microsecs)的百分比

   Excl. CPU(microsec) 函数执行本身花费的cpu时间，不包括子树执行时间,以微秒算(一百万分之一秒)。

   ECPU% Excl. CPU(microsec)的百分比

   Incl.MemUse(bytes) 包括子函数执行使用的内存。

   IMemUse% Incl.MemUse(bytes)的百分比

   Excl.MemUse(bytes) 函数执行本身内存,以字节算

   EMemUse% Excl.MemUse(bytes)的百分比

   Incl.PeakMemUse(bytes) Incl.MemUse的峰值

   IPeakMemUse% Incl.PeakMemUse(bytes) 的峰值百分比

   Excl.PeakMemUse(bytes) Excl.MemUse的峰值

   EPeakMemUse% EMemUse% 峰值百分比


</p>

![avatar](/images/xhprof1.png)

注意到中间的<front color=red>View Full Callgraph </front>链接，通过该链接我们可以看到图形化的分析结果。

![avatar](/images/xhprof2.png)

>图中红色的部分为性能比较低，耗时比较长的部分，我们可以根据根据哪些函数被标记为红色对系统的代码进行优化


---
### 6.本人踩的坑

使用xhprof时，图片查看时[View Full Callgraph]，出现以下错误

```$xslt
failed to execute cmd: " dot -Tpng". stderr: `sh: dot: command not found '
```
各种goole，stackoverflow，说需要安装graphviz，好了，本人mac使用brew安装

```$xslt
brew install graphviz
```
安装成功了，再试，依然提示以上错误。

而我直接在命令行输入 dot -V是正常的

```$xslt
bash-3.2# dot -Vdot - graphviz version 2.38.0 (20140413.2041)
```

那么就是php运行环境有问题了，然后就排查 xhprof的 callgraph.php，最后定位到 <front color=red> xhprof/xhprof_lib/utils/callgraph_utils.php  的 xhprof_generate_image_by_dot </front> 函数

```$xslt
function xhprof_generate_image_by_dot($dot_script, $type) {
  $descriptorspec = array(
       // stdin is a pipe that the child will read from
       0 => array("pipe", "r"),
       // stdout is a pipe that the child will write to
       1 => array("pipe", "w"),
       // stderr is a pipe that the child will write to
       2 => array("pipe", "w")
       );

  $cmd = " dot -T".$type;
  $process = proc_open( $cmd, $descriptorspec, $pipes, sys_get_temp_dir(), array( 'PATH' => getenv( 'PATH' ) ) );
  if (is_resource($process)) {
    fwrite($pipes[0], $dot_script);
    fclose($pipes[0]);

    $output = stream_get_contents($pipes[1]);

    $err = stream_get_contents($pipes[2]);
    if (!empty($err)) {
      print "failed to execute cmd: \"$cmd\". stderr: `$err'\n";
      exit;
    }

    fclose($pipes[2]);
    fclose($pipes[1]);
    proc_close($process);
    return $output;
  }
  print "failed to execute cmd \"$cmd\"";
  exit();}
```
问题出现在callgraph_utils.php 112行：

```$xslt
proc_open( $cmd, $descriptorspec, $pipes, sys_get_temp_dir(), array( 'PATH' => getenv( 'PATH' ) ) );
```

其中
```$xslt
getenv( 'PATH' ) 输出为：/usr/bin:/bin:/usr/sbin:/sbin
```

而使用brew 安装 的dot在

```$xslt
/usr/local/bin/dot
```
所以我追加了 在后面如下：/usr/local/bin
```$xslt
proc_open( $cmd, $descriptorspec, $pipes, sys_get_temp_dir(), array( 'PATH' => getenv( 'PATH' ).':/usr/local/bin' ) );
```

