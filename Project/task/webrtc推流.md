目标：**将本地采集的视频数据，通过WebRTC协议实时传输到服务器或对等端，实现低延迟的直播与通信**

任务清单：

1. vulkan 渲染帧数据（`vk::Image` -> `AVFrame`）
2. ffmpeg 编码帧数据 （ `AVFrame` ->`AVPacket`）
3. 建立`rtc::PeerConnection`，发送编码后的数据



