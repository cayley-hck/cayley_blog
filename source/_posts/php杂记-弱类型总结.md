---
title: php杂记-弱类型总结
date: 2016-08-05 14:36:43
tags:
---
php是个弱类型的语言,这是众所周知的事情,所谓的弱类型就是php不严格检查变量的类型.
PHP 支持 8 种原始数据类型。
四种标量类型：
boolean（布尔型）
integer（整型）
float（浮点型，也称作 double)
string（字符串）
两种复合类型：
array（数组）
object（对象）
最后是两种特殊类型：
resource（资源）
NULL（无类型）

在验证变量类型的时候,可以使用var_dump()|| gettype()php内置函数,开查看.或者使用is_{type}函数(is_int(),is_string(),is_array(),is_object())来判断


{% codeblock %}
<?php
$var = 1;
var_dump($var);
$var = 1.11;
var_dump($var);
$var = array();
var_dump($var);
$var = "string";
var_dump($var);
$var = new stdClass();
var_dump($var);
?>
{% endcodeblock %}

看下执行结果:
{% codeblock %}
huangchengkaideMacBook-Air:www kai$ php test.php 
int(1)
array(0) {
}
string(6) "string"
huangchengkaideMacBook-Air:www kai$ 
huangchengkaideMacBook-Air:www kai$ 
huangchengkaideMacBook-Air:www kai$ php test.php 
int(1)
float(1.11)
array(0) {
}
string(6) "string"
object(stdClass)#1 (0) {
}
{% endcodeblock %}

弱类型的语言对变量的数据类型没有限制，所以我们可以在任何地时候将变量赋值给任意的其他类型的变量，同时变量也可以转换成任意地其他类型的数据。


php怎样实现弱类型的呢?
php是c语言来实现,我们现在看下zend引擎,中类型定义文件 zend_types.h (php7)
{% codeblock %}

typedef struct _zval_struct     zval;
struct _zval_struct {
        zend_value        value;                        /* value */
        union {
                struct {
                        ZEND_ENDIAN_LOHI_4(
                                zend_uchar    type,                     /* active type */
                                zend_uchar    type_flags,
                                zend_uchar    const_flags,
                                zend_uchar    reserved)     /* call info for EX(This) */
                } v;
                uint32_t type_info;
        } u1;
        union {
                uint32_t     var_flags;
                uint32_t     next;                 /* hash collision chain */
                uint32_t     cache_slot;           /* literal cache slot */
                uint32_t     lineno;               /* line number (for ast nodes) */
                uint32_t     num_args;             /* arguments number for EX(This) */
                uint32_t     fe_pos;               /* foreach position */
                uint32_t     fe_iter_idx;          /* foreach iterator index */
        } u2;
};

{% endcodeblock %}

通过类型定义的代码,可以知道,php所有的变量结果都是用zval结构体来保存的,而zaval结果体中的zend_value结构体又是真正保存数据的

现在继续看下zend_value结构体

{% codeblock %}

typedef union _zend_value {
        zend_long         lval;                         /* long value */
        double            dval;                         /* double value */
        zend_refcounted  *counted;
        zend_string      *str;
        zend_array       *arr;
        zend_object      *obj;
        zend_resource    *res;
        zend_reference   *ref;
        zend_ast_ref     *ast;
        zval             *zv;
        void             *ptr;
        zend_class_entry *ce;
        zend_function    *func;
        struct {
                uint32_t w1;
                uint32_t w2;
        } ww;
} zend_value;


{% endcodeblock %}

zend_value是一个联合体。对于小于64位的简单类型，会直接存储值。对于其他比较复杂的类型，如字符串，数组，对象等，是存储的指针。这样，对于简单类型来说，变得简单高效

引发的问题:


1.类型装换问题
自动类型转换:
FALSE 会转为0，TRUE 会转为 1
1)如果该字符串没有包含 '.'，'e' 或 'E' 并且其数字值在整型的范围之内（由 PHP_INT_MAX 所定义），该字符串将被当成 integer 来取值。其它所有情况下都被作为 float 来取值
2)该字符串的开始部分决定了它的值。如果该字符串以合法的数值开始，则使用该数值。否则其值为 0（零）。合法数值由可选的正负号，后面跟着一个或多个数字（可能有小数点），再跟着可选的指数部分。指数部分由 'e' 或 'E' 后面跟着一个或多个数字构成。
{% codeblock %}
<?php
$foo = "0";  // $foo 是字符串 (ASCII 48)
$foo += 2;   // $foo 现在是一个整数 (2)
$foo = $foo + 1.3;  // $foo 现在是一个浮点数 (3.3)
$foo = 5 + "10 Little Piggies"; // $foo 是整数 (15)
$foo = 5 + "10 Small Pigs";     // $foo 是整数 (15)
?>
{% endcodeblock %}


如果要将某类型强制转换为某类型,可以对其使用强制类型转换||settype()函数
强制转换:
(bool), (boolean) - 转换为布尔类型 boolean:


当转换为 boolean 时，以下值被认为是 FALSE：
布尔值 FALSE 本身
整型值 0（零）
浮点型值 0.0（零）
空字符串，以及字符串 "0"
不包括任何元素的数组
特殊类型 NULL（包括尚未赋值的变量）
从空标记生成的 SimpleXML 对象

