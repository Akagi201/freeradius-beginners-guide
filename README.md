# RTSSBook

## Outline

* [ch01 总览](ch01-overview/README.md)
* [ch02 工具](ch02-tools/README.md)
  * [FFmpeg](ch02-tools/02_01_ffmpeg.md)
  * [Wireshark](ch02-tools/02_02_wireshark.md)
  * [OBS](ch02-tools/02_03_obs.md)
  * [Nginx](ch02-tools/02_04_nginx.md)
  * [SRS](ch02-tools/02_05_srs.md)
* [ch03 文件封装格式](ch03-file-format/README.md)
* [ch04 传统流媒体协议](ch04-traditional-streaming-protocols/README.md)
  * [RTP](ch04-traditional-streaming-protocols/04_01_rtp.md)
  * [RTSP](ch04-traditional-streaming-protocols/04_02_rtsp.md)
  * [RTCP](ch04-traditional-streaming-protocols/04_03_rtcp.md)
* [ch05 流行流媒体协议](ch05-popular-streaming-protocols/README.md)
  * [RTMP](ch05-popular-streaming-protocols/05_01_rtmp.md)
  * [HLS](ch05-popular-streaming-protocols/05_02_hls.md)
  * [HTTP-FLV](ch05-popular-streaming-protocols/05_03_http_flv.md)
* [ch06 前沿流媒体协议](ch06-modern-streaming-protocols/README.md)
  * [RTMFP](ch06-modern-streaming-protocols/06_01_rtmfp.md)
  * [WebRTC](ch06-modern-streaming-protocols/06_02_webrtc.md)
  * [Dash](ch06-modern-streaming-protocols/06_03_dash.md)
* [ch07 播放端](ch07-player/README.md)
  * [web player](ch07-player/07_01_web_player.md)
  * [Android](ch07-player/07_02_android.md)
  * [iOS](ch07-player/07_03_ios.md)
  * [VLC](ch07-player/07_03_vlc.md)
  * [Flash](ch07-player/07_03_flash.md)
* [ch08 推流端](ch08-publish/README.md)
  * [FFmpeg](ch08-publish/08_01_ffmepg.md)
  * [OBS](ch08-publish/08_02_obs.md)
  * [Flash](ch08-publish/08_03_flash.md)
  * [WebRTC](ch08-publish/08_04_webrtc.md)
  * [Android](ch08-publish/08_05_android.md)
  * [iOS](ch08-publish/08_06_ios.md)
* [ch09 服务端](ch09-server/README.md)
  * [SRS](ch09-server/09_01_srs.md)
  * [nginx-rtmp](ch09-server/09_02_nginx_rtmp.md)

## 排版规范
* [中文文案排版指北](https://github.com/sparanoid/chinese-copywriting-guidelines)
* 有一点特别的地方是, 我本人的全用英文半角标点符号, 所以, 希望大家也用 "半角标点+空格" 的方式.

## Git 协作方式
* [CONTRIBUTING](CONTRIBUTING.md)

## Gitbook 指南
* 安装命令行工具: `npm install -g gitbook-cli` 建议使用最新 3.0 以上版本.
* build 前先安装 `book.json` 中配置的插件, `gitbook install`.
* 本地编译: `gitbook build` 会将 build 后网页文件放到 `_book` 目录.
* 本地查看: `gitbook serve` 默认会进行 build, 然后 serve 一个网页提供给本地浏览器访问.

## LICENSE
* [LICENSE](LICENSE.md)
