---
layout: post
categories: PHP
title: PhpSpreadsheet Excel文件导入使用说明
meta: Excel文件导入，以前可以用PHPExcel，但这个库已经不维护了，现在改为PhpSpreadsheet库了，所以下面说说这个库怎么导入Excel文件。
---
* content
{:toc}

## 正文

Excel文件导入，以前可以用PHPExcel，但这个库已经不维护了，现在改为PhpSpreadsheet库了，所以下面说说这个库怎么导入Excel文件。

phpoffice/phpspreadsheet 包 Packagist：<https://packagist.org/packages/phpoffice/phpspreadsheet> ，
官方文档：<https://phpspreadsheet.readthedocs.io/en/latest/> , 或 <https://phpoffice.github.io/PhpSpreadsheet/>

我们前端使用ajax-upload把Excel文件导入后端：

![]({{site.baseurl}}/images/20190226/20190226172911.jpg)

我们看一下后端的处理，用的框架是Yii2：

composer中引入phpspreadsheet：
```
"phpoffice/phpspreadsheet": "1.5.2",
```

控制器部分：
```php
<?php
use ClientLogic;

class DoController {
    /**
     * 客户导入操作
     * @return string
     */
    public function actionClientImportDo()
    {
        if ($_FILES['clientExcel']) {
            $file = $_FILES['clientExcel']['tmp_name'];

            $res = ClientLogic::clientImport($file);

            return json_encode($res);
        }

        return json_encode([
            'code' => 2010,
            'msg' => '未选择文件',
            'data' => [],
        ]);
    }
    
     /*其他方法*/
}
```

逻辑处理层：
```php
<?php
use LReadExcel;

class ClientLogic {
    /**
     * 客户导入
     * @param string $file
     * @return array
     */
    public static function clientImport($file = '')
    {
        if (empty($file) || !file_exists($file)) {
            return [
                'code' => 2010,
                'msg' => '文件不存在',
                'data' => []
            ];
        }

        $data = LReadExcel::readExcelToArray($file);
        unset($data[0]);

        $succ = 0;
        $fail = count($data);

        foreach ($data as $value)
        {
            $params['name'] = $value[0];
            if ($value[1] == '男') {
                $params['sex'] = 10;
            } elseif ($value[1] == '女') {
                $params['sex'] = 20;
            } else {
                $params['sex'] = 1;
            }
            $params['birthday'] = $value[2];
            ...
            $params['status'] = 10;
            $params['updated_time'] = time();
            $params['updated_user_id'] = Yii::$app->user->id;

            $res = WClientDao::saveClientInfo( $params );
            if ( $res['code'] == 200 ) {
                $succ++;
                $fail--;
            } else {
                $succ--;
                $fail++;
            }
        }

        return [
            'code' => 200,
            'msg' => 'ok',
            'data' => [
                'succ' => $succ,
                'fail' => $fail
            ]
        ];
    }
    
    /*其他方法*/
}
```

Excel读取组件层：
```php
<?php
use PhpOffice\PhpSpreadsheet\Reader\Xlsx;

/**
 * 读取Excel类
 * Class LReadExcel
 * @package common\misc
 */
class LReadExcel
{
    /**
     * 读取Excel为数组
     * @param $file
     * @return array
     */
    public static function readExcelToArray($file)
    {
        return self::readExcel($file)->getSheet(0)->toArray();
    }

    /**
     * 读取Excel
     * @param $file
     * @return \PhpOffice\PhpSpreadsheet\Spreadsheet
     */
    public static function readExcel($file)
    {
        $reader = new Xlsx();
        $reader->setReadDataOnly(true);
        $spreadsheet = $reader->load($file);

        return $spreadsheet;
    }
}
```

可以看到Excel文件核心读取处理是在 LReadExcel 类中，读取为数组结构返回过去。

<br/><br/><br/><br/><br/>
## 参考资料

PHPOffice/PhpSpreadsheet <https://github.com/PHPOffice/PhpSpreadsheet>

scepterscythe/ajax-upload <https://github.com/scepterscythe/ajax-upload>

Ajax Upload 文件上传 <https://blog.csdn.net/teresa502/article/details/7952486>

<https://blog.csdn.net/dunegao/article/details/78987016>

基于HTML5的jquery文件上传插件 <http://www.jq22.com/jquery-info2247>

<https://www.cnblogs.com/abel/archive/2013/07/24/3213409.html>

<https://blog.csdn.net/w36680130/article/details/81665035>

<https://www.cnblogs.com/7mile/p/3156942.html>

基于HTML5的可预览多图片Ajax上传 <https://www.zhangxinxu.com/wordpress/2011/09/%E5%9F%BA%E4%BA%8Ehtml5%E7%9A%84%E5%8F%AF%E9%A2%84%E8%A7%88%E5%A4%9A%E5%9B%BE%E7%89%87ajax%E4%B8%8A%E4%BC%A0/>

