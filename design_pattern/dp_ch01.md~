title: 设计模式（01）-单例模式（Singleton）
date: 2014-11-18 18:34:26
categories:
- 设计模式
tags:
---
##要求
  必须单例
  推迟创建
  及时销毁

##方法一
```
#include<iostream>

class Singleton{
    public:
        static Singleton* Instance(){
            if( instance_ == 0 ){
                instance_ = new Singleton();
            }
            return instance_;
        }
    private:
        static Singleton *instance_;
        Singleton(){}
        Singleton(const Singleton&);
        Singleton& operator==(const Singleton&);
        ~Singleton();
};
```
缺点：满足`必须单例`和`推迟创建`原则，但没有满足及时销毁原则。

##方法二
```
#include<iostream>

class Singleton{
    public:
        static Singleton* Instance(){
            if( instance_ == 0 ){
                instance_ = new Singleton();
            }
            return instance_;
        }
    private:
        class Gabor{
        public:
            ~Gabor(){ 
                if(instance_){
                    delete instance_;
                }
            }
        };
        static Singleton *instance_;
        static Gabor gabor_;
        Singleton(){}
        Singleton(const Singleton&);
        Singleton& operator==(const Singleton&);

};
 Singleton* Singleton::instance_ = 0;
```
缺点：使用一个垃圾回收类来实现的，实现了`必须单例`和`延迟创建`原则。关于两个static变量哪个先析构，标准未定义。
##方法三
```
#include<iostream>

class Singleton{
    public:
        static Singleton* Instance(){
            static Singleton singleton;
            return &singleton;
        }
    private:
        Singleton(){}
        Singleton(const Singleton&);
        Singleton& operator==(const Singleton&);
};
```
缺点：满足`必须单例`和`延迟创建`，但未满足`及时释放`原则，同时线程不安全。
