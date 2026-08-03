---
title: "PHP-类"
date: 2024-09-16
categories: 
  - "php"
---

### 类的创建与调用

```
class classname{
    public $name = '小猫';
    public function funcname(){
        echo $this->name.'在喝水';
    }
}

$cat = new classname;
$cat->name = '小狗';
$cat->funcname();
```

### 公有、保护、私有

```
public //公有
protected //保护
private //私有
```

### \_\_construct 函数

```
class classname{
    public $name = '小猫';
    public function __construct()
    {
        $this->name = '小花';
    }
    public function funcname(){
        echo $this->name.'在喝水';
    }
}

$cat = new classname;
//__construct函数会在new类的时候运行（可以传参）
$cat->funcname();
$cat->name = '小狗';
$cat->funcname();
```

### \_\_destruct函数

```
class classname{
    public $name = '小猫';
    public function __construct()
    {
        $this->name = '小花';
    }
    public function funcname(){
        echo $this->name.'在喝水';
    }
    public function __destruct()
    {
        echo '类被注销';
    }
}
$cat = new classname;
//__construct函数会在new类的时候自动运行
$cat->funcname();
$cat->name = '小狗';
$cat->funcname();
unset($cat);
//__destruct函数会在类被注销时自动运行
```

### static 静态变量

```
class classname{
    public $name = '小猫';
    public static $do = '喝水';
    public function __construct()
    {
        $this->name = '小花';
    }
    public function funcname(){
        echo $this->name.'在喝水';
    }
    public function take(){
        echo $this->name.self::$do; 
    }
    public function __destruct()
    {
        echo '类被注销';
    }
}

$cat = new classname;
//static 为静态变量 调用需用self（使用静态变量在下一次new类的时候不会改变）
//非静态不能在静态函数中直接调用要调用需要new一个类(new self)->name;
$cat->take();
```

### const 静态常量

```
class classname{
    public $name = '小猫';
    public static $do = '喝水';
    public const ANIMAL = '狗';
    
    public function __construct()
    {
        $this->name = '小花';
    }
    public function funcname(){
        echo $this->name.'在喝水';
    }
    public static function take(){
        echo self::ANIMAL.self::$do; 
    }
    public function __destruct()
    {
        echo '类被注销';
    }
}

$cat = new classname;
//静态常量只能读取不能改变
$cat::take();
echo classname::ANIMAL;
```

### extends 类继承
