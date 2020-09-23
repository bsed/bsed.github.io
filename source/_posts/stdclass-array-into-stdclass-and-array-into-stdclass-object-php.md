---
title: PHP stdClass 转化为 Array 以及 Array 转化为 stdClass – stdClass Object
date: 2012-03-05 21:03:00
updated: 2015-07-08 21:04:59
tags: 
- Display
categories: 
- css

---
在开发中会遇到很多array转stdClass Objects 以及stdClass Objects 转成array,最常见的就是json数据的解析，比如

`$arr = json_decode($str);`

这样得到的$arr 其实一个stdClass对象，不能直接做数组用。不过这个可以有快捷的解决方法

`$arr = json_decode($str,true);`


<!--more-->


这样得到的$arr就是array了。

这是比较常见的场景，那么当处于其他的情况下呢？比如当try catch时，如下：	

    try {
    				$request->check();
    			} catch (Exception $e) {
    				$result->code = $e->getCode();
    				$result->msg = $e->getMessage();
    				return $result;
    			}

这样返回的$result是一个stdClass object,这就需要其他的方式将其转成array了。于是可以用以下方法：

	function objectToArray($d) {
		if (is_object($d)) {
			// Gets the properties of the given object
			// with get_object_vars function
			$d = get_object_vars($d);
		}

		if (is_array($d)) {
			/*
			* Return array converted to object
			* Using __FUNCTION__ (Magic constant)
			* for recursive call
			*/
			return array_map(__FUNCTION__, $d);
		}
		else {
			// Return array
			return $d;
		}
	}

 

那么反着转呢？即array转到stdClass 对象

    <?php
    
    	function arrayToObject($d) {
    		if (is_array($d)) {
    			/*
    			* Return array converted to object
    			* Using __FUNCTION__ (Magic constant)
    			* for recursive call
    			*/
    			return (object) array_map(__FUNCTION__, $d);
    		}
    		else {
    			// Return object return $d;
    		}
    	}
    
    ?>

接下来是一个完整的实例：

	// Create new stdClass Object
	$init = new stdClass;

	// Add some test data
	$init->foo = "Test data";
	$init->bar = new stdClass;
	$init->bar->baaz = "Testing";
	$init->bar->fooz = new stdClass;
	$init->bar->fooz->baz = "Testing again";
	$init->foox = "Just test";

	// Convert array to object and then object back to array
	$array = objectToArray($init);
	$object = arrayToObject($array);

	// Print objects and array
	print_r($init);
	echo "\n";
	print_r($array);
	echo "\n";
	print_r($object);

输出：

    stdClass Object
    (
        [foo] => Test data
        [bar] => stdClass Object
            (
                [baaz] => Testing
                [fooz] => stdClass Object
                    (
                        [baz] => Testing again
                    )
    
            )
    
        [foox] => Just test
    )
    
    Array
    (
        [foo] => Test data
        [bar] => stdClass Object
            (
                [baaz] => Testing
                [fooz] => stdClass Object
                    (
                        [baz] => Testing again
                    )
    
            )
    
        [foox] => Just test
    )
    
    stdClass Object
    (
        [foo] => Test data
        [bar] => stdClass Object
            (
                [baaz] => Testing
                [fooz] => stdClass Object
                    (
                        [baz] => Testing again
                    )
    
            )
    
        [foox] => Just test
    )

 


