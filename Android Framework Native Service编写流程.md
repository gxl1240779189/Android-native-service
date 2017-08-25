---
title: Android Framework Native Service编写流程
tags: Native Service,FrameWork
---

### 1.1 前言
距离上一次认真写博客，差不多过了快一年了，这段期间，我从学生变为职场员工，从Android工程师变为现在研究Android Framework，但是幸好还在学习Android的路上，不忘初心，方成大牛。下面就来分享一下Android Framework的学习心得。
### 2.1 Native Service编写流程介绍
Native Service是指运行在Framework的系统服务，是Application运行的基石，例如常用的ActivityManagerService、WindowManagerService、PackageManagerService等，本文的目的就是编写一个类似于上述的Service提供给别的进程使用。
大致的步骤主要分为

 1. 编写Ixxx.h Ixxx.cpp 主要编写业务层的接口、Bpxxx客户端、Bnxxx服务端。
 2. 编写xxxService.h xxxService.cpp 将上述Bnxxx服务端集成到Service里面。
 3. 编写main_xxxService.cpp用来启动步骤2的xxxService。
 4. 编写一个TestClient，用来测试xxxService是否正常工作。
 5. 编写xxx.mk，将main_xxxService和TestClient变为可执行文件。
 6. 先运行./main_xxxService可执行文件，在运行./TestClient可执行文件。
#### 2.1.1 编写业务层 Ixxx.h

``` stylus
class IAddManager : public IInterface

{

public:

    DECLARE_META_INTERFACE(AddManager)

    virtual int test()=0;

};
```
上面业务接口只有一个test()纯虚函数。

#### 2.1.2 编写Bnxxx服务端

``` stylus
class BnAddManager : public BnInterface<IAddManager>

{

public:

    enum {

        TEST= IBinder::FIRST_CALL_TRANSACTION + 0,

    };

    virtual status_t onTransact(

            uint32_t code,

            const Parcel& data,

            Parcel* reply,

            uint32_t flags = 0);

    

     virtual int test();

};
```

上述类的定义遵照一点的模板，必定是Class Bnxxx:public BnInterface<Ixxx>。
BnInterface是一个模板类，该模板类主要是通信层的功能，继承该类，Bnxxx就获得了跨进程的通信的能力，底层实现为Binder机制。