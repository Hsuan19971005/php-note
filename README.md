# PHP 筆記
PHP(Hypertext Prepocessor)超文字預處理器，為直譯式的伺服器語言，可嵌入在HTML中使用。

[toc]
## 環境安裝與設定
下載php的方式：
- MAMP(適合Wordpress)
- Homebrew(適合Laravel等框架)

常見的web server:
- Apache
- Nginx(需要處理fpm)
- PHP自帶的web server(不是很好用)

PHP設定檔為`php.ini`，位在`/opt/homebrew/etc/php/8.1/`

## Homebrew 安裝 PHP
```bash!
$ brew install php
$ brew install php@8.1
# reopen terminal
$ which php # /opt/homebrew/bin/php
$ ls -al "php安裝的路徑"
$ brew link php
$ brew unlink php
$ echo $PATH # 前面的指令優先級較高
$ php -S 127.0.0.1:8000 # 運行index.php
```
---

## PHP基礎語法
PHP使用`<?php`、`?>`作為開頭與結尾：
```php
<?php 
// php code
?>
```
### Comment 註解
```php
// 單行註解，較常使用
# linux風格的單行註解
/* 這是
   多行縮排 */
```
### echo / print
`echo`與`print`都能輸出字串到畫面，兩者差異：
- `echo`：無回傳值，可省略括號()，效能稍微快一些
- `print`：回傳值1
```php=
echo('print out the words');
echo 'print out the words';
echo "Hello World! <br/>";
echo 'Hello World! <br/>';

// 接合變數與字串
$var = 'love';
echo 'I '.$var.' you.'; // I love you.
print 'I'.$var.'HAHA';
```
### 變數命名
變數命名規則：
- 以`$`開頭加上變數名稱
- 英文字母或底線`_`開頭，不可以數字開頭
- 英文大小寫有差異 ex: $name, $Name
- 變數名稱只可使用英文數字字符與底線(A-z, 0-9, and _ )
```php
$txt = 'love';
echo "I $txt you."; // I love you.
```
:::info
:information_source: 沒有初始值的變數，預設會是`NULL`
:::
### 變數範圍 Scope
PHP有三種變數範圍：local、global、static，宣告在funciton中的變數為local scope，宣告在funciton外部的變數則是global scope。
```php
$global_x = 3; // global scope 

function myFun() {
  $x = 5; // local scope
  echo "<p>Variable x inside function is: $x</p>";
}
myFun();
```
在function內部取用外部的變數會產生錯誤，然而可以使用`global`關鍵字在function內部取得global scope的變數：
```php=
$x = 3;
function myFun(){
  global $x;
  echo $x;
}
```
其實PHP有一個超級全域變數矩陣`$GLOBAL`用以記錄所有的global variable，因此也可以使用該變數於function內取得global variable：
```php
$x = 5;
$y = 10;

function myFun() {
  $GLOBALS['y'] = $GLOBALS['x'] + $GLOBALS['y'];
}

myFun();
echo $y; // outputs 15
```
可以透過`static`宣告變數，該變數不會因為function執行結束而消失：
```php
function myFun() {
  static $x = 0;
  echo $x;
  $x++;
}

myFun(); // 0
myFun(); // 1
myFun(); // 2
```

### DataType
* String
* Integer
* Float (也可以稱作double)
* Boolean
* Array
* Object
* NULL
* Resource

可以使用`var_dump()`輸出變數的資料型別到畫面中。
### 字串String

```php
$txt = 'PHP';
echo "Learning ".$txt." is ". "fun <br/>"; // Learning PHP is fun

// 注意''與""的不同
$txt = 'love';
echo 'I $txt you.'; // I $txt you. 
echo "I $txt you."; // I love you.
```
在`""`中使用`\`來跳脫字元
```php
$txt = "My name is \"Hsuan\".\n"; // My name is "Hsuan".
```
-> [跳脫字元列表](https://www.php.net/manual/en/regexp.reference.escape.php)
String 其他的常見操作請參考標題：**PHP 常見函數與使用情境 / String**
### 數字 Number
主要有三個類別：
- Integer：整數
- Float：浮點數，PHP中double與float是相同的
- Number Strings：字串類型的數字，如`'120'`

另外還有兩個資料類別：
- `Infinity`：超過`PHP_FLOAT_MAX`的數字
- `NaN`：代表計算錯誤或無法運算
```php
$a = 5;
$b = 5.34;
$c = "25";

