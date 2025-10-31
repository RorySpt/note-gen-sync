目标：**将本地采集的视频数据，通过WebRTC协议实时传输到服务器或对等端，实现低延迟的直播与通信**

任务清单：

1. vulkan 渲染帧数据（`vk::Image` -> `AVFrame`）
2. ffmpeg 编码帧数据 （ `AVFrame` ->`AVPacket`）
3. 建立`rtc::PeerConnection`，发送编码后的数据

[设备和队列 :: Vulkan 文档项目 --- Devices and Queues :: Vulkan Documentation Project](https://docs.vulkan.org/spec/latest/chapters/devsandqueues.html)

[C/C++音视频高级开发 FFmpeg编程入门 - 知乎](https://zhuanlan.zhihu.com/p/447780181)

[FFmpeg 学习](https://github.com/0voice/ffmpeg_develop_doc)

本周：

1. 研究WebRTC推流，一种可能的方案是从Vulkan中抓取帧数据，使用ffmpeg 编码成视频帧，然后使用libdatachannel进行推送
2. 现在可以实现的是从ZetaEngine抓取一段时间的帧数据到本地，然后使用 ffmpeg 合成视频，然后利用libdatachannel的example中的工具采样成sample再推流到网页
3. 现在可以在ZetaEngine中使用ffmpeg库实时编码到AVPacket，AVPacket到sample还需要研究

