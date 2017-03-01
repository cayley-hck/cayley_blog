---
title: php设计模式-适配器
date: 2016-09-06 15:33:10
tags:
---
 适配器模式（Adapter Pattern）, 主要使用适配器来更新接口,而不需要去改动公共接口的标准。把一个类的接口变换成客户端所期待的另一种接口， 使原本因接口不匹或者不兼容,而无法在一起工作的两个类能够在一起工作。又称为转换器模式、变压器模式、包装（Wrapper）器模式,
 在转换一个对象的接口用于另一个对象时，实现Adapter对象不仅是最佳做法，而且也能减少很多麻烦,适配器模式利用继承或组件来进行模式设计。
相比继承，组件可用性高，低耦合，冗余度低，因此推荐采用组件的模式来进行设计


适用条件:

当你的实现和需要的接口，都无法修改的时候。
例如，你需要给甲方已有的系统做标准的兼容，标准不可修改，甲方的系统也不可修改，这个时候你就需要适配器的设计模式了。
对于web编程来说，将你现有的实现，和三方库结合起来，就需要使用适配器模式。



组成:
Target(目标)：— 定义Client使用的与特定领域相关的接口。
Client(客户端)：与符合Target接口的对象协同。
Adaptee(被适配者)：定义一个已经存在并已经使用的接口，这个接口需要适配。
Adapte(适配器) ：适配器模式的核心。它将对被适配Adaptee角色已有的接口转换为目标角色Target匹配的接口。对Adaptee的接口与Target接口进行适配

适配器模式在不修改现有代码的基础上，保留了架构。使用继承的适配器和使用组件的适配器各有利弊，继承的类冗余度/空间复杂度偏高，组件的调用栈/时间复杂度偏高，应该结合实际情况选择。


实现:

假设我们有两个类,但我们不能够修改这两个类实现

{% codeblock %}
<?php
//adpatee
class AdapteeOne {
  protected $name;
  public function __construct() {
    $this->name = "One";
  }
  public function doOne() {
    echo "{$this->name} eat fish.\n";
  }
}

class AdapteeOne {
  protected $name;
  public function __construct() {
    $this->name = "Two";
  }
  public function doTwo() {
    echo "{$this->name} eat moss.\n";
  }
}
?>
{% endcodeblock %}

我们现在有个需要，实现一个do接口,需要使用adapteeOne的doOne方法来实现 ||adapteeTwo的doTwo方法
{% codeblock %}
<?php
//traget
interface Target {
  function do();
}
?>
{% endcodeblock %}

但是我们不能修改AdapteeOne和AdapteeTwo类，这里就需要使用适配器了，创建两个适配器：

1.使用继承的适配器模式

{% codeblock %}
<?php
//adapter
class OneAdapter extends AdapteeOne implements Target {
  public function __construct() {
    $this->name = "One";
  }
  public function do() {
    $this->doOne();
  }
}

class TwoAdapter extends AdapteeTwo implements Target {
  public function __construct() {
    $this->name = "Two";
  }
  public function do() {
    $this->doTwo();
  }
}
?>
{% endcodeblock %}

然后是调用代码：
{% codeblock %}

<?php
//client
$oneAdapter = new OneAdapter();
$oneAdapter->do();

$twoAdapter = new TwoAdapter();
$twoAdapter->do();

?>
{% endcodeblock %}


2.使用组件的适配器模式
{% codeblock %}
<?php
//adapter
class OneAdapter  implements Target {
  protected $obj;
  public function __construct() {
    $this->obj = new AdapteeOne();
  }
  public function do() {
    $this->obj->doOne();
  }
}

class TwoAdapter implements Target {
  protected $obj;
  public function __construct() {
    $this->obj = new AdapteeTwo;
  }
  public function do() {
    $this->obj->doTwo();
  }
}
?>
{% endcodeblock %}

然后是调用代码：
{% codeblock %}
<?php
//client
$oneAdapter = new OneAdapter();
$oneAdapter->do();

$twoAdapter = new TwoAdapter();
$twoAdapter->do();

?>
{% endcodeblock %}






