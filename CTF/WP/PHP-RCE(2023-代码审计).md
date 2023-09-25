Web 1:
```

<?php

$action = $_GET['action'];
$parameters = $_GET;
if (isset($parameters['action'])) {
    unset($parameters['action']);
}

$a = call_user_func($action, ...$parameters);


```


Web 2:
```

<?php

$action = $_GET['action'];
$parameters = $_GET;
if (isset($parameters['action'])) {
    unset($parameters['action']);
}

call_user_func($action, $parameters)($_POST['a'])($_POST['b']);

```




Web3:
```
<?php
$action = $_GET['action'];
$parameters = $_GET;
if (isset($parameters['action'])) {
    unset($parameters['action']);
}

call_user_func($action, $parameters);


if(count(glob(__DIR__.'/*'))>3){
    readfile('flag.txt');
}

?>
```





Web4:
```
<?php
Class A{
    static function f(){
        system($_POST['a']);
    }
}


$action = $_GET['action'];
$parameters = $_GET;
if (isset($parameters['action'])) {
unset($parameters['action']);
}

call_user_func($action, $parameters);


```


Web5:
```

<?php
Class A{
    static function f(string $a){
        system($a);
    }
}


$action = $_GET['action'];
$parameters = $_GET;
if (isset($parameters['action'])) {
unset($parameters['action']);
}

call_user_func($action, $parameters);
echo $_POST['a'];

```


