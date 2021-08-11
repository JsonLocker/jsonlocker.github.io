---
title: Tesseract-OCR
date: 2021-08-11 10:37:08
categories: php
tags:
    - ocr
    - 文字识别
    - tesseract
---


## 1. install leptonica

切换到`root`角色

```
wget http://www.leptonica.org/source/leptonica-1.78.0.tar.gz
tar -xzvf leptonica-1.78.0.tar.gz
cd leptonica-1.78.0
./configure
make && make install
export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig/
```

## 2. install Tesseract-OCR


```
wget https://codeload.github.com/tesseract-ocr/tesseract/tar.gz/4.1.0
tar -xvf 4.1.0
cd tesseract-4.1.0/
./autogen.sh
./configure
make && make install
sudo ldconfig
```

## 3. Download language lib

[阿里云盘](https://www.aliyundrive.com/s/MFac1PTmF7f/folder/611336faed54d9326b1545debb194f72600a0c72)
```
cd /usr/local/share/tessdata/
wget https://github.com/tesseract-ocr/tessdata/raw/master/eng.traineddata
wget https://github.com/tesseract-ocr/tessdata/raw/master/chi_sim.traineddata
```

## 4. test

```
tesseract imagename out -l eng/chi_sim

```


## 5. PHP OCR

[github](https://github.com/thiagoalessio/tesseract-ocr-for-php)

```
composer require thiagoalessio/tesseract_ocr

use thiagoalessio\TesseractOCR\TesseractOCR;
echo (new TesseractOCR('text.png'))
	->lang('chi_sim','eng')  //对应字体库文件名
    ->run();

```

**补充说明**

中文识别率不高,需要另外单独进行字库识别训练.


## 6. errors

- 执行 ./autogen.sh 报错

```
./autogen.sh:行59: bail_out: 未找到命令
./autogen.sh:行82: aclocal: 未找到命令

# 处理办法

yum install automake -y
yum install libtool -y

```


- tesseract make 时报错

```
libtool: Version mismatch error.  This is libtool 2.4.6, but the
libtool: definition of this LT_INIT comes from libtool 2.4.2.
libtool: You should recreate aclocal.m4 with macros from libtool 2.4.6
libtool: and run autoconf again.


# 处理办法

执行 autoreconf -ivf 命令
```


- 安装完成后执行命令报错

```
$ tesseract 13.jpg result -l chi_sim

Error in pixReadMemTiff: function not present
Error in pixReadMem: tiff: no pix returned
Error in pixaGenerateFontFromString: pix not made
Error in bmfCreate: font pixa not made
Error opening data file /usr/local/share/tessdata/eng.traineddata
Please make sure the TESSDATA_PREFIX environment variable is set to your "tessdata" directory.
Failed loading language 'eng'
Tesseract couldn't load any languages!
Could not initialize tesseract.

# 处理办法

1. 下载预训练文件
2. 将训练文件放至 /usr/local/share/tessdata 目录

下载地址：https://github.com/tesseract-ocr/tessdata
chi_sim.traineddata  中文
eng.traineddata      英文
enm.traineddata      数字
```

- 只需make 报错

```
configure: error: Leptonica 1.74 or higher is required. Try to install libleptonica-dev package.

# 处理办法

export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig/
```

