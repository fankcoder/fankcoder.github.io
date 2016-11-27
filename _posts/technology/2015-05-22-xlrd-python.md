---
layout: post
title: 用xlrd\xlwt读写excel
category: 技术
tags: python
description:
---

### 首先安装xlrd\xlwt模块

xlrd模块下载地址:

https://pypi.python.org/pypi/xlrd

xlwt模块下载地址:

https://pypi.python.org/pypi/xlwt

linux 安装命令 

```
python setup.py install
```

windos 安装命令

```
setup.py install
```
 

### 使用方法

#### xlrd
打开excel

```
import xlrd
data = xlrd.open_workbook('demo.xls') #注意这里的workbook首字母是小写
```

查看文件中包含sheet的名称

```
data.sheet_names()
```

得到第一个工作表，或者通过索引顺序 或 工作表名称

```
table = data.sheets()[0]
table = data.sheet_by_index(0)
table = data.sheet_by_name(u'Sheet1')
```

获取行数和列数

```
nrows = table.nrows
ncols = table.ncols
```

获取整行和整列的值（数组）

```
table.row_values(i)
table.col_values(i)
```

循环行,得到索引的列表

```
for rownum in range(table.nrows):
print table.row_values(rownum)
#单元格
cell_A1 = table.cell(0,0).value
cell_C4 = table.cell(2,3).value
分别使用行列索引
cell_A1 = table.row(0)[0].value
cell_A2 = table.col(1)[0].value
#简单的写入
row = 0
col = 0
ctype = 1 # 类型 0 empty,1 string, 2 number, 3 date, 4 boolean, 5 error
value = 'lixiaoluo'
xf = 0 # 扩展的格式化 (默认是0)
table.put_cell(row, col, ctype, value, xf)
table.cell(0,0) # 文本:u'lixiaoluo'
table.cell(0,0).value # 'lixiaoluo'
```

#### xlwt

新建一个excel文件

```
import xlwt
file = xlwt.Workbook() #注意这里的Workbook首字母是大写，无语吧
```

新建一个sheet

```
table = file.add_sheet('sheet name')
```

写入数据table.write(行,列,value)

```
table.write(0,0,'test')
```

如果对一个单元格重复操作，会引发

```
returns error:
 Exception: Attempt to overwrite cell:
 sheetname=u'sheet 1' rowx=0 colx=0
```
 
所以需要在打开时加cell_overwrite_ok=True解决

```
table = file.add_sheet('sheet name',cell_overwrite_ok=True)
```

保存文件

```
file.save('demo.xls')
```

另外，使用style

```
style = xlwt.XFStyle() #初始化样式

font = xlwt.Font() #为样式创建字体

font.name = 'Times New Roman'

font.bold = True

style.font = font #为样式设置字体

table.write(0, 0, 'some bold Times text', style) # 使用样式
```
 

### Linux下保存用中文名保存正常,windos下坑爹乱码问题

```
#coding:utf8

file.save('世界你好')#linux正常,win乱码

file.save(u'世界你好'.encode('GBK'))# win正常
```
