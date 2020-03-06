---
title: python数据可视化方式（持续更新）
date: 2020-03-03 10:43:06
tags: 
  - python
  - pandas

---

**目录 (Table of Contents)**

* [初始准备包引入](#初始准备)
* [解决图表显示方块丢失中文问题](#解决图表显示方块丢失中文问题)
* [比较地区与房屋单价金额数量的关系](#例1：（树状图、箱形图）比较地区与房屋单价金额数量的关系)
* [房屋面积的大小比例，面积与价格关系](#例2：（质量直方图、线性集中关系图、树状图）面积的大小比例，面积与价格关系)

# 初始准备
````python
    import pandas as pd
    import matplotlib.pyplot as plt
    import seaborn as sns
    import re
````

# 解决图表显示方块丢失中文问题
````python
    plt.rcParams['font.sans-serif'] = ['SimHei']  # 中文字体设置-黑体
    plt.rcParams['axes.unicode_minus'] = False  # 解决保存图像是负号'-'显示为方块的问题
    sns.set(font='SimHei')  # 解决Seaborn中文显示问题
````

# 例1：（树状图、箱形图）比较地区与房屋单价金额数量的关系

## 1.  数据结构

## 2.  关系展示
````python
    # 设置三个图表ax1,ax2,ax3的大小 : 3列1行 每个12×18大小
    f,[ax1,ax2,ax3] = plt.subplots(3,1,figsize=(12,18))
    
    # 1.数据准备
    df_house_mean = house_df.groupby('Region')['houseSinglePrice'].mean().sort_values(ascending=False).to_frame().reset_index()
    
    # 1.数据展示
    sns.barplot(x='Region', y='houseSinglePrice', palette='Blues_d', data=df_house_mean, ax=ax1)
    ax1.set_title('成都各地区没平米单价对比')
    ax1.set_xlabel('区域')
    ax1.set_ylabel('每平米单价')
    
    # 2.数据准备
    df_house_count = house_df.groupby('Region')['houseId'].count().sort_values(ascending=False).to_frame().reset_index()
    df_house_count.rename(columns = {'houseId':'Count'},inplace='True')
    
    # 2.数据展示
    sns.barplot(x='Region', y='Count', palette='Greens_d', data=df_house_count, ax=ax2)
    ax2.set_title('成都各区二手房数量对比')
    ax2.set_xlabel('区域')
    ax2.set_ylabel('数量')

    # 3.数据展示
    sns.boxplot(x='Region', y='houseTotalMoney', data=house_df, ax=ax3)
    ax3.set_title('成都各区二手房房屋总价')
    ax3.set_xlabel('区域')
    ax3.set_ylabel('房屋总价')
````
![例1](python数据可视化方式/例1.png)

# 例2：（质量直方图、线性集中关系图、树状图）面积的大小比例，面积与价格关系

## 1.  数据结构

## 2.  关系展示
````python
    # 设置两个图表ax1,ax2的大小 : 1列2行 每个16×6大小
    f, [ax1,ax2] = plt.subplots(1, 2, figsize=(16, 6))
    
    # 房屋面积
    sns.distplot(house_df['houseBuildingArea'], ax=ax1, color='r')
    sns.kdeplot(house_df['houseBuildingArea'], shade=True, ax=ax1)
    ax1.set_xlabel('面积')

    # 房屋面积和价格的关系
    sns.regplot(x='houseBuildingArea', y='houseTotalMoney', data=house_df, ax=ax2)
    ax2.set_xlabel('面积')
    ax2.set_ylabel('总价')
    
    # -------------------------------------------------------------------------------------------
    
    f, [ax1, ax2] = plt.subplots(1, 2, figsize=(16, 8))
    
    # 数据准备
    elevator_df =  house_df[house_df['houseElevator'] != '暂无数据']
    elevator_df['houseElevator'].value_counts()

    sns.countplot(x = 'houseElevator',data = elevator_df, ax=ax1)
    ax1.set_title('有无电梯数量对比', fontsize=15)
    ax1.set_xlabel('是否有电梯')
    ax1.set_ylabel('数量')

    sns.barplot(x='houseElevator', y='houseSinglePrice', data=elevator_df, ax=ax2)
    ax2.set_title('有无电梯房价对比')
    ax2.set_xlabel('是否有电梯')
    ax2.set_ylabel('单价')
````
![例2-1](python数据可视化方式/例2-1.png)
![例2-2](python数据可视化方式/例2-2.png)
