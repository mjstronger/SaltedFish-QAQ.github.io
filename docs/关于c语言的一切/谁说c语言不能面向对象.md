# 谁说c语言不能面向对象

## 前言鸭

语言分为两种，听得懂的和听不懂的（不是XD。是面向对象和面向过程，而c语言则是经典的面向过程语言。但究其根本，面向过程也好，面向对象也罢都是一种思想而已。那么既然是思想其必然和所实现的工具没有关系，所以……我小咸鱼今天就是要用勺子吃面条！XD

## 问题描述

最近碰到了一个按键检测较多的项目，写完之后在前台处理模块出现了大量的，重复性代码。同样的或者略微差异的端口检测函数，同样的包含了滤波，上下边沿，长按，按下，弹起等检测项目。而大同小异的代码却有了一个致命的差异，不同的gpio端口号。于是出现了……一坨shit（fuck。而恰巧，本人领导有做过按键的一个面向对象的文件，我有刚好看了点oopc。于是，lets do it！

## 思想描述

所有的按键都是基于gpio检测而来的，而gpio所具备的东西也就那么几个：端口号，端口状态，端口检测函数，端口输出函数等不一而足。于是应当先声明一歌gpio对象，然后再声明一个按键对象，让其包含gpio对象。以此达到继承的目的，（不要问为什么没有其他特性，因为我菜。由此而实现一个代码，多个按键共同使用的目的。

## 代码展示

talk is cheap，show me the code。

先是相关的gpio函数指针声明，及gpio对象声明

```c
typedef uint8_t (*ExterPort_Check)(ExterPortInPut);//端口检测函数指针
typedef void (*ExterPort_OutPut)(GPIO_PinEnum, FunctionalState);//端口输出函数指针

typedef enum 
{
   lowlevel = 0,//端口低电平
   highlevel,
}ExterPortState;

typedef struct ExterPortObject
{
    ExterPortInPut exterPort;//端口号
    ExterPort_Check exterPort_Check;//端口检测函数
    ExterPort_OutPut exterPort_outPut;//端口输出函数
    ExterPortState state;//端口状态
}ExterPortObject;//对象名
```

然后是按键对象及其相关声明

```c
typedef enum 
{
   fallingedge,//下降沿
   risingedge,//上升沿
   longpress,//长按
   press,//按下
   nopress,//弹起
}KeyState;

typedef struct KeyObject
{
    ExterPortObject exterPortObject;//gpio对象
    KeyState state;//按键状态
    uint16_t pressTimer;//按键定时器
    uint16_t longPressDelay;//按键长延时
}KeyObject;
```

然后就是相关函数啦

