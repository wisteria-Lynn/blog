---
title: 'file,blob,base64格式转换'
comments: true
date: 2019-10-30 08:55:08
categories:
 - js
tags:
img:
---

## File

通常情况下， File 对象是来自用户在一个 `<input> `元素上选择文件后返回的 FileList 对象,也可以是来自由拖放操作生成的 DataTransfer 对象，或者来自 HTMLCanvasElement 上的 mozGetAsFile() API。

File 对象是特殊类型的 Blob，且可以用在任意的 Blob 类型的 context中。

__构造函数__

```js
var myFile = new File(bits, name[, options]);
// 例如
var file = new File(["foo"], "foo.txt", {
  type: "text/plain",
});
Object.prototype.toString.call(file)// [object File]
```

参数：

* bits
  ArrayBuffer，ArrayBufferView，Blob，或者 DOMString 对象的 Array — 或者任何这些对象的组合。这是 UTF-8 编码的文件内容。
    
* name

  USVString，表示文件名称，或者文件路径。

* options(可选)

  选项对象，包含文件的可选属性。可用的选项如下：
  * type: DOMString，表示将要放到文件中的内容的 MIME 类型。默认值为 "" 。
  * lastModified: 数值，表示文件最后修改时间的 Unix 时间戳（毫秒）。默认值为 Date.now()。
  
__属性__

File 接口也继承了 Blob 接口的属性：

* File.lastModified 只读
返回当前 File 对象所引用文件最后修改时间，自 UNIX 时间起始值（1970年1月1日 00:00:00 UTC）以来的毫秒数。

* File.lastModifiedDate 只读 
返回当前 File 对象所引用文件最后修改时间的 Date 对象。

* File.name 只读
返回当前 File 对象所引用文件的名字。

* File.size 只读
返回文件的大小。

* File.webkitRelativePath 只读 
返回 File 相关的 path 或 URL。

* File.type 只读
返回文件的 多用途互联网邮件扩展类型（MIME Type）

__方法__

File 接口没有定义任何方法，但是它从 Blob 接口继承了以下方法：

Blob.slice([start[, end[, contentType]]])
返回一个新的 Blob 对象，它包含有源 Blob 对象中指定范围内的数据。

> file可分blob碎片提交

__浏览器兼容__

Edge/IE 不支持File的构造函数

## File转url

```js
let _URL = window.URL || window.webkitURL;
let url = _URL.createObjectURL(file|blob)
// 释放
_URL.revokeObjectURL(url)
```

## base64转Blob

```js
function dataURLtoBlob(dataurl) {
    var arr = dataurl.split(','), mime = arr[0].match(/:(.*?);/)[1],
        bstr = atob(arr[1]), n = bstr.length, u8arr = new Uint8Array(n);
    while (n--) {
        u8arr[n] = bstr.charCodeAt(n);
    }
    return new Blob([u8arr], { type: mime });
}
```

## Blob转为base64

```js
function blobToDataURL(blob, callback) {
    let a = new FileReader();
    a.onload = function (e) { callback(e.target.result); }
    a.readAsDataURL(blob);
}
```

## base64转File

```js
function dataURLtoFile(dataurl, filename) {//将base64转换为文件
        var arr = dataurl.split(','), mime = arr[0].match(/:(.*?);/)[1],
            bstr = atob(arr[1]), n = bstr.length, u8arr = new Uint8Array(n);
        while(n--){
            u8arr[n] = bstr.charCodeAt(n);
        }
        return new File([u8arr], filename, {type:mime});
    }
```

## url转base64

```js
function getImgToBase64(url,callback){//将图片转换为Base64
  var canvas = document.createElement('canvas'),
    ctx = canvas.getContext('2d'),
    img = new Image;
  img.crossOrigin = 'Anonymous';
  img.onload = function(){
    canvas.height = img.height;
    canvas.width = img.width;
    ctx.drawImage(img,0,0);
    var dataURL = canvas.toDataURL('image/png');
    callback(dataURL);
    canvas = null;
  };
  img.src = url;
}
```