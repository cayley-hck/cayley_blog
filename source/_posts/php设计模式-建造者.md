---
title: php设计模式-建造者
date: 2016-09-06 21:59:49
tags:
---
建造者模式也称生成器模式，将一个复杂对象的构造与它的表示分离，使同样的构建过程可以创建不同的表示，这样的设计模式被称为建造者模式。

解决问题:
 消除其他对象的复杂创建过程，这是最佳做法，而且在对象的构造和配置方法改变时，可以尽可能的减少重复更改代码。

组成:

产品角色：建造中的复杂对象。它要包含那些定义组件的类，包括将这些组件装配成产品的接口。

抽象建造者：为创建一个Product对象的各个部件指定抽象接口，以规范产品对象的各个组成成分的建造。一般而言，此角色规定要实现复杂对象的哪些部分的创建，并不涉及具体的对象部件的创建。

具体建造者:

1）实现抽象建造者角色的抽象方法。

2）定义并明确它所创建的表示，即针对不同的业务逻辑，具体化复杂对象的各部分的创建

3) 提供一个检索产品的接口

4) 构造一个使用Builder接口的对象即在指导者的调用下创建产品实例

指导者：调用具体建造者角色以创建产品对象的各个部分。指导者并没有涉及具体产品类的信息，真正拥有具体产品的信息是具体建造者对象。它只负责保证对象各部分完整创建或按某种顺序创建。


实现:
{% codeblock %} 
<?php
//产品
class product
{
    protected $_type = "";
    protected $_size = "";
    protected $_color = "";
    
    //假设有三个复杂的创建过程
    public function setType($type)
    {
        $this->_type = $type;
    }
    public function setSize($size)
    {
        $this->_size = $size;
    }
    public function setColor($color)
    {
        $this->_color = $color;
    }
}
$productConfigs = array('type'=>'shirt','size'=>'XL','color'=>'red');
$product = new product();
//创建对象时分别调用每个方法不是最佳做法，扩展和可适应性低
$product->setType($productConfigs['type']);
$product->setSize($productConfigs['size']);
$product->setColor($productConfigs['color']);
//如果使用复杂的创建过程中使用构造函数来实现更不可取。


//下面使用建造者模式,进行重写

//抽象建造者
abstract Builder{
	//建造部分
	public abstract function build();
	//提交返回产品接口
	public abstract function getProduct();  

}

//具体建造者
class productBuilder
{
    protected $_product = null;
    protected $_configs = array();
    public function __construct($configs)
    {
        $this->_product = new product();
        $this->_configs = $configs; 
    }
    public function build()
    {
        $this->_product->setSize($this->_configs['size']);
        $this->_product->setType($this->_configs['type']);
        $this->_product->setColor($this->_configs['color']);
    }
    public function getProduct()
    {
        return $this->_product;
    }
}

//指导者
$builder = new productBuilder($productConfigs);
$builder->build();
$product = $builder->getProduct();
?>
{% endcodeblock %}