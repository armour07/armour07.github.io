---
title: Python 使用 xlrd 模块解析 Excel
tags:
  - Python
  - xlrd
  - Excel
categories:
  - Python
  - module
abbrlink: 8489
date: 2018-08-13 15:29:00
---

## 简介
xlrd是Python的一个第三方模块，用于读取Excel文件。它支持xls格式，但自1.2.0版本起不再支持xlsx格式。对于xlsx文件，建议使用openpyxl模块。

## 安装
### 1. 使用pip安装
```bash
pip install xlrd
```

## 基础用法
### 1. 导入模块
```python
import xlrd
```

### 2. 打开文件
```python
data = xlrd.open_workbook("example.xls")  # 打开Excel文件
```

### 3. 获取工作表
```python
sheet = data.sheet_by_index(0)  # 根据索引获取工作表
sheet = data.sheet_by_name("Sheet1")  # 根据名称获取工作表
sheet_names = data.sheet_names()  # 获取所有工作表名称
```

### 4. 数据类型
xlrd支持以下数据类型：
```text
0: empty
1: string(text)
2: number
3: date
4: boolean
5: error
6: blank
```

### 5. 行操作
```python
row_length = sheet.nrows  # 获取行数
row_values = sheet.row_values(0)  # 获取第一行的值
row_types = sheet.row_types(0)  # 获取第一行的数据类型
```

### 6. 列操作
```python
col_length = sheet.ncols  # 获取列数
col_values = sheet.col_values(0)  # 获取第一列的值
col_types = sheet.col_types(0)  # 获取第一列的数据类型
```

### 7. 单元格操作
```python
cell_value = sheet.cell_value(0, 0)  # 获取第一行第一列的值
cell_type = sheet.cell_type(0, 0)  # 获取第一行第一列的数据类型
```

## 高级用法
### 1. 处理日期
```python
date_value = xlrd.xldate_as_datetime(sheet.cell_value(0, 0), data.datemode)
```

### 2. 处理大型文件
```python
data = xlrd.open_workbook("large_file.xls", on_demand=True)
```

## 常见问题
### 1. xlsx文件不支持
自xlrd 1.2.0版本起，不再支持xlsx文件。建议使用openpyxl模块处理xlsx文件。

### 2. 文件无法打开
确保文件路径正确且文件未被其他程序占用。

## 模块对比
| 功能          | xlrd         | openpyxl     |
| ------------- | ------------ | ------------ |
| 支持xls文件   | 是           | 是           |
| 支持xlsx文件  | 否           | 是           |
| 读取性能      | 快           | 中等         |
| 写入功能      | 不支持       | 支持         |
| 处理大型文件  | 有限支持     | 支持         |

## 完整代码示例
```python
import xlrd

# 打开工作簿
data = xlrd.open_workbook("example.xls")

# 获取工作表
sheet = data.sheet_by_index(0)

# 读取数据
for row in range(sheet.nrows):
    row_values = sheet.row_values(row)
    print(row_values)
```