当转换为 boolean 时,-1 和其它非零值（不论正负）一样，被认为是 TRUE
所有其它值都被认为是 TRUE（包括任何资源）。
{% codeblock %}
<?php
var_dump((bool) "");        // bool(false)
var_dump((bool) 1);         // bool(true)
var_dump((bool) -2);        // bool(true)
var_dump((bool) "foo");     // bool(true)
var_dump((bool) 2.3e5);     // bool(true)
var_dump((bool) [12]); // bool(true)
var_dump((bool) []);   // bool(false)
var_dump((bool) "false");   // bool(true)
var_dump((bool) "0");   // bool(false)
var_dump((bool) 0.0);   // bool(false)
var_dump((bool) null);   // bool(false)
var_dump((bool) new stdClass());   // bool(true)

?>
{% endcodeblock %}

(int), (integer) - 转换为整形 integer

boolean转换时: FALSE 会转为0，TRUE 会转为 1,
浮点型转换时: 会向下取整,不要将未知的分数强制转换为 integer，这样有时会导致不可预料的结果
array转换时:空数组会转为 0 ,非空数组转为1
string类型转换时: 
使用强制转换时不会解析'.'，'e' 或 'E'为float类型,而且直接采用该字符串是否已合法的数字开始,则使用该值,否则其值为0;
其他类型转换时:会导致未知错误
{% codeblock %}
<?php
echo (int) true ; // 1
echo (int) false; //0
echo (int) 0.1; //0
echo (int) 1.5; //1
echo (int) ( (0.1+0.7) * 10 ); // 显示 7,其中内部的表示其实是类似 7.9999999999999991118...。
echo (int) []; //0
echo (int) [2,1,2,4,'222'=>11]; //1
echo (int)"10.5";                // 10 , 10.5被当成浮点数,向下取整为10
echo (int)"-10.5";                // -10 , -10是合法,为-10
echo (int)"-1.3e3";               // -1,-1是合法的数字
?>
{% endcodeblock %}

(float), (double), (real) - 转换为浮点型 float
string转换时:
如果该字符串包含 '.'，'e' 或 'E',会当做float来取值 ,而且采用该字符串是否已合法的数字开始,则使用该值,否则其值为0
其他的跟转换为int类似
{% codeblock %}

<?php
echo (float) 10;                                 //10
echo (float) '10.1';                            //10.1
echo (float) '-1.3e3';                          //-1300
echo (float) '-1.3e3abc';                       //-1300
echo (float) true;                              //1
echo (float) false;                             //0
echo (float) [];                                //0
echo (float) [1,2];                             //1
?>

{% endcodeblock %}

(string) - 转换为字符串 string
一个值可以通过在其前面加上 (string) 或用 strval() 函数来转变成字符串.
 boolean 的 TRUE 被转换成 string 的 "1"。Boolean 的 FALSE 被转换成 ""（空字符串）
 整数 integer 或浮点数 float 被转换为数字的字面样式的 string
 数组 array 总是转换成字符串 "Array"
 对象 object 总是被转换成字符串 "Object",自 PHP 5 起，适当时可以用 __toString 方法
 资源 resource 总会被转变成 "Resource id #1" 这种结构的字符串
 NULL 总是被转变成空字符串
 {% codeblock %}

 <?php
echo (string) 10;                                //'10'
echo (string) 10.1;                             //'10.1'
echo (string) -1.3e3;                           //-1300
echo (string) 1.3e3;                //-1300
echo (string) true;                             //1
echo (string) false;                            //0
$arr = [1,2,4];
        echo "{$arr}";                          //array
       echo strval($arr);              //array

?>
{% endcodeblock %}


(array) - 转换为数组 array
对于任意 integer，float，string，boolean 和 resource 类型，如果将一个值转换为数组，将得到一个仅有一个元素的数组，其下标为 0，该元素即为此标量的值。换句话说，(array)$scalarValue 与 array($scalarValue) 完全一样。

如果一个 object 类型转换为 array，则结果为一个数组，其单元为该对象的属性。键名将为成员变量名，不过有几点例外：整数属性不可访问；私有变量前会加上类名作前缀；保护变量前会加上一个 '*' 做前缀。这些前缀的前后都各有一个 NULL 字符。这会导致一些不可预知的行为：

将 NULL 转换为 array 会得到一个空的数组
{% codeblock %}

<?php

var_dump((array) 1);
var_dump((array) 1.1);
var_dump((array) 1.3e3);
var_dump((array) (new stdClass()));
var_dump((array) 'is array');
var_dump((array) null);
class A {
    private $A; // 输出 '\0A\0A'
}

class B extends A {
    private $A; // 输出 '\0B\0A'
    public $AA; // 输出 'AA'
}

