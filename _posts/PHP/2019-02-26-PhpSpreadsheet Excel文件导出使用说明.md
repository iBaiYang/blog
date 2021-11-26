---
layout: post
categories: PHP
title: PhpSpreadsheet Excel文件导出使用说明
meta: 上面说了Excel文件导入，下面说一下Excel文件导出
---
* content
{:toc}

### 正文

控制器部分：
```
    // 读取到的数据
    $data = [
        [...],
        [...],
    ];

    // 准备excel数据
    $data_new = [];
    foreach( $data as $key => $value )
    {
        $data_new[$key]['id'] = $value['id'];
        $data_new[$key]['name'] = $value['name'];
        $data_new[$key]['sex_desc'] = $value['sex_desc'];
        $data_new[$key]['birthday'] = $value['birthday'];
        $data_new[$key]['home_address'] = $value['home_address'];
        $data_new[$key]['status_desc'] = $value['status_desc'];
        $data_new[$key]['created_user_name'] = $value['created_user_name'];
        $data_new[$key]['created_date'] = date('Y-m-d H:i:s',$value['created_time']);
        $data_new[$key]['updated_user_name'] = $value['updated_user_name'];
        $data_new[$key]['updated_date'] = date('Y-m-d H:i:s',$value['updated_time']);
    }

    $title = '客户列表';
    $fileName = $title . '.xlsx';

    $columnMap = ['ID', '姓名', '性别', '生日', '家庭地址', '状态', '添加人', '添加时间', '更新人', '更新时间'];

    $fileName = LPhpSpreadsheet::getExcel( $title, $data_new, $columnMap, $fileName, $is_browserDown = 1, $width = 16 );
```

common\misc\LPhpSpreadsheet.php文件：
```php
<?php
namespace common\misc;

use PhpOffice\PhpSpreadsheet\Spreadsheet;
use PhpOffice\PhpSpreadsheet\Writer\Xlsx;

class LPhpSpreadsheet
{
    /**
     * 获取excel
     * 浏览器下载/在服务器保存
     * @param $title
     * @param $data
     * @param $columnMap
     * @param string $fileName
     * @param int $is_browserDown
     * @param int $width
     * @return string
     */
    public static function getExcel( $title, $data, $columnMap, $fileName = '', $is_browserDown = 1, $width = 0 )
    {
        $spreadsheet = new Spreadsheet();
        $spreadsheet->getProperties()
            ->setCreator("tmk")
            ->setLastModifiedBy("tmk")
            ->setTitle( $title )
            ->setSubject( $title )
            ->setDescription( "" )
            ->setKeywords( "" )
            ->setCategory( "" );

        array_unshift($data, $columnMap);

        $sheet = $spreadsheet->getActiveSheet();
        $sheet->fromArray($data, null, 'A1');

        // 设置列宽
        $toCol = $sheet->getColumnDimension($sheet->getHighestColumn())->getColumnIndex();
        if ( empty($width) ) {
            for($i = 'A'; $i <= $toCol; $i++) {
                $sheet->getColumnDimension($i)->setAutoSize(true);
            }
            $sheet->calculateColumnWidths();
        } else {
            for($i = 'A'; $i <= $toCol; $i++) {
                $sheet->getColumnDimension($i)->setWidth($width);
            }
        }

        $writer = new Xlsx($spreadsheet);

        // 浏览器下载/在服务器保存
        if ( $is_browserDown ) {
            $fileName = empty($fileName) ? time() .rand(10000, 99999) .".xlsx" : $fileName;

            header('Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet');  // 告诉浏览器输出07Excel文件
//        header('Content-Type:application/vnd.ms-excel');  // 告诉浏览器将要输出Excel03版本文件
            header('Content-Disposition: attachment;filename=' .$fileName);  // 告诉浏览器输出浏览器名称
            header('Cache-Control: max-age=0');  // 禁止缓存

            $writer->save('php://output');
        } else {
            $fileName = empty($fileName) ? "/tmp/". time() . rand(10000, 99999) .".xlsx" : $fileName;

            $writer->save( $fileName );
        }

        // 清楚内存
        $spreadsheet->disconnectWorksheets();
        unset($spreadsheet);

        return $fileName;
    }
}
```

#### sql表保存为Excel

