---
title: PHP-并发请求
date: 2019-03-22 12:19:23
tags:
 - php
categories:
 - 后端编程
---

> <front color="red">进程是资源分配的最小单位，线程是CPU调度的最小单位</front>

## 1. 多线程并发:通过cURL并发请求  
### 1.1 通过curl_multi实现
[PHP cURL所有函数列表](https://secure.php.net/manual/zh/ref.curl.php)

**并发中用的curl_multi_\*相关函数**
- [curl_multi_add_handle](https://php.net/manual/zh/function.curl-multi-add-handle.php)— 向curl批处理会话中添加单独的curl句柄
- [curl_multi_close](https://secure.php.net/manual/zh/function.curl-multi-close.php) — 关闭一组cURL句柄
- [curl_multi_exec](https://secure.php.net/manual/zh/function.curl-multi-exec.php) — 运行当前 cURL 句柄的子连接
- [curl_multi_getcontent](https://secure.php.net/manual/zh/function.curl-multi-getcontent.php) — 如果设置了CURLOPT_RETURNTRANSFER，则返回获取的输出的文本流
- [curl_multi_info_read](https://secure.php.net/manual/zh/function.curl-multi-info-read.php) — 获取当前解析的cURL的相关传输信息
- [curl_multi_init](https://secure.php.net/manual/zh/function.curl-multi-init.php) — 返回一个新cURL批处理句柄
- [curl_multi_remove_handle](curl_multi_remove_handle) — 移除curl批处理句柄资源中的某个句柄资源
- [curl_multi_select](https://secure.php.net/manual/zh/function.curl-multi-select.php) — 等待所有cURL批处理中的活动连接
- [curl_multi_setopt](https://secure.php.net/manual/zh/function.curl-multi-setopt.php) — 为 cURL 并行处理设置一个选项
- [curl_multi_strerror](https://php.net/manual/zh/function.curl-multi-strerror.php) — 返回字符串描述的错误代码
<!--more-->

### 1.2 PHP curl_multi 实现并发请求步骤

1. 调用 curl_multi_init，初始化一个批处理handle
2. 循环调用 curl_multi_add_handle，往1中的批处理handle 添加curl_init来的子handle
3. 持续调用 curl_multi_exec，直到所有子handle执行完毕。
4. 根据需要循环调用 curl_multi_getcontent 获取结果
5. 调用 curl_multi_remove_handle，并为每个字handle调用curl_close
6. 调用 curl_multi_close

### 1.3 PHP curl_multi 实现代码
```php
<?php

    /**
     *  Functional description : 
     *  Programmer : Mr.Liu
     * @param array $urls
     *  $urls = [
            'http://liuqinghui.dev.lywf.me/test/libin/ttt',
            'http://liuqinghui.dev.lywf.me/test/libin/ttt',
            'http://liuqinghui.dev.lywf.me/test/libin/ttt',
            'http://liuqinghui.dev.lywf.me/test/libin/ttt',
            'http://liuqinghui.dev.lywf.me/test/libin/ttt',
      ];
     * @return array
     */
    function multiGetRequest(array $urls):array 
    {

        //1、初始化一个批处理handle
        $mh = curl_multi_init();

        //2、往批处理handle 添加curl_init来的子handle
        foreach ($urls as $i => $url) {
            $connect[$i] = curl_init($url);
            curl_setopt($connect[$i], CURLOPT_HEADER, 0);
            curl_setopt($connect[$i], CURLOPT_CONNECTTIMEOUT, 60);
            curl_setopt($connect[$i], CURLOPT_RETURNTRANSFER, true);
            curl_multi_add_handle($mh, $connect[$i]);
        }

        //3、并发执行，直到全部结束。
        do {
            curl_multi_exec($mh, $active);
        } while ($active);

        //4、获取结果
        $return=[];
        foreach ($urls as $i => $url) {
            $return[] = curl_multi_getcontent($connect[$i]);

        }
        //5、移除子handle，并close子handle
        foreach ($urls as $i => $url) {
            curl_multi_remove_handle($mh, $connect[$i]);
            curl_close($connect[$i]);
        }

        //6、关闭批处理handle
        curl_multi_close($mh);
        return $return;
    }
```

## 2. 多进程并发：通过swoole_process实现
[查看文档](https://wiki.swoole.com/wiki/page/214.html)

### 2.1 示例代码
```php
<?php

$start_time = microtime(true);
$urls       = [
    'http://liuqinghui.dev.lywf.me/test/libin/ttt',
    'http://liuqinghui.dev.lywf.me/test/libin/ttt',
    'http://liuqinghui.dev.lywf.me/test/libin/ttt',
    'http://liuqinghui.dev.lywf.me/test/libin/ttt',
    'http://liuqinghui.dev.lywf.me/test/libin/ttt',
];

//遍历任务创建子进程
foreach ($urls as $url) {
    /**
     * 创建子进程，并调用方法 my_process
     */
    $process = new swoole_process("my_process", true);

    /**
     * 1.执行fork系统调用，启动进程;
     * 创建成功返回子进程的PID，创建失败返回false
     */
    $process->start();
    /**
     * 通过管道发数据到子进程。
     * 管道是单向的：发出的数据必须由另一端读取。不能读取自己发出去的
     */
    $process->write($url);

    /**
     * 保存进程,循环结算后统一调取返回数数据：
     */
    $process_list[] = $process;

}
//读取结果
foreach ($process_list as $process){
    //从管道中读取数据。
    echo $rec = $process->read();
}

/**
 * 子进程结束必须要执行wait进行回收，否则子进程会变成僵尸进程
 * $ret 是个数组其数据结构：
   array(3) {
    'pid' =>
    int(461)
    'code' =>
    int(0)
    'signal' =>
    int(0)
 }
*/
while ($ret = swoole_process::wait()) {
    $pid = $ret['pid'];
    echo PHP_EOL . "Worker Exit, PID=" . $pid . PHP_EOL;
}


//子进程创建成功后要执行的函数
function my_process(swoole_process $worker)
{
    sleep(1);//暂停1s
    $url    = $worker->read();
    //---- 执行具体业务程序开始-----
    $ch = curl_init();
    // 设置URL和相应的选项
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_HEADER, 0);
    // 抓取URL并把它传递给浏览器
    $return =  curl_exec($ch);
    // 关闭cURL资源，并且释放系统资源
    curl_close($ch);
    //---- 执行具体业务程序结束-----
    $worker->write($return);//写入数据到管道
}
$end_time = microtime(true);
echo sprintf("use time:%.3f s\n", $end_time - $start_time);

```


## 3. 多进程和多线程的区别？如何选择
### 3.1 不同的维度的对比

| 对比维度 | 多进程 | 多线程 |总结|
| ------ | ------ | ------ | ------ |
| 数据共享、同步 | 数据共享复杂，需要用IPC；<br/>数据是分开的，同步简单 | 因共享进程数据，数据共享简单。<br/>但也因此导致同步复杂 | 各有优势|
| 内存、CPU | 占用内存多，切换复杂。<br/>CPU利用率低 | 占用内存少，切换简单，CPU利用率高 |线程占优|
| 创建销毁、切换 | 创建销毁、切换复杂，速度慢 | 创建销毁、切换简单，速度很快 |线程占优|
| 编程、调试 | 编程简单，调试简单 | 编程复杂，调试复杂 |进程占优|
| 可靠性 | 进程间不会互相影响 | 一个线程挂掉将导致整个进程挂掉 |进程占优|
| 分布式 | 适应于多核、多机分布式；<br/>扩展到多台机器比较简单 | 适应于多核分布式 |进程占优|

[如何选择?](https://blog.csdn.net/lishenglong666/article/details/8557215)