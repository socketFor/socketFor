---
title: "PHP"
date: 2024-09-22
categories: 
  - "php"
coverImage: "13.jpg"
---

### 语法

```
<?php

?>
```

### API的编写

```
<?php
//判断是否为空
function Isnull($str){
    return $str==null;
};
//判断是否小于6
function Islenth($str){
    return strlen($str)<6;
};

$message = '';

//遍历数组
foreach(['user','password'] as $key => $value){
    if(Isnull($_POST[$value])){
        $message = $value.'不能为空';
        break;
    }
    if($value =='password' && Islenth($_POST[$value])){
        $message = $value.'不能为小于6位';
        break;
    }
};

// 0 ok ; 1 error
$formdata = [
    'cod' => 0,
    "msg" => $message,
    'data' => []
];

if($message != ''){
    $formdata['cod'] = 1;
}
//返回JSON格式的数据
printf(json_encode($formdata,JSON_UNESCAPED_UNICODE));

?>
```

### MySQL封装

```
<?php
  class PD{
    public static $tablename;
    public static $where='';
    public static $pdo;
    public static $stmt;
    public static $page='';
    public static $order='';
    public function __construct(){
      self::connent();
    }

    //连接数据库
    public static function connent(){
      $config = require 'config.php';
      $dsn=$config['dbms'].":host=".$config['host'].";dbname=".$config['dbName'];
      try {
        self::$pdo = new PDO($dsn,$config['user'],$config['pass']);
        echo "连接成功<br/>";
      } catch (PDOException $e) {
        die ("Error!: " . $e->getMessage() . "<br/>");
      }
     }
    // 选中数据表
    public static function table($tablename){
      self::$tablename = $tablename;
      return new self();
    }

    //查询条件
    public function where(string $tablewhere){
      $where = '';
      if(!empty($tablewhere)){
        //where拼接
        $where = "where ".$tablewhere;
      }
      self::$where = $where;
      return $this;
    }

    //增添数据
    //object->insert(array $data)
    public function insert(array $data){
      try {
        $keys = array_keys($data);   //获取数组key值
        $value = array_values($data);//获取数组value值
        if(empty($data)){
          throw new Exception('插入数据不能为空');
        };//为空抛出异常
        $mysql = "insert into ".self::$tablename." (".implode(',',$keys).") values (".implode(',',$value).");";
        //implode(',',$keys)数组转字符串
        self::$stmt = self::$pdo->prepare($mysql);
        self::$stmt->execute();
        $result = self::$stmt->rowCount();
        self::$stmt->closeCursor();
        return $result;
      } catch (PDOException $e) {
        throw new Exception('ERROR'.$e);
      }
    }

    //删除数据
    //object->where(str)->delete()
    public function delete(){
      try {
        if(empty(self::$where)){
          throw new Exception('缺少where条件');
        }
        $mysql = "delete from ".self::$tablename." ".self::$where.";";
        self::$stmt = self::$pdo->prepare($mysql);
        self::$stmt->execute();
        $result = self::$stmt->rowCount();
        self::$stmt->closeCursor();
        return $result;
      } catch (PDOException $e) {
        throw new Exception('ERROR'.$e);
      }
    }

    //更改数据
    //object->where(str)->update
    public function updata(string $data){
      try {
        if(empty(self::$where)){
          throw new Exception('缺少条件');
        }
        if(empty($data)){
          throw new Exception('缺少修改内容');
        }
        $mysql = "update ".self::$tablename." set ".$data." ".self::$where.";";
        return $mysql;
      } catch (PDOException $e) {
        throw new Exception('ERROR'.$e);
      }
    }

    //查询数据
    //object->where(str)->select(array)
    public function select(array $data){
      try {
        if(empty($data)){
          throw new Exception('缺少查询内容');
        }
        $mysql = "select ".implode(',',$data)." from ".self::$tablename." ".self::$where." ".self::$order." ".self::$page.";";
        self::$stmt = self::$pdo->prepare($mysql);
        self::$stmt->execute();
        $result = self::$stmt->fetchall(PDO::FETCH_ASSOC);
        self::$stmt->closeCursor();
        return $result;
      } catch (PDOException $e) {
        throw new Exception('ERROR'.$e);
      }
    }   

    //页数查询
    //object->limit(array)
    public function limit(array $page){
      self::$page = "LIMIT ".$page[0].",".$page[1];
      return $this;
    }

    /*
    功能:排序查询

    方法:name:排序表名
         num :为1时升序，为2时降序

    用法：object->order(str,[1,2])
    */
    public function order(string $name,int $num){
      if($num==1){
        self::$order = "order by ".$name." "."asc";
      }
      elseif($num==2){
        self::$order = "order by ".$name." "."desc";
      }
      return $this;
      
    }

  } 

  // phpinfo();
    $te = new PD;
    $text = $te::table("user")->where("")->limit([0,3])->select(["username","password"]);
    print_r($text);

?>
```
