# payping for laravel 11 - 12
درگاه پرداخت پی پینگ برای نسخه های جدید لاراول

payment and oauth2 request library for payping

## نحوه استفاده

## 📦 نصب پکیج

```bash
composer require farbodtf/payping
```

### درخواست پرداخت
```php
$token = "توکن اختصاصی ";
$args = [
        "amount" => میلغ تراکنش,
    "payerIdentity" => "شناسه کاربر در صورت وجود",
    "payerName" => "نام کاربر پرداخت کننده",
    "description" => "توضیحات",
    "returnUrl" => "آدرس برگشتی از سمت درگاه",
    "clientRefId" => "شماره فاکتور"
];

$payment = new \PayPing\Payment($token);

try {
    $payment->pay($args);
} catch (Exception $e) {
    var_dump($e->getMessage());
}
//echo $payment->getPayUrl();

header('Location: ' . $payment->getPayUrl());
```

### تایید پرداخت
```php
$token = "توکن اختصاصی";

$payment = new \PayPing\Payment($token);

try {
    if($payment->verify($_GET['refid'], 100)){
        echo "success";
    }else{
        echo "fail";
    }
} catch (Exception $e) {
    echo $e->getMessage();
}
``` 
### بررسی ثبت نام کاربر بر اساس ایمیل
```php

$token = "توکن اختصاصی";

$register = new \PayPing\Register($token);

var_dump($register->checkEmail("ایمیل مورد نظر"));

```
### ثبت نام کاربر در پی پینگ
```php
$token = "توکن اختصاصی";

$register = new \PayPing\Register($token);

$params = [
    "UserName" => "نام کاربری",
    "Email" => "ایمیل - اجباری",   
    "FirstName" => "نام کوچک",
    "LastName" => "نام خانوادگی",
    "PhoneNumber" => "شماره تلفن",
    "NationalCode" => "کد ملی",
    "BirthDay" => "تاریخ تولد",
    "Sheba" => "شناسه شبا"
];
try {
    $register_id = $register->registerUser("آدرس برگشتی هنگام تکمیل ثبت نام در پی پینگ", $params);
  header("Location: ".$register->getRegisterUrl());
}catch (Exception $e){
    echo $e->getMessage();
}

```

### ورود کاربران توسط oauth2
```php
$client_id = "client-id"; 
$client_secret = "client-secret"; 
$codeVerify = 'یک  شناسه یکتا به ازای هر کاربر ';//generateVerified();
$callback = "آدرس برگشتی هنگام ورود در پی پینگ";
$scope = [ //دسترسی های مورد نیاز از پنل پی پینگ 
   PayPing\Scopes::OPENID
   //, ...
];

$state = ["مقادیری که بعد از ثبت نام و برگشت نیاز دارید"];


try {
    $auth = new PayPing\Authorization($client_id, $client_secret, $callback, $codeVerify);
    echo '<a href="' . $auth->getAuthLink($scope,$state) . '" >login with payping</a>' . "<br>";
} catch (Exception $exception) {
    echo $exception->getMessage();
}
```

### تایید ورود در پنل پی پینگ
```php

$client_id = "client-id"; 
$client_secret = "client-secret"; 
$codeVerify = 'یک  شناسه یکتا به ازای هر کاربر که در مرحله قبل ساخته شده است ';
$callback = "آدرس برگشتی هنگام ورود در پی پینگ";
$auth = new PayPing\Authorization($client_id,$client_secret,$callback,$codeVerify);
try {
	echo '<pre>';
    print_r($auth->getAccessToken($callback, $codeVerify));
} catch (Exception $exception) {
    echo $exception->getMessage();
}
```
