---
title: php设计模式-装饰器
date: 2016-09-06 22:51:31
tags:
---

装饰器模式,动态地给一个对象添加一些额外的职责或者行为。在不必改变原类代码和使用继承的情况下，动态的扩展一个对象的功能。它是通过创建一个包装对象，也就是装饰来包裹真实的对象。这比使用子类更加灵活.
当用于一组子类时，装饰器模式更加有用。如果你拥有一族子类，你需要在与子类独立使用情况下添加额外的特性，你可以使用装饰器模式，以避免代码重复和具体子类数量的增加

解决问题: 

不必重写任何已有的功能性代码，而是对某个基于对象应用增量变化。在主代码流中应该能够直接插入一个或多个更改或“装饰”目标对象的装饰器，同时不影响其他代码流。


组成:

抽象组件：定义一个对象接口，以规范准备接受附加责任的对象，即可以给这些对象动态地添加职责,可忽略.

具体组件 :被装饰者，定义一个将要被装饰增加功能的类。可以给这个类的对象添加一些职责

抽象装饰器:维持一个指向构件组件抽象的实例，并定义一个与抽象组件接口一致的接口,可忽略

具体装饰器角色:向组件添加职责

实现:
{% codeblock %}

<?php   
  
/** 
 * 抽象组件
 */  
class widget {  
    function paint() {  
        return $this->_asHtml();  
    }  
}  
  
/** 
 *  
 * 具体组件 ,testinput对象  
 */  
class TextInput extends widget {  
  
    protected $_name;  
    protected $_value;  
  
    function TextInput($name, $value='') {  
        $this->_name = $name;  
        $this->_value = $value;  
    }  
  
    function _asHtml() {  
        return '<input type="text" name="'.$this->_name.'" value="'.$this->_value.'">';  
  
    }  
  
}  

/**
* 如果我们要在input前面加上提示文字,lable ,这样子,都要加,那就要好多个子类了
* 那是不是都要继承widget类,然后实现其具体标签的类,那会产生大量的子类,而已每个子类只是做些小改变而已
* 这种情况就要使用到装饰器的设计模式
*/

/** 
 * 抽象装饰器（Decorator)
 *  
 */  
class LableWidgetDecorator {  
  
    protected $_widget;  
    function __construct( &$widget) {  
        $this->_widget = $widget;  
    }  
    function paint() {  
        return $this->_widget->paint();  
    }  
  
}


/** 
 * 具体装饰器角色: 
 * 为建立一个标签,需要传入其的内容，以及原始的组件 
 * 也需要重写paint（）方法 
 * 
 */  
  
  
class LableDecorator extends LableWidgetDecorator {  
  
    protected $_label;  
  
    function __construct($label, &$widget) {  
        $this->_label = $label;  
        parent::__construct($widget);  
    }  
  
    function paint() {  
        return '<b>'.$this->_label.':</b> '.$this->_widget->paint();  
    }  
  
}  
  
  
/** 
 * 使用
 */  

$label =  new LableDecorator('姓名', new TextInput('name', '');
echo $lable->paint(); 	

$label =  new LableDecorator('昵称', new TextInput('nickname', '');  
echo $lable->paint(); 	

$label =  new LableDecorator('年龄', new TextInput('age', '');  
echo $lable->paint(); 	

?>  
{% endcodeblock %}

总结:

1）使用装饰器设计模式设计类的目标是：
 不必重写任何已有的功能性代码，而是对某个基于对象应用增量变化。 

2)
 装饰器设计模式采用这样的构建方式： 在主代码流中应该能够直接插入一个或多个更改或“装饰”目标对象的装饰器，同时不影响其他代码流。

3)
 装饰器模式采用对象组合而非继承的手法，实现了在运行时动态的扩展对象功能的能力，
 而且可以根据需要扩展多个功能，避免了单独使用继承带来的“灵活性差”和“多子类衍生问题”。
 同时它很好地符合面向对象设计原则中“优先使用对象组合而非继承”和“开放-封闭”原则。

