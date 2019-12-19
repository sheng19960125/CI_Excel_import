# CI_Excel_import

## Excel 匯入
Codelgniter 目前自身無法使用Excel匯入匯出的功能，必須利用第三方庫phpexcel才能使在Codelgniter中簡易的調用Excel匯出匯入等等的功能。

## Excel 步驟
1 . 下載文件夾 ( third_party ) ， 並直接覆蓋至 application 資料夾下。   
2 . 放置完成後，我們依然無法直接調用EXCEL的模組。   
3 . 為了更簡易的使用，在 Library 下創建我們的 Excel.php 檔案，內容如下。    
```
<?php

if ( ! defined('BASEPATH')) exit('No direct script access allowed');

require_once APPPATH."/third_party/PHPExcel/PHPExcel.php";

class Excel extends PHPExcel {
	
	public function __construct() {
		
		parent::__construct();
    
	}
}
?>
```
最重要的文行即是 `require_once APPPATH."/third_party/PHPExcel/PHPExcel.php"`		

4 . 完成以上步驟即可簡易的調用Excel囉~   
5 . 為了更方便的讓我們使用，將`Model`內新增， Exceldata_model.php 內容如下。		
```

/**
   * 匯入文件
   *
   * @param [type] $file = 文件位置 例如: 'uploads/會員資料.xlsx';
   * @param [type] $type = 輸出類型 1/文件欄位名稱,2/文件內容,3/整份文件
   * @return array:整個EXL轉成陣列資料
   */

public function import_documents($file , $type = 3){
   
    //read excel file from path
    $objPHPExcel		= PHPExcel_IOFactory::load($file);

    //read excel file
    $cell_collection	= $objPHPExcel->getActiveSheet()->getCellCollection();

    //extract to a PHP readable array format
    foreach ($cell_collection as $cell) {
      $column		= $objPHPExcel->getActiveSheet()->getCell($cell)->getColumn();
      $row 		= $objPHPExcel->getActiveSheet()->getCell($cell)->getRow();
      $data_value = $objPHPExcel->getActiveSheet()->getCell($cell)->getValue();

      if($row == 1)$header[$row][$column]	= $data_value;//標題

      if($row != 1)$arr_data[$row][$column] 	= $data_value;//檔案內容

      $data[$row][] = $data_value;//整個文件轉成陣列
    }

    if($type == 3)return $data;
    if($type == 2)return $arr_data;
    return $header;
  }
}

```

## Excel 匯入新建使用者 推薦圖 訂單
1 . 確認用戶匯入`Excel`的資料，各個專案所需的資料一定相對不一樣，先去詢問客戶的新建需求我們再來做相對應的匯入。		
2 . 目前總共分為三大部分，匯入並新建使用者資訊，接著新建位階圖及訂單資訊。
3 . 匯入部分，每次匯入必須建立他的Excel檔至專案內，以方便以後的參考。		
```
if($_FILES['files']['error']==0 ){
	$post_data['files'] = $this->allfun->upload_xlsx($_FILES['files']);
}
$file = './uploads/xlsx/'.$post_data['files'].'.xlsx';
```
獲取完成後，建立資料夾uploads/xlsx/下建立檔案。	

調用後，獲取 Excel 匯入格式如下。		
```
[2] => Array(
        [A] => 1
        [B] => user001
        [C] => user000
)
[3] => Array(
        [A] => 2
        [B] => user002
        [C] => user001
)
[4] => Array(
    	[A] => 3
        [B] => user003
        [C] => user001
)
```
分別'A','B','C'就是Excel相對應的資訊欄位		
		
3 . 新建使用者部分，取得excel文件內容並新增，`$this->yu->import_documents($excel);`利用回傳 excel 直接新建使用者資訊。
```
foreach($excel as $items){
	$user_id = $this->insert($items);
}
```