is_int($a); // true
is_int($b); // false
is_int($c); // false

is_float($a); // false
is_float($b); // true
is_float($c); // false

is_numeric($a); // true
is_numeric($b); // true
is_numeric($c); // true
```
:::warning
:warning: 小心自動型別轉換
```php
var_dump('1' + 2); // int(3)
var_dump('1' + '3.0'); // float(4)
```
:::
在進行String、Integer、Float三者間的型別轉換時，除了使用PHP提供的函式庫，如`intval`外，還可使用cast `(int)`來進行轉換：
```php
// to integer
$x = 123.456;
echo intval($x);
echo (int)$x;

// to float
$y = '456.789';
echo floatval($y);
echo (float)$y;

// to string
$z = 654.321;
echo strval($z);
echo (string)$z;
```
Number 其他的常見操作請參考標題：**PHP 常見使用方法與情境 / Number & Math**
### 運算子
這裡針對特殊運算子進行說明。[運算子列表](https://www.w3schools.com/php/php_operators.asp)
- `==`、`!=`的比較**不會**判斷型別
- `==`、`!=`的比較**會**判斷型別
- `<>`等於`!=`但不推薦使用
```php
var_dump(2 == '2.0'); // bool(true)
var_dump(2 === '2.0'); // bool(false)
```
- 建議不要使用`and`、`or`、`xor`，而是使用`&&`、`||`、`!`
- `<=>`會比較兩數大小，相同回傳0，左側數字大回傳1，右側數字大回傳-1

### True or False
以下情況會判斷為false: 
- `false`本身
- 字串類型 `''`, `'0'`
- 數字類型 `0`, `0.0`, `-0.0`
- 空矩陣 `[]`
- `NULL`

:::warning
:warning: 使用邏輯判斷時要注意Boolean的自動轉型：
```php!
var_dump('' === false); // false
var_dump('' == false); // true
echo (4-4) ? 'true' : 'false'; // true
```
:::

### 型別指定 ＆ 強制轉型 & 嚴格模式
PHP預設會自動轉型，可以透過型別指定或強制轉型(cast)試著解決這一問題，但是還是有可能會出現錯誤(Number轉String)。最好的解法是設定嚴格模式，注意`strict_type`只能寫在第一行。
```php
// 型別指定（數字可能自動轉成String）
function myFunction(Person person){}
// 使用cast強制轉型
(int) $stringVar
```
```php
// 嚴格模式，只能寫在第一行
declare(strict_type=1);
```

### 常數&魔術常數
常數Constant在宣告後就無法再被修改，可以使用`define()`、`const`進行宣告，或者也可以使用Superglobals中的`$GLOBALS`宣告：
```php!
// 常數以大寫表示，並且不使用$
define(BR, "<br/>");
const BR = "<br/>"; // 注意：const不能在block scope內宣告

$GLOBALS["BR"] = "<br/>";
```
魔術常數(Magic Constants)：為系統常數，case-insensitive，會於編譯時賦予數值，因此會根據系統的差異而有所變化，以下為9個Magic Constants。
- `__CLASS__`
- `__DIR__`
- `__FILE__`
- `__FUNCTION__`
- `__LINE__`
- `__METHOD__`
- `__NAMESPACE__`
- `__TRAIT__`
- `ClassName::class`

### 超級全域變數
超級全域變數(Superglobals)沒有scope的限制，可以在任何位置取用：
- `$GLOBALS`
- `$_SERVER`
- `$_REQUEST`
- `$_POST`
- `$_GET`
- `$_FILES`
- `$_ENV`
- `$_COOKIE`
- `$_SESSION`

常見使用範例：
```php!
$x = 1;
$GLOBALS['x']; // 獲取全域變數

$_GET['yourParam']; // 獲取query string的參數
$_POST['yourParam']; // 獲取post的參數
$_COOKIE['yourParam']; // 獲取瀏覽器傳出的Cookie參數

// _REQUEST為_GET, _POST, _COOKIE的集合，不建議使用，有安全上的疑慮
$_REQUEST['yourParam']; 
```
 
 
### 條件判斷
```php=
// if判斷
if(){

}else{

}

if(){

}elseif(){

}else{

}

