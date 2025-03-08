---
title: Python 使用 openpyxl 模块解析 Excel
date: 2020-12-09 16:36:00
tags:
- Python
- openpyxl
- Excel
categories:
- Python
- module
---

## 简介
openpyxl是Python的一个第三方模块，它可以用来读取和修改Excel文件。

## 安装
安装openpyxl模块可以使用pip命令：
```bash
pip install openpyxl
```

## 基础用法
### 打开表格
```python
import openpyxl as xl
wb = xl.load_workbook("example.xlsx")  # 打开表格
```
注意：打开时要确保文件是关闭状态。

### 读取sheet
```python
sheet = wb["Sheet1"]  # 通过名称获取sheet
sheet = wb.active     # 获取当前活动的工作表
```
`get_sheet_by_name`方法已弃用，建议使用`wb[sheet_name]`。

### 创建sheet
```python
wb.create_sheet("Sheet2", 2)  # 在索引2的位置创建Sheet2
```

### 读取表格
```python
value = sheet.cell(row=1, column=2).value  # 读取第1行第2列的值
```

### 写入表格
```python
sheet.cell(row=1, column=2, value="测试")  # 写入值到第1行第2列
```

### 保存文件
```python
wb.save("example_modified.xlsx")  # 保存文件
wb.close()  # 关闭工作簿
```

## 高级用法
### 处理大型Excel文件
```python
wb = xl.load_workbook("large_file.xlsx", read_only=True)
```

### 使用样式和格式
```python
from openpyxl.styles import Font, Alignment
font = Font(bold=True)
alignment = Alignment(horizontal="center")
sheet.cell(row=1, column=1).font = font
sheet.cell(row=1, column=1).alignment = alignment
```

### 处理不同Excel版本
```python
wb = xl.load_workbook("old_file.xls", keep_vba=True)
```

### 处理错误
```python
try:
    wb = xl.load_workbook("invalid_file.xlsx")
except Exception as e:
    print(f"Error: {e}")
```

## 完整代码示例
```python
import openpyxl as xl

# 打开工作簿
wb = xl.load_workbook("example.xlsx")

# 创建新sheet
wb.create_sheet("Sheet2", 2)

# 获取sheet
sheet = wb["Sheet1"]

# 读取和修改单元格
old_value = sheet.cell(row=1, column=2).value
new_value = "测试b"
sheet.cell(row=1, column=2, value=new_value)

# 打印结果
print(f"{old_value} --> {new_value}")

# 保存并关闭工作簿
wb.save("example_modified.xlsx")
wb.close()
```

## 常见问题
### 1. 文件无法打开
确保文件路径正确且文件未被其他程序占用。

### 2. 写入数据无效
确保在写入后调用`wb.save()`保存更改。

### 3. 读取大型文件慢
使用`read_only=True`模式加载文件。