---
layout: post
categories: PHP
title: PHP form-data格式Post多维数组提交实现
meta: form-data格式Post多维数组提交实现
---
* content
{:toc}

### 正文

如果POST中，我们的数据以 `Content-Type:application/json;charset=utf-8` 的方式传递，那多维数组没有问题，但如果设计到文件传输，
就只能有`Content-Type:multipart/form-data; boundary=---------------------------243605601316575381459540562` 的方式传递文件流。

但`multipart/form-data`方式中单个post字段不支持数组，我们要进行封装。

#### Curl封装类

```php
<?php
class Curl
{
    public static function docurlFile($url, $options = [], $params = [], $files = [])
    {
        $options = [
            CURLOPT_URL => $url,
            CURLOPT_TIMEOUT => 30,
            CURLOPT_CONNECTTIMEOUT => 30,
            CURLOPT_RETURNTRANSFER => true,
        ] + $options;
        
        if (stripos($url, "https://") !== false) {
            $options += [
                CURLOPT_SSL_VERIFYPEER => false,
                CURLOPT_SSL_VERIFYHOST => false,
                CURLOPT_SSLVERSION => CURL_SSLVERSION_TLSv1  // ssl2和ssl3等
            ];
        }
        
        $ch = curl_init();
        
        curl_setopt_array($ch, $options);
        
        if (!empty($files)) {
            $customeData = self::customePostFields($params, $files);
            
            @curl_setopt_array($ch, [
                CURLOPT_POST => true,
                CURLOPT_POSTFIELDS => implode("\r\n", $customeData['body']),
                CURLOPT_HTTPHEADER => [
                    "Expect: 100-continue",
                    "Content-Type: multipart/form-data; boundary={$customeData['boundary']}"
                ]
            ]);
        } else {
            curl_setopt_array($ch, [
                CURLOPT_POST => true,
                CURLOPT_POSTFIELDS => http_build_query($params)
            ]);
        }
        
        $result = curl_exec($ch);
        
        $status = curl_getinfo($ch);
        $curl_errno = curl_errno($ch);
        $curl_error = curl_error($ch);
        
        curl_close ($ch);
        
        if (isset($status['http_code']) && $status['http_code'] == 200) {
            return $result;
        }
    
        return false;
    }
    
    private static function customePostFields($params = [], $files = [])
    {
        // invalid characters for "name" and "filename"
        static $disallow = array("\0", "\"", "\r", "\n");
        
        $params = self::postParamsBuild($params);
        
         // build normal parameters
        foreach ($params as $k => $v) 
        {
            $k = str_replace($disallow, "_", $k);
            $body[] = implode("\r\n", [
                "Content-Disposition: form-data; name=\"{$k}\"",
                "",
                filter_var($v),
            ]);
        }
        
        // build file parameters
        foreach ($files as $k => $v) 
        {
            $data = file_get_contents($v['path']);
            $mime = isset($v['mime']) ? $v['mime'] : 'application/octet-stream';
            $body[] = implode("\r\n", [
                "Content-Disposition: form-data; name=\"{$k}\"; filename=\"{$v['name']}\"",
                "Content-Type: {$mime}",
                "",
                $data,
            ]);
        }
        
        // generate safe boundary
        do {
            $boundary = "---------------------" . md5(mt_rand() . microtime());
        } while (preg_grep("/{$boundary}/", $body));

        // add boundary for each parameters
        array_walk($body, function (&$part) use ($boundary) {
            $part = "--{$boundary}\r\n{$part}";
        });

        // add final boundary
        $body[] = "--{$boundary}--";
        $body[] = "";
        
        return [
            'boundary' => $boundary,
            'body' => $body
        ];
    }
    
    /**
     * post参数构建，涉及到多维数组
     * 
     */
    private static function postParamsBuild(array $params = [], $numericPrefix = '', $argSeparator = '&', $keyPrefix = '')
    {
        $arr = array();
        foreach ($params as $key => $val) 
        {
            if ($val === NULL) {
                continue;
            }
            if (!is_array($val) && !is_object($val)) {
                if (is_bool($val)) {
                    $val = $val ? 1 : 0;
                }
                if ($keyPrefix === '') {
                    if (is_int($key)) {
                        $arr[$numericPrefix . $key] = $val;
                    } else {
                        $arr[$key] = $val;
                    }
                } else {
                    $arr[$keyPrefix . '[' . $key . ']'] = $val;
                }
            } else {
                if ($keyPrefix === '') {
                    $newKeyPrefix = $key;
                } else {
                    $newKeyPrefix = $keyPrefix . '[' . $key . ']';
                }
                
                $arr += call_user_func_array(__METHOD__, array($val, $numericPrefix, $argSeparator, $newKeyPrefix));
            }
        }
        
        return $arr;
    }
}
```

#### 提交实现

```php
$postData['name'] = 'abc';
$postData['month'] = 3;

$files = [
    'upload' => [
        "path" => '/tmp/23232323',
        "mime" => "application/octet-stream",
        "name" => 'a1.xls'
    ]
];

$url = 'http://www.ctmdl.com/post/upload';

$ret = Curl::docurlFile($url, [], $postData, $files);
```

customePostFields() 封装后的数据：
```
-----------------------------243605601316575381459540562
Content-Disposition: form-data; name="name"

abc
-----------------------------243605601316575381459540562
Content-Disposition: form-data; name="month"

3
-----------------------------243605601316575381459540562
Content-Disposition: form-data; name="upload"; filename="a1.xls"
Content-Type: application/wps-office.xls

（一段文件流乱码，如Ïà¡±á      À      F>  þÿ                   !                 þÿÿÿ    #   ÿÿÿÿÿÿÿÿÿÿÿÿÿÿÿÿÿÿÿÿÿÿÿÿÿÿÿÿ）
-----------------------------243605601316575381459540562--
```

如果我们使用这个数据追踪看一下：
```
$postData['name']['a'] = 'abc';
$postData['name']['b']['a'] = 'edf';
$postData['name']['b']['b'] = 'ghi';
$postData['month'] = 3;

// http_build_query() 后结果：
name%5Ba%5D=abc&name%5Bb%5D%5Ba%5D=edf&name%5Bb%5D%5Bb%5D=ghi&month=3 
// name[a]=abc&name[b][a]=edf&name[b][b]=ghi&month=3

// postParamsBuild() 的结果应该是：
array(4) {
  ["name[a]"]=>
  string(3) "abc"
  ["name[b][a]"]=>
  string(3) "edf"
  ["name[b][b]"]=>
  string(3) "ghi"
  ["month"]=>
  int(3)
}
```

customePostFields() 封装后的数据：
```
-----------------------80544fd9473fe265063effc7264e5dd8
Content-Disposition: form-data; name="name[a]"

abc
-----------------------80544fd9473fe265063effc7264e5dd8
Content-Disposition: form-data; name="name[b][a]"

edf
-----------------------80544fd9473fe265063effc7264e5dd8
Content-Disposition: form-data; name="name[b][b]"

ghi
-----------------------80544fd9473fe265063effc7264e5dd8
Content-Disposition: form-data; name="month"

3
-----------------------80544fd9473fe265063effc7264e5dd8--
```

<br/><br/><br/><br/><br/>
### 参考资料

Content-Disposition <https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Disposition>

Web开发技术 > HTTP > HTTP Headers > Content-Type <https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type>

