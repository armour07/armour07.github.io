---
title: Python 使用xlrd模块解析xlsx
date: 2025-03-08 02:31:30
tags:
- Python
categories:
- Python
- xlrd
---
####一， python安装xlrd模块
#####1, cmd下安装：
```
pip3 install xlrd
```
####二， 使用模块
#####1, 导入模块
```
  import xlrd
```
#####2, 打开文件
```
data = xlrd.open_workbook(path)
```
#####3, 获取工作表
```
sheet = data.sheets()[index]              #根据索引获取表
sheet = data.sheet_by_index(sheet_index)  #根据索引获取表
sheet = data.sheet_by_name(sheet_name)    #根据名字获取表
sheet_names = data.sheet_names()          #获取所有表名
```
#####4,数据类型
```
0: empty
1: string(text)
2: number
3: date
4: boolean
5: error
6: blank
```
#####5, 行操作
```
row_length = data.nrows     #获取行长度
data.row_len(rowx)             #获取行长度
data.row(rowx)               #返回该行的对象数据列表
data.row_slice(rowx)         #返回该行的对象数据列表
data.row_types(rowx, start_colx=0, end_colx=None)   #返回该行的类型数据列表
data.row_values(rowx, start_colx=0, end_colx=None)  #返回该行的值数据列表
```
#####6, 列操作
```
col_length = data.ncols         #获取列长度
data.col_len(colx)              #获取列长度
data.col(colx)                  #返回该列的对象数据列表
data.col_slice(colx)            #返回该列的对象数据列表
data.col_types(colx, start_rowx=0, end_rowx=None)   #返回该列的类型数据列表
data.col_values(colx, start_rowx=0, end_rowx=None)  #返回该列的值数据列表
```
#####7， 单元格
```
data.cell(rowx, colx)       #返回该单元格 数据类型:数据
data.cell(rowx,colx).value  #返回该单元格 数据
data.cell(rowx,colx).ctype  #返回该单元格 数据类型

data.cell_value(rowx,colx)  #返回该单元格 数据
data.cell_type(rowx,colx)   #返回该单元格 数据类型
```

xlrd 1.2.0以上版本不支持xlsx文件，有点无语，会出现报错：XLRDError: Excel xlsx file; not supported
解决一：
卸载xlrd：pip uninstall xlrd
更新pip：pip install --upgrade pip
安装1.2.0版本xlrd（注意两个==）：pip install xlrd==1.2.0
解决二：
推荐使用 openpyx

![](test.png)