var_dump((array) new B());
?>
huangchengkaideMacBook-Air:www kai$ php test.php 
array(1) {
  [0]=>
  int(1)
}
array(1) {
  [0]=>
  float(1.1)
}
array(1) {
  [0]=>
  float(1300)
}
array(0) {
}
array(1) {
  [0]=>
  string(8) "is array"
}
array(0) {
}
array(3) {
  ["BA"]=>
  NULL
  ["AA"]=>
  NULL
  ["AA"]=>
  NULL
}
huangchengkaideMacBook-Air:www kai$ 
{% endcodeblock %}

有两个键名为 'AA'，不过其中一个实际上是 '\0A\0A' 


(object) - 转换为对象 object
如果将一个对象转换成对象，它将不会有任何变化。如果其它任何类型的值被转换成对象，将会创建一个内置类 stdClass 的实例。如果该值为 NULL，则新的实例为空。数组转换成对象将使键名成为属性名并具有相对应的值。对于任何其它的值，名为 scalar 的成员变量将包含该值。
{% codeblock %}


<?php
$obj = (object) 'string';
echo $obj->scalar."\n";  // 'string'
$obj = (object) 1;
echo $obj->scalar."\n";  // 1
$obj = (object) 1.1;
echo $obj->scalar."\n";  // 1.1
$obj = (object) 1.3e3;
echo $obj->scalar."\n";  // 1300
$obj = (object) [];
var_dump($obj);
$obj = (object) [1,2,3,5];
var_dump($obj);
$obj = (object) ['b'=>1,'c'=>2];
var_dump($obj);
?>

huangchengkaideMacBook-Air:www kai$ php test.php 

string
1
1.1
1300
object(stdClass)#1 (0) {
}
object(stdClass)#2 (4) {
  [0]=>
  int(1)
  [1]=>
  int(2)
  [2]=>
  int(3)
  [3]=>
  int(5)
}
object(stdClass)#1 (2) {
  ["b"]=>
  int(1)
  ["c"]=>
  int(2)
}
huangchengkaideMacBook-Air:www kai$ 
{% endcodeblock %}

(unset) - 转换为 NULL (PHP 5)
特殊的 NULL 值表示一个变量没有值。NULL 类型唯一可能的值就是 NULL。

在下列情况下一个变量被认为是 NULL：

被赋值为 NULL。

尚未被赋值。

被 unset()。

使用 (unset) $var 将一个变量转换为 null 将不会删除该变量或 unset 其值。仅是返回 NULL 值而已。

{% codeblock %}

{% endcodeblock %}

以上内容参考自官方文档

2.类型比较

类型比较表的官方文档地址:
https://secure.php.net/manual/zh/types.comparisons.php

特别要注意,有时候比较的时候,会涉及类型转换问题:

松散型(==)和严格型(===)比较问题等
<?php

if(0=='0'){ //true
    echo 'true';
} else{
    echo 'false';
}

if(0 == 'abcdefg'){ //true
    echo 'true';
} else{
    echo 'false';
}

if(0 === 'abcdefg'){ //true
    echo 'true';
} else{
    echo 'false';
}

if(1 == '1abcdef'){ //true
    echo 'true';
} else{
    echo 'false';
}

?>


3.内置函数的参数的松散

md5()

$array1[] = array(
    "foo" => "bar",
    "bar" => "foo",
);
$array2 = array("foo", "bar", "hello", "world");
var_dump(md5($array1)==var_dump($array2)); //true

PHP手册中的md5()函数的描述是string md5 ( string $str [, bool $raw_output = false ] )，md5()中的需要是一个string类型的参数。但是当你传递一个array时，md5()不会报错，知识会无法正确地求出array的md5值，这样就会导致任意2个array的md5值都会相等。这个md5()的特性在攻防平台中的bypass again同样有考到。

strcmp()

strcmp()函数在PHP官方手册中的描述是int strcmp ( string $str1 , string $str2 ),需要给strcmp()传递2个string类型的参数。如果str1小于str2,返回-1，相等返回0，否则返回1。strcmp函数比较字符串的本质是将两个变量转换为ascii，然后进行减法运算，然后根据运算结果来决定返回值。
如果传入给出strcmp()的参数是数字呢？

$array=[1,2,3];
var_dump(strcmp($array,'123')); //null,在某种意义上null也就是相当于false。

strcmp这种特性在攻防平台中的pass check有考到。

switch()

如果switch是数字类型的case的判断时，switch会将其中的参数转换为int类型。如下：


$i ="2abc";
switch ($i) {
case 0:
case 1:
case 2:
    echo "i is less than 3 but not negative";
    break;
case 3:
    echo "i is 3";
}

这个时候程序输出的是i is less than 3 but not negative，是由于switch()函数将$i进行了类型转换，转换结果为2。

in_array()

在PHP手册中，in_array()函数的解释是bool in_array ( mixed $needle , array $haystack [, bool $strict = FALSE ] ),如果strict参数没有提供，那么in_array就会使用松散比较来判断$needle是否在$haystack中。当strince的值为true时，in_array()会比较needls的类型和haystack中的类型是否相同。


$array=[0,1,2,'3'];
var_dump(in_array('abc', $array));  //true
var_dump(in_array('1bc', $array)); //true

可以看到上面的情况返回的都是true,因为’abc’会转换为0，’1bc’转换为1。
array_search()与in_array()也是一样的问题。