if($val != null){

}esle{

}

// Ternary 三元運算子
( ) ? :

// switch 要記得使用break
$number = 3;
switch($number){
case 0:
  echo "0";
  break;
case 1:
  echo "1";
  break;
default:
  echo 'default';
  break;
}
```
### 矩陣 Array
矩陣是一種數據結構，PHP中的矩陣類似傳統動態矩陣與雜湊表的結合，PHP稱呼其Indexed Array與Associative Array，一般宣告Indexed Array時，其key值如同傳統矩陣由0開始編號，再進行元素處理時，key的順序不一定會重新排列，這點需要特別注意。
Indexed Array:
```php!
// 宣告 
$arr = array('hello', 2, 'world');
$arr = ['hello', 2, 'world'];
// 操作元素
echo $arr[0];
array_push($array1, 'new');
array_pop($array1);
array_shift($array1);
array_unshift($array1, "apple", "raspberry");
array_splice($array1, 2, 0, ['insert']);

// 去除元素
// 使用unset去除元素會造成空缺
unset($arr[1]); //去除key為1的組合，key=1會造成空缺
$array1 = array_values($array1); // 利用array_values整理出新的矩陣
// 使用array_splice去除元素不會造成空缺
array_splice($arr, 2, 1);
```
Associative Array:
```php
// 宣告 
$array2 = array('name' => 'Jack', 0 => 'number0');
// 操作元素
$array2['online'];
$array2[0];
```
Array 其他的常見操作請參考標題：**PHP 常見函數與使用情境 / Array**
### 迴圈
```php!
while(){

}

do{

}while()

$students = ['Lailai','Jarvis','Elsa','Ian'];
for ($index = 0; $index < count($students); $index++){
  echo '姓名: ' . $students[$index] . '<br/>';
}

foreach($students as $student){
  echo '姓名: ' . $student . '<br/>';
}
                                           
foreach($age as $key => $val){
}
```
可於迴圈中搭配運算子使用：`continue`、`break`

### 巢狀迴圈
```php!
$students = [
  [
    '姓名' => '小明',
    '數學成績' => 30,
    '英文成績' => 40
  ],
  [
    '姓名' => '老王',
    '數學成績' => 83,
    '英文成績' => 30
  ]
];

foreach($students as $student){
  foreach($student as $key => $value){
    
  }
}
```
### 函數與回傳值
```php!
// 無回傳值的函數
function functionNoReturn(){
    echo 'my funtion!';
}
functionNoReturn();

// 有回傳值的函數
function functionWithReturn(){
    return 'my function!';
}
echo functionWithReturn();

// 可回傳Object or Array
function personInfo(){
    return array('Jack', 18, 'Male');
}
list ($name, $age, $gender) = personInfo();

// 有參數的函數
function minusTwo($num1, $num2){
    return $num1 - $num2;
}

// 可指定回傳參數與回傳類型
function addTwo(int $num1, int $num2) : string {
    return strval($num1 + $num2);
}

// Default Argument
function multiplyTwo($num1, $num2 = 0){
    ...
}
```
### Class類別與Object
```php!
class Student{
    public $name;
    public $grades = array();
    public function __construct($name, $mathScore, $englishScore){
      $this->name = $name;
      $this->grades[0] = $mathScore;
      $this->grades[1] = $englishScore;
    }

    public function printAllGrades(){
      foreach($this->grades as $grade){
        echo $grade.'<br/>';
      }
    }
  }
```
使用`require`或`include`引用別的.php檔：
```php!
require "./student.php";
$s1 = new Student('Elsa', 10, 20);
$s2 = new Student('Ian', 40, 80);

$s1->printAllGrades();
```
### 類別常數與靜態方法
```php!
const BR = '<br/>';
...
static public function printHigherMathGrades($student1, $student2){
      $br = self::BR;
      $higherStudent = ($student1->grades[0] > $student2->grades[0]) ? $student1 : $student2;
      echo $higherStudent->grades[0].$br;
    }
```

```php!
echo Student::BR;
Student::printHigherMathGrades($s1, $s2);
```
### 繼承
使用`extends`繼承其他 Class，你也可以使用`parent`呼叫父Class的方法：
```php=
class A {
    function example() {
        echo "I am A::example() and provide basic functionality.\n";
    }
}

