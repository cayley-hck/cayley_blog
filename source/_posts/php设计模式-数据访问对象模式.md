---
title: php设计模式-数据访问对象模式
date: 2016-09-06 22:27:10
tags:
---
 数据访问对象模式, 描述了如何创建提供透明访问任何对象源的对象。
 采用这种设计模式,数据库连接的初始化，数据库选择，数据获取等操作。这种对数据的访问的操作的封装就是数据访问对象模式。
 其实数据访问对象模式除了用在数据库访问上。还可以用在很多地方，例如：我们要建立一个文件缓存系统，就涉及到缓存的创建，读取，更新等操作，我们也可以把这些操作抽象出来，方便操作，而且不用重复书写代码大量代码，有时候也能起到让调用者无需关心实现细节的目的

 解决问题：如何创建透明访问任何数据源的对象(重复和数据源抽象化)


{% codeblock %}


<?php

/**
*这是一个抽象类，为了能够使用该类必须扩展该类。因为很可能会同时打开多个数据库连接，所以在数据访问对象类中存储内部的数据库连接并且每个查询都进行引用是十分重要的。这个数据访问对象类应
*当唯一地引用自其自己的连接。通常，在更多可拓展的模型中，接口被创建用于共享连接。
*/

abstract class baseDAO
{
    private $__connection;
    public function __construct()
    {
        $this->__connectToDB(DB_USER, DB_PASS, DB_HOST, DB_DATABASE);
    }
    private function __connectToDB($user, $pass, $host, $database)
    {
        $this->__connection = mysqli::_connect($host, $user, $pass, $database);
    }
    public function fetch($value, $key = NULL)
    {
        if(is_null($key))
        {
            $key = $this->_primaryKey;
        }
        $sql = "select * from {$this->_tableName} where {$key}='{$value}'";
        $results = mysqli_query($sql, $this->__connection);
        $rows = array();
        while ($result = mysqli_fetch_array($results))
        {
            $rows[] = $result;
        }
        return $rows;
    }
    public function update($keyedArray)
    {
        $sql = "update {$this->_tableName} set ";
        foreach ($keyedArray as $column=>$value)
        {
            $updates[] = "{$column} = '{$value}' ";
        }
        $sql .= implode(",",$updates);
        $sql .= "where {$this->_primaryKey}='{$keyedArray[$this->_primaryKey]}'";
        mysqli:_query($sql, $this->__connection);
    }
}

/**
* 具体实现子类
*/
class userDAO extends baseDAO
{
    protected $_tableName = "userTable";
    protected $_primaryKey = "id";
    public function getUserByFirstName($name)
    {
        $result = $this->fetch($name, 'firstName');
        return $result;
    }
}

/**
*该对象的使用者不会知道实际使用的表结构和数据库引擎
*/

define('DB_USER','user'); 
define('DB_PASS','pass');
define('DB_HOST','localhost');
define('DB_DATABASE','test');

$user = new userDAO();
$id = 1;
$userInfo = $user->fetch($id);
$updates = array('id'=>1, 'firstName'=>'arlon');
$user->update($updates);
$all = $user->getUserByFirstName('arlon');

php?>

{% endcodeblock %}