```c
#define KEYLONGPRESSTIME        700//长按延时
#define SAMPLINGTIMES           20//滤波次数
#define MINQUALIFIEDTIMES       15//最小滤波合格次数

void ExterPortObject_Init(ExterPortObject* pobj, ExterPortInPut port);
void KeyObject_Init(KeyObject* pobj, ExterPortInPut port);

void ExterPortObject_Init(ExterPortObject* pobj, ExterPortInPut port)
{
    pobj->exterPort = port;//初始化gpio端口
    pobj->exterPort_Check = ExterPort_GetInPutLevel;//存入端口检测函数
    pobj->exterPort_outPut = ExterPort_SetFault;//存入端口输出函数，这两个函数由外部实现。
    pobj->state = highlevel;//初始化端口电平，本项目空闲状态为高电平
}

void KeyObject_Init(KeyObject* pobj, ExterPortInPut port)
{
    ExterPortObject_Init(&(pobj->exterPortObject), port);//初始化gpio对象
    pobj->state = nopress;//初始化按键状态
    pobj->pressTimer = 0;//初始化按键定时器
    pobj->longPressDelay = KEYLONGPRESSTIME;//初始化长按延时
}

static ExterPortState ExterPortObject_GetState(ExterPortObject* pobj);

//本函数使用的滤波功能有限，仅仅只能滤掉高频部分而低频抖动无能为力，尚待更改。如果没有更改，我菜，我懒
static ExterPortState ExterPortObject_GetState(ExterPortObject* pobj)
{
    ExterPortState state = highlevel;
    uint8_t times = 0;

    for (int i = 0; i < SAMPLINGTIMES; i++)//连续检测20次gpio电平
    {
        if (!pobj->exterPort_Check(pobj->exterPort))
        {
            times++;
        }
    }

    if (times >= MINQUALIFIEDTIMES)//当合格次数大于15次时认为检测成功
    {
        state = lowlevel;
    }
    else
    {
        state = highlevel;
    }
    
    return state;//返回检测电平
}

void KeyScanInput(KeyObject* pobj);

void KeyScanInput(KeyObject* pobj)
{
    ExterPortState currentPortLevel = highlevel;

    currentPortLevel = ExterPortObject_GetState(&(pobj->exterPortObject));//检测按键电平

    if (currentPortLevel != pobj->exterPortObject.state)//当当前检测电平不等于原电平时，开始进行上下边沿判断
    {
        if (pobj->exterPortObject.state == highlevel)//因本项目常态为高电平，所以此处为下降沿。即按键下降边沿
        {
            pobj->state = fallingedge;
        }
        else if (pobj->exterPortObject.state == lowlevel)
        {
            pobj->state = risingedge;
        }
        pobj->exterPortObject.state = currentPortLevel;
        return;
    }

    if (pobj->exterPortObject.state == lowlevel)//按下时进行按键延时即可
    {
        pobj->state = press;
        pobj->pressTimer++;
    }
    else if (pobj->exterPortObject.state == highlevel)
    {
        pobj->state = nopress;
        pobj->pressTimer = 0;
    }
    
    
    if (pobj->pressTimer >= pobj->longPressDelay)//当按键定时器值达到长按延时时，设置为长按状态
    {
        pobj->state = longpress;
        pobj->pressTimer = 0;
    }
    
}
```

如何使用

```c
//先声明
KeyObject gStopKeyCheck;
KeyObject gRun1KeyCheck;
KeyObject gRun2KeyCheck;
KeyObject gAlarmSignalCheck;
KeyObject gPauseSignalCheck;
KeyObject gPauseSignalGenerate;

//再初始化
KeyObject_Init(&gRun1KeyCheck, Port_X0);
KeyObject_Init(&gRun2KeyCheck, Port_X1);
KeyObject_Init(&gStopKeyCheck, Port_X2);
KeyObject_Init(&gAlarmSignalCheck, Port_X3);
KeyObject_Init(&gPauseSignalCheck, Port_X4);
KeyObject_Init(&gPauseSignalGenerate, (ExterPortInPut)EXTER_PORT_Y0);

//然后周期性的调用扫描函数即可
KeyScanInput(&gRun1KeyCheck);
KeyScanInput(&gRun2KeyCheck);
KeyScanInput(&gStopKeyCheck);
KeyScanInput(&gAlarmSignalCheck);
KeyScanInput(&gPauseSignalCheck);

//依据对应的按键状态进行功能实现
if (gStopKeyCheck.state == longpress)
{
    return Motor_ClearLapCount;
}

if (gStopKeyCheck.state == fallingedge)
{
    // printf("stop mode\n");
    return Motor_StopMode;//停机状态
}
if (gRun2KeyCheck.state == fallingedge)
{
    // printf("start mode 2\n");
    return Motor_StartMode2;//启动模式2
}
else if (gRun1KeyCheck.state == fallingedge)
{
    // printf("start mode 1\n");
    return Motor_StartMode1;//启动模式1
}
```

## 结语

本次主要探索可以使用某种方式达到面向对象的某些地步，但依然很有限。也只是站在巨人的肩膀上捡石头罢了，但私以为既然是一次值得记下来的事情。遂有此篇，好了说了这么多对象，希望大伙都有对象。