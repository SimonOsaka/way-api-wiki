## 在线做图

简单
[fotor懒设计](https://www.fotor.com.cn/)

## 在线PhotoShop

[photopea](https://www.photopea.com)

## App生成icon\splash

QiAppIconGenerator，集成icon\splash的ios和android图片生成工具。
[QiAppIconGenerator](https://github.com/QiShare/QiAppIconGenerator)

#### uni-app增加android启动图需要的分辨率配置

```xml
<dict>
  <key>purpose</key>
  <string>hdpi Portrait</string>
  <key>name</key>
  <string>LaunchImage-Android-480x762</string>
  <key>size</key>
  <string>{480,762}</string>
</dict>
<dict>
  <key>purpose</key>
  <string>xhdpi Portrait</string>
  <key>name</key>
  <string>LaunchImage-Android-720x1242</string>
  <key>size</key>
  <string>{720,1242}</string>
</dict>
<dict>
  <key>purpose</key>
  <string>xxhdpi Portrait</string>
  <key>name</key>
  <string>LaunchImage-Android-1080x1882</string>
  <key>size</key>
  <string>{1080,1882}</string>
</dict>
```