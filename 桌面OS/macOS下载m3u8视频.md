## 下载m3u8视频

1. 执行`brew install ffmpeg`安装。

2. 安装完成后，执行`ffmpeg -i http://xxx.xxx.xxx/vvv.m3u8 -c copy shipin.mp4`

   * m3u8视频链接，通过浏览器抓取获得

   * 将`http://xxx.xxx.xxx/vvv.m3u8`改为要下载的链接
   * 将`shipin.mp4`改为要生成的文件名称＋后缀

3. 下载完成，视频在ffmpeg目录内，可以移动到其它任意处。

