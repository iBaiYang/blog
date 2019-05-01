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
```
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

<br/><br/><br/><br/><br/>
### 参考资料

PHPOffice/PhpSpreadsheet <https://github.com/PHPOffice/PhpSpreadsheet>







