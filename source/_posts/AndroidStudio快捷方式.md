---
layout: android
title: Android Studio快捷方式
date: 2018-04-25 16:16:54
tags: 快捷方式
categories: 其他
---

自己整理了在使用AndroidStudio开发工具所使用到的一些快捷键，也出于在开发过程中节省时间提升效率。

**常用的快捷键如下:**

    用TAB代替ENTER，现有的方法或者变量将直接被替代，省去了很多麻烦。
    
    Ctrl+Alt+L：格式化代码对齐
    Ctrl+Alt+O：清楚无效包引用
<!-- more -->
    Ctrl+Alt+Space(空格)：自动补全代码，类名和接口名提示
    Ctrl+空格：代码感应提示
    Alt+Enter：感应代码提示 例如方法转型
    Ctrl+P：提示方法的参数信息
    Ctrl+Q：光标选中方法显示该方法的详细参数信息
    Alt+回车：导入包，自动修正
    ctrl + shift + enter：快速补全完成（方法体大括号的添加，行尾分号的添加，自动格式化改行操作等）
    
    Shift+Shift：查找任意东西
    Ctrl+N：查找类
    Ctrl+Shift+N：查找文件
    Ctrl+Shift+Alt+N:查找类中的方法和变量
    Ctrl＋E：可以显示最近打开的文件列表
    Ctrl+Shift+E：可以显示最近修改的文件列表
    Ctrl+F：当前类中查找文本
    Ctrl+Shift+F：全局查找文本
    Ctrl+R：当前类中替换文本
    Ctrl+Shift+R：全局替换文本
    Alt+F1：查找代码所在的位置
    Ctrl+F12：可以显示当前文件的结构
    Ctrl+H：显示类结构图
    
    Ctrl+X:删除行
    Ctrl+D:复制行
    Shift+Alt+向上箭头：代码往上移
    Shift+Alt+向下箭头：代码往下移
    Ctrl＋Shift＋Backspace：可以跳转到上次编辑的地方
    Ctrl+Alt+Left/Right：返回至上次浏览的位置
    Alt+Up/Down：在方法间快速移动定位
    
    Ctrl+O/i：调出重写方法  overide / implement
    Alt+Insert：快速生成构造方法或是GET,SET方法
    Shift+F6：类重命名
    
    F2或Shift+F2：快速定位高亮错误或警告
    Ctrl + 加号或减号：收起或展示一个代码块
    Ctrl+Alt+T：可以把代码包在一块内，例如try/catch
    Ctrl+/：注释代码 格式：//代码块
    Ctrl+Shift+/：注释多行代码 格式:/*代码块*/
    ctrl + shift + v ：展示最近几次的复制内容
    Ctrl+Shift+F8：查看所有打断点的地方
    
    Ctrl+ALt+V：可以引入变量。例如把括号内的sql赋成一个变量
    
    ctrl+q：打开帮助文档
    
    Ctrl+F7：可以查询当前元素在当前文件中的引用，然后按F3可以选择
    
    Ctrl+[或]：可以调到大括号的开头结尾
    
    Ctrl+k：提交svn
    
    alt + shift + 鼠标点击：可添加一个编辑光标，最后如果不需要那么就esc即可关闭多余光标.
      
    Alt+Shift+K ：注释类说明<这里需自己设置：Settings-->点击Keymap-->在右边右上角搜索Other(fix doc comment)-->看到Fix doc comment 鼠标右键选择第一个，在输入框First Stroke输入ALT+SHIFT+K
    
    创建类注释模板:setting-->Editor-->File and Code Templates -->Includes-->File Header
    
    
    
AndroidStudio Debug模式快捷键:
    
    alt+f8：debug时选中查看值
    
    f8：跳到下一步
    
    f9：跳到下一个断点
    
    alt+f9：跳到光标处
    
    f7：进入到代码
    
    alt+shift+f7：这个是强制进入代码
    
    shift+f8：退出代码，返回上一层
    
    command+f2：停止运行
    
清理无用资源：

    项目-》右键 -》选中Analyze-》Run Inspection by Name -》输入 Unused Resuroces
    以及Refactor->Remove Unused Resources
    快速删除无用的import包
    code->Optimize Imports...