有时我们会向外展示数据库表结构，我们可以直接使用数据库管理工具把指向的数据库表结构导出，导出数据demo.sql文件内容如：
```
/*
Navicat MySQL Data Transfer

Source Server         : 测试环境
Source Server Version : 50720
Source Host           : 127.0.0.1:3306
Source Database       : demo

Target Server Type    : MYSQL
Target Server Version : 50720
File Encoding         : 65001

Date: 2020-09-08 10:54:43
*/

SET FOREIGN_KEY_CHECKS=0;

-- ----------------------------
-- Table structure for activity
-- ----------------------------
DROP TABLE IF EXISTS `activity`;
CREATE TABLE `activity` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `unique_id` varchar(50) NOT NULL COMMENT '活动唯一标识',
  `title` varchar(50) NOT NULL COMMENT '活动名称',
  `start_time` int(11) NOT NULL COMMENT '活动开始时间',
  `stop_time` int(11) NOT NULL COMMENT '活动结束时间',
  `is_delete` tinyint(2) NOT NULL DEFAULT '0' COMMENT '是否删除',
  `rules` text NOT NULL COMMENT '领奖规则',
  `remark` varchar(2048) NOT NULL DEFAULT '' COMMENT '备注',
  `time_create` int(11) NOT NULL COMMENT '创建时间',
  `time_update` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uniq_id` (`unique_id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=17 DEFAULT CHARSET=utf8mb4 COMMENT='活动表';

-- ----------------------------
-- Table structure for activity_award
-- ----------------------------
DROP TABLE IF EXISTS `activity_award`;
CREATE TABLE `activity_award` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `activity_id` int(11) NOT NULL COMMENT '活动ID',
  `award_type` tinyint(2) NOT NULL DEFAULT '0' COMMENT '奖励类型  0 虚拟奖 1 实物奖 ',
  `name` varchar(50) NOT NULL COMMENT '奖品名称',
  `worth` float(5,2) NOT NULL COMMENT '价值金额',
  `all_count` int(11) NOT NULL DEFAULT '0' COMMENT '奖品数量总数',
  `count` int(11) NOT NULL DEFAULT '0' COMMENT '库存数量',
  `chance` float(5,2) NOT NULL COMMENT '中奖概率',
  `pic_urls` varchar(300) NOT NULL DEFAULT '' COMMENT '图片URL',
  `rules` text NOT NULL COMMENT '领奖规则',
  `remark` varchar(2048) NOT NULL DEFAULT '' COMMENT '奖品说明',
  `is_delete` tinyint(2) NOT NULL DEFAULT '0' COMMENT '是否删除',
  `time_create` int(10) NOT NULL COMMENT '创建时间',
  `time_update` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=27 DEFAULT CHARSET=utf8mb4 COMMENT='活动奖品表';
```

可能我们有跟进一步的需求，需要把这个库结构转存为更加易看的Excel表格，看一下处理代码：
```php
<?php
/**
 * 数据库转为Excel
 * @param string $filePath
 * @return bool
 * @throws \PhpOffice\PhpSpreadsheet\Exception
 * @throws \PhpOffice\PhpSpreadsheet\Writer\Exception
 */
public function actionTableToExcel($filePath = 'demo.sql')
{
    if (!file_exists($filePath)) {
        echo  "file is not exist: " .$filePath ."\r\n";
        return true;
    }

    $fileInfo = file_get_contents($filePath);
    preg_match_all("/CREATE TABLE[\d\D]*;[\r]?\n/U", $fileInfo, $matches);

    if (count($matches[0]) == 0) {
        echo "未匹配到数据\r\n";
        return true;
    }

    // 准备excel数据
    $excel_data = [];

    foreach ($matches[0] as $item)
    {
        // 表数据
        $table_data = [];
        // 表头数据
        $table_name_data = [];
        // 表列数据
        $table_column_data[] = ["字段", "数据类型与长度", "字段备注"];

        $explodedArray = explode("\n  ", $item);

        foreach ($explodedArray AS $key => $value)
        {
            if ($key == 0 || $key == (count($explodedArray) -1)) {
                /*分析表名及注释*/
                // 分析表名
                if ($key == 0) {
                    preg_match("/`.+`/", $value, $table_name);
                    $table_name = str_replace("`", "", $table_name[0]);

                    array_push($table_name_data, $table_name);
                }

                // 分析表名注释
                if ($key == count($explodedArray) -1) {
                    preg_match("/COMMENT='.*';/", $value, $table_name_comment);
                    if (!empty($table_name_comment[0])) {
                        $table_name_comment = str_replace("COMMENT='", "", $table_name_comment[0]);
                        $table_name_comment = str_replace("';", "", $table_name_comment);
                    } else {
                        $table_name_comment = "";
                    }

                    array_push($table_name_data, $table_name_comment);
                }
            } else {
                /*分析字段名及注释*/
                preg_match("/^`.+`/", $value, $column_name);
                if (!isset($column_name[0])) {
                    continue;
                }

                $column_name = str_replace("`", "", $column_name[0]);

                preg_match("/` [\S]+/", $value, $column_type);
                $column_type = str_replace("` ", "", $column_type[0]);

                preg_match("/COMMENT '.+'/", $value, $column_comment);
                if (!empty($column_comment[0])) {
                    $column_comment = str_replace("COMMENT '", "", $column_comment[0]);
                    $column_comment = str_replace("'", "", $column_comment);
                } else {
                    $column_comment = "";
                }

                $table_column_data[] = [$column_name, $column_type, $column_comment];
            }
        }

        array_push($table_data, ["表名", "表名备注"], $table_name_data);
        $table_data = array_merge($table_data, $table_column_data);
        $excel_data = array_merge($excel_data, $table_data, [[]], [[]]);

        unset($table_data, $table_name_data, $table_column_data);
    }

    /*导出为excel*/
    $data = $excel_data;
    $title = explode('.',$filePath)[0];

    $spreadsheet = new \PhpOffice\PhpSpreadsheet\Spreadsheet();
    $spreadsheet->getProperties()
        ->setCreator("vip")
        ->setLastModifiedBy("vip")
        ->setTitle( $title )
        ->setSubject( $title )
        ->setDescription( "" )
        ->setKeywords( "" )
        ->setCategory( "" );

    $sheet = $spreadsheet->getActiveSheet();
    $sheet->fromArray($data, null, 'A1');

    // 设置单元格样式
    foreach ($sheet->getRowIterator() as $row)
    {
        $cellIterator = $row->getCellIterator();
        foreach ($cellIterator as $cell) {
            $txt = $cell->getValue();
            if ($txt == "表名" || $txt == "表名备注" || $txt == "字段" || $txt == "数据类型与长度" || $txt == "字段备注") {
                // 设置字体加粗
                $cell->getStyle()->getFont()->setBold(true);
                // 设置背景色
                $cell->getStyle()->getFill()->setFillType(\PhpOffice\PhpSpreadsheet\Style\Fill::FILL_SOLID)->getStartColor()->setARGB('e9e9e9');
                // 设置行高
                $sheet->getRowDimension($cell->getRow())->setRowHeight(20);
            }
        }
    }

    // 设置列宽
    $toCol = $sheet->getColumnDimension($sheet->getHighestColumn())->getColumnIndex();
    for($i = 'A'; $i <= $toCol; $i++) {
        $sheet->getColumnDimension($i)->setAutoSize(true);
    }
    $sheet->calculateColumnWidths();

    // 保存
    $fileName = "./" .$title .".xlsx";
    // 保存为Excel
    $writer = new \PhpOffice\PhpSpreadsheet\Writer\Xlsx($spreadsheet);
    $writer->save( $fileName );
    // 保存为pdf
    $writer = new \PhpOffice\PhpSpreadsheet\Writer\Pdf\Tcpdf($spreadsheet);
    $writer->save( $fileName );

    // 清除内存
    $spreadsheet->disconnectWorksheets();
    unset($spreadsheet);

    echo "成功：" .$title .".xlsx\r\n";
    return true;
}
```

里面用到的多起正则匹配，需要把正则搞清楚。

<br/><br/><br/><br/><br/>
### 参考资料

PHPOffice/PhpSpreadsheet官方文档 <https://phpspreadsheet.readthedocs.io/en/stable/>

PHPOffice/PhpSpreadsheet <https://github.com/PHPOffice/PhpSpreadsheet>

PhpSpreadsheet 设置单元格样式 <https://www.itzxf.com/article/434.html>

使用PhpSpreadsheet的一些格式记录 <https://blog.csdn.net/qq_32080545/article/details/91803806>



