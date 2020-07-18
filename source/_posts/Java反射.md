---
title: Java反射
date: 2020-04-22 20:36:37
tags: 学习笔记·Java
---
## 写在前面  
本文摘记自慕课网付费教程《剑指Java面试-Offer直通车》6-4  
<!-- more -->
1. 谈谈反射  
Java反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意方法和属性；这种动态获取信息以及动态调用对像方法的功能称为Java语言的反射机制。  

2. 对象  
`Method`对象用来装类中的方法，`Field`用来装类中的属性。

3. 常用的Java反射函数  
    - `GetDeclaredMethod()`方法：  
    可以获得所有访问类型的方法，但是不能获得继承的和实现接口的方法。若获取的方法为私有方法，则需要将返回的对象调用`setAccessible()`方法，将`true`作为参数传入才不会报错。形如：`getHello.setAccessible(true)`  
    - `getMethod()`方法：  
    只能调用public方法，但是可以调用对象的继承和实现的接口的方法。若获取的方法为私有方法，则需要将返回的对象调用setAccessible()方法，将true作为参数传入才不会报错。形如：getHello.setAccessible(true)    
    - `invoke(Object,Object)`方法：  
    通过让`Method`对象调用`invoke()`方法来传参调用方法，其中`invoke()`方法的第一个参数为含有此方法的对象的实例化，第二个参数为方法要传入的参数。  
    