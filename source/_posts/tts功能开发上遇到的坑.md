---
title: tts功能开发上遇到的坑
date: 2019-08-12
categories: JavaScript
author: yangpei
tags:
  - JavaScript
comments: true
cover_picture: /images/banner.jpg
---

**tts功能开发上遇到的坑**

1. 使用request库请求音频，返回文件的文件无法试听——应该设置encoing:null，这样可以返回buffer

<!-- more -->

```JavaScript
 request(item.src, { encoding: null },function (error, response, body) {}
```

2. 文件type问题解决（后端音频返回的格式有很多，因此需要提前与后端确定，使用动态的方式添加文件名后缀）

```JavaScript
    let suffix = response.headers['content-type'];
    let suffixMap = {
                  'audio/mp3': 'mp3',
                  'audio/mp4': 'mp4',
                  'audio/wav': 'wav',
                  'audio/x-ms-wma': 'wma',
                  'audio/mpegurl': 'm3u',
                }
    let fileName = `${item.id}.${suffixMap[suffix]}`;
```

3. 下载的文件出现502请求

```JavaScript
502就是后台服务异常,是外部依赖的异常,直接给相应的后端反馈
后端服务负载, 异常率：
并发高, 对方负载就高, 异常率就高
浏览器单个访问, 负载低, 不异常, 就很正常
```

4. excel交互解决（采用的是elementui库，但是上传的文件无法覆盖上次上传的文件，因此需要手动删除再进行上传），解决方案如下：

```
   <el-upload
        ref="upload"
        :action="/tts/import`"
        accept="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
        :on-success="onImportSuccess"
        :on-error="onImportError"
        name="file"
        :file-list="fileList"  //设置fileList
        :on-exceed="onExceed"  //文件超出指定个数的处理函数
        :limit="1"  //规定文件上传的个数
      >
    </el-upload>

    data(){
        return{
            fileList: [],
        }
    },
    methods:{
        onExceed(file) {
            this.fileList = [
                {
                  name: file[0].name
               }
            ];
        },
    }
```

5. 音频下载时，url中含有中文下载不成功（502），数字字符等下载成功（200），因为没有针对url进行encodeURIComponent转码处理，解决方案如下：

```JavaScript
      window.open(
        `${downloadUrl}?data=${encodeURIComponent(
          JSON.stringify(data)  //如果传递的是json格式，则需要进行序列化 [{id:1,src:'xx'},{id:2,src:'xx'}]
        )}`
      );
      
      // 注意：在后端不进行decode操作
```

6. 在异步下载版本中，下载文件数量过大时，会导致很多请求返回502.因为后端限制了并发数量，因此应该改为同步版本进行下载。解决方案：使用request-promise库可以解决，具体参考[npm文档](https://www.npmjs.com/package/request-promise)