class B extends A {
    function example() {
        echo "I am B::example() and provide additional functionality.\n";
        parent::example();
    }
}

$b = new B;
$b->example();

// Output:
// I am B::example() and provide additional functionality.
// I am A::example() and provide basic functionality.

```



---

## PHP 常見函數與使用情境
### Number & Math
```php=
// 四捨五入、無條件進位、無條件捨去、操作到小數第N位
round($num, $precision);
ceil($num);
floor($num);

// 次方、根號、餘數、絕對值、PI
pow($num, $exponent);
$num ** $exponent;
sqrt($num);
$num ** (1/2);
$num % $remainderNum;
abs($num);
pi();

// 數字每位分割轉換成矩陣
array_map('intval', str_split($num));
```
### String
```php=
strval($var);
strlen($str); // 字串長度
str_word_count($str); // 單字數量
$new_str = 'somestring' . $val1 . $val2; // 字串相接

// 字串轉換成矩陣
str_split($str); // char全分割
explode($seperator, $str); // 依照seperator分割

// 找尋特定字串index
strpos($string, $findme, $offset);


// 大小寫轉換：全大寫、全小寫、首字大寫
strtolower($string); // 全大寫
strtoupper($string); // 全小寫
ucfirst($string); // 首字大寫
ucwords($string); // 全單字首字大寫

// 獲取字串部分內容
substr('abcd', 1, 2); // 'bc'
substr('abcd', 1); // 'bcd'
substr('abcd', -2); // 'cd'
substr('abcd', 0, -1); // 'abc'

// 字串開頭or結尾
str_ends_with($str, $needle);
str_starts_with($str, $needle);

// 字串前後填充
// type有：STR_PAD_RIGHT, STR_PAD_LEFT
str_pad($str, $length, $pad_string, $type);

// Unicode：字串轉Unicode、Unicode轉字串
ord($str); // string to unicode
chr($numbr); // unicode to string

substr_count($str, $needle); // 計算字串中的某個字詞的數量

// 替換特定字
str_replace($search, $replace, $subject);
str_replace("%body%", "black", "<body text='%body%'>"); // Provides: <body text='black'>
$vowels = array("a", "e", "i", "o", "u", "A", "E", "I", "O", "U");
str_replace($vowels, "", "Hello World of PHP"); // Provides: Hll Wrld f PHP

preg_replace($pattern, $replace, $subject); // 正則表達式替換
```

### Array
```php=
count($arr); // 矩陣長度
empty($arr); // bool 是否為空[], 0, '0', '', null
implode(',', $array); // 串接成字串，中間夾著特定字串

// 矩陣首位插入、矩陣首位去除、矩陣末位插入、矩陣末位去除
array_unshift($arr, $val);
array_shift($arr);
array_push($arr, $val);
$arr[] = $val;
array_pop($arr);

// 矩陣index：插入元素、去除元素、取代元素
array_splice($arr, $index, $length, $val_arr);
array_splice($arr, $index, 0, $newvals); // 插入多個新元素
array_splice($arr, $index, 1); // 去除1個元素
array_splice($arr, $index, 1, $newval); // 取代1個元素

// 數字矩陣：總和、最大值、最小值、乘積
array_sum($array); // 總和，array裡面可以是字串
max($arr); // 最大
min($arr); // 最小
array_product($array); // 乘積，array裡面可以是字串

// map, filter, reduce
array_map(callback, $arr1, $arr2...);
array_filter($arr, callback); // 回傳的key是會亂掉的
array_reduce($arr, callback, $init); // function($carry, $item){}

// 特定元素是否在矩陣內
in_array($findme, $array);

// 取得特定元素在矩陣內的index
array_search($findme, $arr, $isStrict);

// 元素排列：遞增排列、遞減排列、條件排列
sort($arr); // [1,2,3]
rsort($arr); // [3,2,1]
usort($arr, callback); // user條件排列

// 範圍矩陣
range(1, 12);
range(1,100,10);
range("a","z");

rand($from, $to); // 隨機int(含頭尾)

// 取得所有 keys 或 values 的矩陣
array_keys($arr);
array_values($arr);

...$arr // 展開矩陣
echo json_encode($arr); // 輸出成JSON格式
```
### Others
```php=
gettype($var); // 取得變數類型
is_null($var); // null判斷
bindec("101100"); // binary to decimal
```