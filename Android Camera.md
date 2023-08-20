# Android Camera

## Camera系统基本硬件结构

### 核心组件

- 镜头Lens：用来聚光
- 感光器Sensor：用于光电转换，将像平面转换成图像信息
- 图像处理器：加工处理图像数据，比如降噪、去马赛克、颜色矫正

![图片](https://mmbiz.qpic.cn/mmbiz_png/Alm9kic5yxKxeRCrZAOuNSLWl3dC1PIvjpLicAgSsCqGk7DSVNiczDQibWMTN2icHlYnbXw86udO6PeBouicNia29Q85g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 名词解释

- 光圈：控制着瞬时进光量，两个主要影响：一是进光量，二是画面景深（景深越浅，背景越模糊；景深越深，背景越清晰）
- 快门：控制着曝光时间，两个主要作用：一是影响曝光，二是影响被摄物体形态
- IR cut filter：红外截止滤光片，它放在于Lens与Sensor之间
- 对焦马达：使像平面与感光器感光平面重合，形成清晰的成像效果
- 感光度ISO：对光线的敏感程度，两个主要影响：一是影响照片曝光，二是影响画质

### 相机的快门

- 相机的快门有两道闸门，分别是：前帘、后帘
- 在进行拍摄时，后帘从下往上打开，这个时候不会有光量，因为前帘还是闭合的。当后帘从下往上打开，然后前帘从上往下打开，画面从上往下进行曝光，这就是“咔”，等曝光结束后，后帘就从上往下闭合，后帘一闭合整个曝光就结束了，这就是“嚓”
- 相机之所以有两个快门帘，就是为了能从上往下曝光，也能从上往下结束曝光。如果相机只有一个前帘，那么是从上往下开始曝光，当曝光结束就要从下往上了，这就会导致上面的曝光比下面多

## RGB三原色原理

人眼对红、绿、蓝最为敏感，人的眼睛像一个三色接收器的体系，任何光都可以用红、绿、蓝这3种光按不同的比例混合而成，这就是三原色原理。

- 自然界的任何光色都可以由3种光色按不同的比例混合而成
- 三原色之间是相互独立的，任何一种光色都不能由其余的两种光色来组成
- 混合色的饱和度由3种光色的比例来决定。混合色的亮度为3种光色的亮度之和

### 色环

![img](https://pic1.zhimg.com/v2-f3b892e10e1dba35d547280965ab5bbc_r.jpg)

## 专有名词

- ISP（Image Signal Process，图像信号处理）
- Engine layer：引擎层
- Sensor：传感器

| 3A =>(AWB, AE, AF)                           | 表示AWB, AE, AF的简写   |
| -------------------------------------------- | ----------------------- |
| AE =>(Auto Exposure)                         | 自动曝光                |
| AF =>(Auto Focus)                            | 自动对焦                |
| AWB =>(Auto White Balance)                   | 自动白平衡              |
| FBC =>(Frame Buffer Compressed)              | 帧缓冲压缩              |
| FBC0 =>f(Rockchip yuv420sp fbc encoder)      | RK平台yuv420sp fbc 编码 |
| FBC2 =>(Rockchip yuv422sp fbc encoder)       | RK平台yuv422sp fbc 编码 |
| RkAiq =>(Rockchip Automatical Image Quality) | RK平台自动图像质量      |
| IQ =>(Image Quality)                         | 图像质量                |
| ISP =>(Image Signal Process)                 | 图像信号处理            |
| ISPP =>(Image Signal Post Process)           | 图像信号后处理          |
| Raw sensor                                   | 原始传感器              |
| CIS(cmos image sensor) Driver                | cmos图像传感器驱动      |
| CSI(cmos sensor interface)                   | cmos传感器接口          |
| Lens                                         | 镜头                    |
| VFS                                          | 虚拟文件系统            |
| BLC(BlackLevel Correction)                   | 黑电平校正              |
| LSC(Lens Shade Correction)                   | 镜头阴影校正            |
| BPC(Bad Point Correction)                    | 坏点校正                |

## Camera engine

Camera engine主要实现的是Raw sensor的3A控制

## Rkisp Driver

RKISP 驱动主要是依据 v4l2 / media framework 实现硬件的配置、中断处理、控制 buffer 轮转，以及控制 subdevice(如 mipi dphy 及 sensor)的上下电等功能

- v4l2(Video for Linux 2)：Linux内核中关于视频设备驱动的框架，现在的摄像头都适配这个主流框架

## ISP图像处理流程

![img](https://img-blog.csdnimg.cn/20191020182453549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RpbmcyODM1OTU4NjE=,size_16,color_FFFFFF,t_70)

### BLC(BlackLevel Correction)------黑电平校正

- 在调试相机镜头过程，把设备放入封闭的密封箱中，会发现镜头呈现黑色，但是黑色的程度不够黑，这是由于暗电流的影响， 传感器出来的实际原始数据并不是我们需要的黑平衡（ 数据不为0），所以需要找到一个矫正值，然后将所有的区域的像素减去此矫正值，如果图像呈现纯黑色，说明矫正成功

### LSC(Lens Shade Correction)------镜头阴影校正

- 由于相机在成像距离较远时，随着视场角慢慢增大，能够通过照相机镜头的斜光束将慢慢减少，从而使得获得的图像中间比较亮，边缘比较暗，这个现象就是光学系统中的渐晕
- 常用的镜头矫正的具体实现方法是，首先确定图像中间亮度比较均匀的区域，该区域的像素不需要做矫正；以这个区域为中心，计算出各点由于衰减带来的图像变暗的速度，这样就可以计算出相应R、G、B通道的补偿因子

### BPC(Bad Point Correction)------坏点校正

- 如果图像中存在坏点的话，那么坏点会随着颜色插补的过程往外扩散，直到影响整幅图像。因此必须在颜色插补(Demosaic)之前进行坏点的消除

### Demosaic------颜色插值

- 经过滤色板的作用之后，每个像素点只能感应到一种颜色，必须要找到一种方法来复原该像素点其它两个通道的信息，寻找该点另外两个通道的值的过程就是颜色插值的过程
- 由于图像是连续变化的，因此一个像素点的R、G、B的值应该是与周围的像素点相联系的，因此可以利用其周围像素点的值来获得该点其它两个通道的值
- 目前最常用的插补算法是利用该像素点周围像素的平均值来计算该点的插补值

### Bayer Denoise-----去除噪声

### AWB（Automatic White Balance）------自动白平衡

- 在不同光线下拍摄到的图像，会受到光源颜色的影响而发生变化
- 为了消除光源颜色对于图像传感器成像的影响，自动白平衡功能就是模拟了人类视觉系统的颜色恒常性特点来消除光源颜色对图像的影响的
- 白平衡顾名思义就是让白色在任何色温下camera都能把它还原成白，目的就是白色物体在任何色问下都是R=G=B呈现出白色

### CCM（Color Correction Matrix）------颜色校正

- 颜色校正主要为了校正在滤光板处各颜色块之间的颜色渗透带来的颜色误差，对颜色进行校正的过程，都会伴随有对颜色饱和度的调整

## V4l2摄像头驱动流程

### 打开设备

```c++
int fd = open("/dev/video0", O_RDWR);//读写打开
```

### 获取摄像头格式

```c
//获取摄像头支持格式 ioctl(文件描述符,命令，与命令对应的结构体)
struct v4l2_fmtdesc vfmt;
vfmt.type = V4L2_BUF_TYPE_VIDEO_CAPTURE;//摄像头采集

int i = 0;
while(1)         //使用while循环从0开始测试index索引值
{
    vfmt.index = i;
    i++;
    int ret = ioctl(fd,VIDIOC_ENUM_FMT, &vfmt);
    if (ret < 0)
    {
        perror("获取失败");
        break;
    }
    printf("index=%d\n",vfmt.index);
    printf("flags=%d\n",vfmt.flags);
    printf("discription=%s\n",vfmt.description);
    unsigned char *p = (unsigned char *)&vfmt.pixelformat;
    printf("pixelformat=%c%c%c%c\n",p[0],p[1],p[2],p[3]);
    printf("reserved=%d\n",vfmt.reserved[0]);
}
```

### 配置摄像头

```c
//获取摄像头支持格式 ioctl(文件描述符,命令，与命令对应的结构体)
struct v4l2_format vfmt;
vfmt.type = V4L2_BUF_TYPE_VIDEO_CAPTURE; //摄像头采集
vfmt.fmt.pix.width = 640; //设置摄像头采集参数，不可以任意设置
vfmt.fmt.pix.height = 480;
vfmt.fmt.pix.pixelformat = V4L2_PIX_FMT_YYUV; //设置视频采集格式 ，根据上一步测得，注意格式有yyuv和yuyv不要搞混

int ret = ioctl(fd,VIDIOC_S_FMT,&vfmt); //设置格式命令
if (ret < 0)
{
    perror("设置格式失败1");
}

memset(&vfmt,0,sizeof(vfmt));
vfmt.type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
ret = ioctl(fd,VIDIOC_G_FMT,&vfmt);
if (ret < 0)
{
    perror("设置格式失败2");
}

if(vfmt.fmt.pix.width == 640 && vfmt.fmt.pix.height == 480 && vfmt.fmt.pix.pixelformat == V4L2_PIX_FMT_YUYV)
{
    printf("设置成功！");
}else
{
    printf("设置失败3");
}
```

### 申请内核缓冲区队列

```c
//申请内核空间
struct v4l2_requestbuffers reqbuffer;
reqbuffer.type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
reqbuffer.count = 4; //申请4个缓冲区
reqbuffer.memory = V4L2_MEMORY_MMAP;  //映射方式

ret = ioctl(fd,VIDIOC_REQBUFS,&reqbuffer);
if (ret < 0)
{
    perror("申请空间失败");
}
```

### 内核缓冲区队列映射到用户空间

```c
//映射
unsigned char *mptr[4];//保存映射后用户空间的首地址
struct v4l2_buffer mapbuffer;
//初始化type和index
mapbuffer.type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
for(int i = 0; i <4;i++) {  //使用for对四个申请的空间进行轮询
    mapbuffer.index = i;
    ret = ioctl(fd,VIDIOC_QUERYBUF,&mapbuffer); //从内核空间中查询一个空间作映射
    if (ret < 0)
    {
        perror("查询内核空间失败");
    }
    //映射到用户空间
    mptr[i] = (unsigned char *)mmap(NULL,
                                    mapbuffer.length,
                                    PROT_READ|PROT_WRITE,
                                    MAP_SHARED,
                                    fd,
                                    mapbuffer.m.offset);
    //查询后通知内核已经放回
    ret = ioctl(fd,VIDIOC_QBUF,&mapbuffer); 
    if (ret < 0)
    {
        perror("放回失败");
    }
}
```

### 采集帧数据

1. VIDIOC_STREAMON（开始采集写数据到队列中）
2. VIDIOC_DQBUF（告诉内核我要某一个数据，内核不可以修改我正在采集的地方的数据）
3. VIDIOC_QBUF（告诉内核我已经使用完毕，内核可以写入了）
4. VIDIOC_STREAMOFF（停止采集-不在向队列中写数据）

```c
//开始采集
int type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
ret = ioctl(fd,VIDIOC_STREAMON,&type); 
if (ret < 0)
{
    perror("开启失败");
}
//从队列中提取一帧数据
struct v4l2_buffer readbuffer;
readbuffer.type = V4L2_BUF_TYPE_VIDEO_CAPTURE; //每个结构体都需要设置type为这个参赛要记住
ret = ioctl(fd,VIDIOC_DQBUF,&readbuffer); 
if (ret < 0)
{
    perror("读取数据失败");
}

FILE *file=fopen("my.jpg", "w+");  //打开一个文件
fwrite(mptr[readbuffer.index],readbuffer.length,1,file);//写入文件
fclose(file);    //写入完成，关闭文件

//通知内核使用完毕
ret = ioctl(fd, VIDIOC_QBUF, &readbuffer);
if(ret < 0)
{
		perror("放回队列失败");
}

//停止采集
ret = ioctl(fd,VIDIOC_STREAMOFF,&type);

//释放映射
for(int i=0; i<4; i)munmap(mptr[i], size[i]);
```



