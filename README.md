# CI_Excel_import

## Excel 匯入
Codelgniter 目前自身無法使用Excel匯入匯出的功能，必須利用第三方庫phpexcel才能使在Codelgniter中簡易的調用Excel匯出匯入等等的功能。

## Excel 步驟
1 . 下載文件夾(third_party)，並直接覆蓋至application資料夾下。   
2 . 放置完成後，我們依然無法直接調用EXCEL的模組。   
3 . 為了更簡易的使用，在Library下創建我們的Excel.php檔案，內容如下。    
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
最重要的文行即是`require_once APPPATH."/third_party/PHPExcel/PHPExcel.php"`

4.完成以上步驟即可簡易的調用Excel囉~    

