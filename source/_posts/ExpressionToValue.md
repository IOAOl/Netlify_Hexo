---
uuid: 1ecf66ef-1948-f23e-f81a-e307c4a70c1a
title: ExpressionToValue
date: 2020-05-17 23:46:39
categories:
- 程序
tags:
- WPF
- 表达式求值
toc: true
---

<meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests">

# 表达式求值  wpf                                       

主要思想：表达式递归切割求值。

主要分成三个类。Expression，item和factor。



![1589730555323.png](https://i.loli.net/2020/05/18/D36Pi85OzXsHYqI.png)

# Expression（+/-）

由divStringExp类的divexp（）方法分割字符串

将表达式中的加法和减法消除。

先用栈找出最外层的括号：

![1589730585178.png](https://i.loli.net/2020/05/18/9yvzgBIKtb4h1Fr.png)

然后往括号两端扩展

![1589730598531.png](https://i.loli.net/2020/05/18/wQOiU6col4kmjx2.png)

切割加号得到两个items



# Item

把字符串分成被除数和除数

分两种情况：有括号和无括号

## 有括号的情况：

同样是用栈判断最外层的括号

![1589730611640.png](https://i.loli.net/2020/05/18/r9RHuIQCJpPechX.png)

把括号内的乘号和除号省略，分割外面的乘号和除号

并把除数之间和被除数之间相连（或者用两个数组将分子和分母保存起来）

![1589730620914.png](https://i.loli.net/2020/05/18/MzuFHbxXe1aEsKC.png)

## 没括号的情况：

遇到除号将后面的除数加到除数字符串   

 

 

# Factor

拆开最外层括号外的乘号

剩下的元素分成5种情况

### 函数

#### 一元函数

递归求括号内的值，再代入函数

例如sin(exp)

#### 多元函数（目前只有log（a,b）函数）

例如log(a,log(b,c))，将括号内提出来“a,log(b,c)”

将没在括号内的，分割

分成“a“和"log(b,c)"再分别递归求值。

### 次方

求最外层括号外的次方

最外层是括号：

拆括号后递归求值

**注意：次方内可能也有多个表达式**

例如:2^sin(a)

### 参数

找coefficient类将值返回

### 数字

用table.Compute（）方法转化

 

# 参数（Coefficient）类

正则表达式找变量并忽略掉所有的函数名，并初始赋值1



#  tool类

装有静态的函数名对象，方便外界访问



# 界面设计

Stackpanel中嵌套三个Grid布局

![1589730840570.png](https://i.loli.net/2020/05/18/wpmizh45WgBr3GL.png)

## Grid布局1

计算结果用label显示

## Grid布局2

四个listbox分别显示变量名，范围，滑块，变量值

## Grid布局3

输入栏和确定按钮

 

# 事件

##### 确定按钮click事件

初始化动态添加元素到四个listbox

##### 变量范围keydown事件

判断输入是否是数字

##### 四个listbox滑动条同步事件

```c#
VisualTreeHelper.GetChild(VisualTreeHelper.GetChild(this.listBox4, 0), 0) as ScrollViewer;//找到scrollviewer对象
		sv4.ScrollChanged += newScrollChangedEventHandler(listBox4_ScrollChanged);
//添加事件
        private void listBox4_ScrollChanged(object sender, ScrollChangedEventArgs e)
        {
        if (sv4 != null)
        {
            sv3.ScrollToVerticalOffset(sv4.VerticalOffset);
            sv2.ScrollToVerticalOffset(sv4.VerticalOffset);
            sv1.ScrollToVerticalOffset(sv4.VerticalOffset);
        }
    }
```
##### 双击变量名产生滑块动画事件

System.Windows.Threading.DispatcherTimer类用于动画制作

该类用Interval方法设置事件的发生间隔

tmr.Interval = TimeSpan.FromSeconds(0.01);

给他的tick事件添加委托threads方法

start方法启用

判断条件满足后停止

end方法终止

```c#
private void animation(object sender, RoutedEventArgs e)
        {
            Label label = sender as Label;
            int count = 0;
            foreach(Label l1 in listBox1.Items)
            {
                count++;
                if (l1 == label)
                    break;
            }
            ProcessToanimation = 0;
            CountToAnimation = count;
            System.Windows.Threading.DispatcherTimer tmr = new System.Windows.Threading.DispatcherTimer();
            tmr.Interval = TimeSpan.FromSeconds(0.01);
            tmr.Tick += new EventHandler(threads);
            tmr.Start();
        }
private void threads(object c,EventArgs e)
    {
        try
        {
            int count = CountToAnimation;
            slidersList[count-1].Value = ProcessToanimation;
            double td = (slidersList[count - 1].Value - 5) * Convert.ToDouble(rangesList[count - 1].Text);
            td = td / 5;
            valuesList[count - 1].Content = td;
            variableCollection.vlist[count - 1].value = td;
            exp.setVariables(variableCollection);
            result.Content = exp.getValue();
            ProcessToanimation += 0.025;
            if (ProcessToanimation >= 10)
            {
                slidersList[count-1].Value = 6;
                double ts = (slidersList[count - 1].Value - 5) * Convert.ToDouble(rangesList[count - 1].Text)/5;
                valuesList[count - 1].Content = ts;
                variableCollection.vlist[count - 1].value = ts;
                exp.setVariables(variableCollection);
                result.Content = exp.getValue();
                (c as System.Windows.Threading.DispatcherTimer).Stop();
            }
        }
        catch (Exception e1)
        {

        }
    }
```

