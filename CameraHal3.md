# CameraHal3

## CameraHal3基本框架

![image-20230819134904081](/Users/chentao/Library/Application Support/typora-user-images/image-20230819134904081.png)

- Camera hal3对上，主要实现 Framework 一整套 API 接口，响应其 控制命令，返回数据与控制参数结果。 对下， 主要是通 V4l2 框架实现与 kernel 的交互。3a 控制则是通 control loop 接口与 camera_engine_isp 交互。 另外，其中一些组件或功能的实现也会调用到其他一些第三方 库， 如 cameraBuffer 相关，会调用到 Galloc 相关库， jpeg 编码则会调用到 Hwjpeg 相关库

## CameraHal3基本组件

![image-20230819135303545](/Users/chentao/Library/Application Support/typora-user-images/image-20230819135303545.png)

Camera hal3 中的模块主要包括 AAL 与 PSL：

