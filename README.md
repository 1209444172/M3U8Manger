## M3U8Manger (M3U8管理器)

- M3U8格式视频下载
- 获取M3U8格式信息
- 中途停止任务
- 获取下载速度
- 支持多任务下载

适用性：适用以下格式的m3u8格式（ts切片的名字就是当前时间eg:1500480000074.ts），其他格式的需要自己修改源码中的解析规则。
```
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:10
#EXTINF:9.960,
1500480000074.ts
#EXTINF:10.190,
1500480009999.ts
#EXTINF:9.960,
1500480020246.ts
#EXTINF:9.960,
1500480030189.ts
#EXTINF:10.030,
...
```


### 导入：

```java
compile 'com.jwkj:M3U8Manger:v2.0.6'
```

### 获取M3U8信息：

```java
   M3U8InfoManger.getInstance().getM3U8Info(url, new OnM3U8InfoListener() {
            @Override
            public void onSuccess(M3U8 m3U8) {
                ELog.e("获取成功了" + m3U8);
            }

            @Override
            public void onStart() {
                ELog.e("开始获取信息");
            }

            @Override
            public void onError(Throwable errorMsg) {
                ELog.e("出错了" + errorMsg);
            }
        });
```


### 下载M3U8格式的视频文件：

```java
 M3U8DownloadTask task1 = new M3U8DownloadTask("1001");

    public void onDownload(View view) {
        task1.download(url, new OnDownloadListener() {
            @Override
            public void onDownloading(final long itemFileSize, final int totalTs, final int curTs) {
                ELog.e(task1.getTaskId() + "下载中.....itemFileSize=" + itemFileSize + "\ttotalTs=" + totalTs + "\tcurTs=" + curTs);
            }

            /**
             * 下载成功
             */
            @Override
            public void onSuccess() {
                ELog.e(task1.getTaskId() + "下载完成了");
            }

            /**
             * 当前的进度回调
             *
             * @param curLenght
             */
            @Override
            public void onProgress(final long curLenght) {
                if (curLenght - lastLength > 0) {
                    final String speed = NetSpeedUtils.getInstance().displayFileSize(curLenght - lastLength) + "/s";
                    ELog.e(task1.getTaskId() + "speed = " + speed);
                    runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            ELog.e("更新了");
                            tvSpeed1.setText(speed);
                            ELog.e(tvSpeed1.getText().toString());
                        }
                    });
                    lastLength = curLenght;

                }
            }

            @Override
            public void onStart() {
                ELog.e(task1.getTaskId() + "开始下载了");
            }

            @Override
            public void onError(Throwable errorMsg) {
                ELog.e(task1.getTaskId() + "出错了" + errorMsg);
            }
        });
    }

```

### 停止任务：

```java
      task1.stop();
```

## 版本记录

### v2.x

v2.0.8([2017.08.14]())
- 【修复】设置文件保存路径时，会多出文件夹

v2.0.7([2017.08.14]())
- 【修复】设置文件保存路径方法被隐藏

v2.0.6([2017.08.14]())
- 【新增】断点续传
- 【新增】支持多任务
- 【优化】下载速度不准确问题

### V1.x

v1.0.8([2017.08.08]())
- 【新增】onLoadFileSizeForItem方法（获取单个ts文件的平均大小）
    - 可根据这个平均值x总的切片个数（total）=总文件的大小

v1.0.7([2017.07.31]())
- 【修复】获取M3U8信息不走onStart的bug

v1.0.6
- 【修复】指定下载的区间时间不准问题

v1.0.5
- 【修复】下载成功走的是onStart
- 【新增】指定下载的区间

v1.0.4
- 【新增】获取M3U8基本信息方法
- 【新增】停止任务方法

v1.0.3
- 【修复】url失效时抛出异常
- 【新增】M3U8开始、结束时间
