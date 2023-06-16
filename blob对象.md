# Blob对象

Blob对象表示一个不可变、原始数据的类文件对象。Blob表示的不一定是JavaScript原生格式的数据。File接口基于Blob，继承了blob的功能并将其扩展使其支持用户系统上的文件。

## 创建Blob对象
### 通过构造函数
```js
var blob = new Blob(dataArr:Array<any>, opt:{type:string});
```

- dataArray：数组，包含了要添加到Blob对象中的数据，数据可以是任意多个ArrayBuffer，ArrayBufferView， Blob，或者 DOMString对象。
- opt：对象，用于设置Blob对象的属性（如：MIME类型）

1. 创建一个装填DOMString对象的Blob对象
2. 创建一个装填ArrayBuffer对象的Blob对象
3. 创建一个装填ArrayBufferView对象的Blob对象（ArrayBufferView可基于ArrayBuffer创建，返回值是一个类数组。如下：创建一个8字节的ArrayBuffer，在其上创建一个每个数组元素为2字节的“视图”）

### 通过Blob.slice()方法
此方法返回一个新的Blob对象，包含了原Blob对象中指定范围内的数据
```js
var newBlob = Blob.slice(start:number, end:number, contentType:string);
```
- start：开始索引，默认为0
- end：截取结束索引（不包括end）
- contentType：新Blob的MIME类型，默认为空字符串

### 通过canvas.toBlob()方法
此方法将canvas元素的内容转化为Blob对象
```js
var canvas = document.getElementById("canvas");
canvas.toBlob(function(blob){
    console.log(blob);
});
```
### 应用场景
前面提到，File接口基于Blob，继承了Blob的功能并进行了扩展，故我们可以像使用Blob一样使用File对象。

## 分片上传
通过Blob.slice方法，可以将大文件分片，轮循向后台提交各文件片段，即可实现文件的分片上传。
分片上传逻辑如下：

- 获取要上传文件的File对象，根据chunk（每片大小）对文件进行分片
- 通过post方法轮循上传每片文件，其中url中拼接querystring用于描述当前上传的文件信息；post body中存放本次要上传的二进制数据片段
- 接口每次返回offset，用于执行下次上传
下面是分片上传的简单实现：
```js
initUpload();
 
//初始化上传
function initUpload() {
    var chunk = 100 * 1024;   //每片大小
    var input = document.getElementById("file");    //input file
    input.onchange = function (e) {
        var file = this.files[0];
        var query = {};
        var chunks = [];
        if (!!file) {
            var start = 0;
            //文件分片
            for (var i = 0; i < Math.ceil(file.size / chunk); i++) {
                var end = start + chunk;
                chunks[i] = file.slice(start , end);
                start = end;
            }
            
            // 采用post方法上传文件
            // url query上拼接以下参数，用于记录上传偏移
            // post body中存放本次要上传的二进制数据
            query = {
                fileSize: file.size,
                dataSize: chunk,
                nextOffset: 0
            }
 
            upload(chunks, query, successPerUpload);
        }
    }
}
 
// 执行上传
function upload(chunks, query, cb) {
    var queryStr = Object.getOwnPropertyNames(query).map(key => {
        return key + "=" + query[key];
    }).join("&");
    var xhr = new XMLHttpRequest();
    xhr.open("POST", "http://xxxx/opload?" + queryStr);
    xhr.overrideMimeType("application/octet-stream");
    
    //获取post body中二进制数据
    var index = Math.floor(query.nextOffset / query.dataSize);
    getFileBinary(chunks[index], function (binary) {
        if (xhr.sendAsBinary) {
            xhr.sendAsBinary(binary);
        } else {
            xhr.send(binary);
        }
 
    });
 
    xhr.onreadystatechange = function (e) {
        if (xhr.readyState === 4) {
            if (xhr.status === 200) {
                var resp = JSON.parse(xhr.responseText);
                // 接口返回nextoffset
                // resp = {
                //     isFinish:false,
                //     offset:100*1024
                // }
                if (typeof cb === "function") {
                    cb.call(this, resp, chunks, query)
                }
            }
        }
    }
}
 
// 每片上传成功后执行
function successPerUpload(resp, chunks, query) {
    if (resp.isFinish === true) {
        alert("上传成功");
    } else {
        //未上传完毕
        query.offset = resp.offset;
        upload(chunks, query, successPerUpload);
    }
}
 
// 获取文件二进制数据
function getFileBinary(file, cb) {
    var reader = new FileReader();
    reader.readAsArrayBuffer(file);
    reader.onload = function (e) {
        if (typeof cb === "function") {
            cb.call(this, this.result);
        }
    }
}
```
以上是文件分片上传前端的简单实现，当然，此功能还可以更加完善，如后台需要对合并后的文件大小进行校验；或者前端加密文件，全部上传完毕后后端解密校验等，此处不做赘述。

## 通过url下载文件
window.URL对象可以为Blob对象生成一个网络地址，结合a标签的download属性，可以实现点击url下载文件
实现如下：
```js
createDownload("download.txt","download file");
 
function createDownload(fileName, content){
    var blob = new Blob([content]);
    var link = document.createElement("a");
    link.innerHTML = fileName;
    link.download = fileName;
    link.href = URL.createObjectURL(blob);
    document.getElementsByTagName("body")[0].appendChild(link);
}
```
执行后页面上会生成此Blob对象的地址，点击后可下载

## 下载excel文件
res可以是后台返回数据
```js
let res = [];
        const blob = new Blob([res], { type: 'application/vnd.ms-excel' });
        const link = document.createElement('a');
 
        link.href = window.URL.createObjectURL(blob);
        link.download = '报表';
        link.click();
```
## 通过url显示图片
我们知道，img的src属性及background的url属性，都可以通过接收图片的网络地址或base64来显示图片，同样的，我们也可以把图片转化为Blob对象，生成URL（URL.createObjectURL(blob)），来显示图片。

## base64转url显示和下载

### dataurl，base64格式

```
data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMUAAADECAIAAACgBnICAAABPGlDQ1BJQ0MgUHJvZmlsZQAAKJFjYGDiSCwoyGFhYGDIzSspCnJ3UoiIjFJgf87AxsDOwMXAwmCdmFxc4BgQ4ANUwgCjUcG3awyMIPqyLsgsw08Fc+7ftHI7dutqrefvNacx1aMArpTU4mQg/QeIo5MLikoYGBgjgGzl8pICELsGyBYpAjoKyJ4AYqdD2EtA7CQIewdYTUiQM5B9BsgWSM5ITAGy7wDZOklI4ulIbKi9IMBIwI0kg5LUihIQ7ZxfUFmUmZ5RouAIDJlUBc+8ZD0dBSMDI2MGBlA4Q1R/vgF2AaMYB0KscjoDg901oGAPQiwqj4FhnS8Dg8QphJjSRQYGobsMDDuuFyQWJcIdwPiNpTjN2AjC5t7OwMA67f//z+EMDOyaDAx/r////3v7//9/lzEwMN9iYDjwDQB1a2AsngN5eAAAAGxlWElmTU0AKgAAAAgABAEaAAUAAAABAAAAPgEbAAUAAAABAAAARgEoAAMAAAABAAIAAIdpAAQAAAABAAAATgAAAAAAAABIAAAAAQAAAEgAAAABAAKgAgAEAAAAAQAAAMWgAwAEAAAAAQAAAMQAAAAAPyaneQAAAAlwSFlzAAALEwAACxMBAJqcGAAAA25JREFUeAHt0kERACAMA0HAv6DK6+DhnhsBedzsnZljCkQFXvTjRoFfgCcOygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV9LWWiA7Dg6i2LAAAAAElFTkSuQmCC
```

### 切割数据

```
const arr = dataurl.split(',')
[
    "data:image/png;base64",
    "iVBORw0KGgoAAAANSUhEUgAAAMUAAADECAIAAACgBnICAAABPGlDQ1BJQ0MgUHJvZmlsZQAAKJFjYGDiSCwoyGFhYGDIzSspCnJ3UoiIjFJgf87AxsDOwMXAwmCdmFxc4BgQ4ANUwgCjUcG3awyMIPqyLsgsw08Fc+7ftHI7dutqrefvNacx1aMArpTU4mQg/QeIo5MLikoYGBgjgGzl8pICELsGyBYpAjoKyJ4AYqdD2EtA7CQIewdYTUiQM5B9BsgWSM5ITAGy7wDZOklI4ulIbKi9IMBIwI0kg5LUihIQ7ZxfUFmUmZ5RouAIDJlUBc+8ZD0dBSMDI2MGBlA4Q1R/vgF2AaMYB0KscjoDg901oGAPQiwqj4FhnS8Dg8QphJjSRQYGobsMDDuuFyQWJcIdwPiNpTjN2AjC5t7OwMA67f//z+EMDOyaDAx/r////3v7//9/lzEwMN9iYDjwDQB1a2AsngN5eAAAAGxlWElmTU0AKgAAAAgABAEaAAUAAAABAAAAPgEbAAUAAAABAAAARgEoAAMAAAABAAIAAIdpAAQAAAABAAAATgAAAAAAAABIAAAAAQAAAEgAAAABAAKgAgAEAAAAAQAAAMWgAwAEAAAAAQAAAMQAAAAAPyaneQAAAAlwSFlzAAALEwAACxMBAJqcGAAAA25JREFUeAHt0kERACAMA0HAv6DK6+DhnhsBedzsnZljCkQFXvTjRoFfgCcOygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV98cRAWYCnsqYvnhgoC/BU1vTFEwNlAZ7Kmr54YqAswFNZ0xdPDJQFeCpr+uKJgbIAT2VNXzwxUBbgqazpiycGygI8lTV9LWWiA7Dg6i2LAAAAAElFTkSuQmCC"
]
```

### 获取数据格式

```
const mime = arr[0].match(/:(.*?);/)[1]

image/png
```

### 获取数据类型

```
const suffix = mime.split('/')[1]

png
```

### 解码base64字符串

- atob() //ASCII to Base64
- btoa() //Base64 to ASCII

```
const bstr = atob(arr[1])

PNG

   
IHDR   Å   Ä    r  <iCCPICC Profile  (c``âH,(Èaa``ÈÍ+)
rwRR`ÎÀÆÀÎÀÅÀÂ`\\ààTÂ £QÁ·k ú².È,ÃOsîß´r;vëj­çï5§1Õ£ ®Ôâd ý£J#låò»È):
È b§CØK@ì${XMH3}ÈHÎHL²ï Ù:IHâéHl¨½ ÀHÀ$Ôí_PYQ¢àTÏ¼d=##cP8CT¾v£B¬r:Ý5 `B,*a/Ä)ÒE¡»;®$%ÂÀø¥8ÍØÂæÞÎÀÀ:íÿÿÏáì¯ÿÿÿ{ûÿÿ100ßb`8ð
 uk`,yx   leXIfMM *           >       F(       i       N       H      H            Å        Ä    ?&§y   	pHYs       nIDATxíÒA  AÀ¿ ÊëàáyÜìc
D^ôãF_'Ê<5}ñÄ@Y§²¦/(ðTÖôÅeÊ¾xb ,ÀSYÓOx*kúâ² OeM_<1Pà©¬é'Ê<5}ñÄ@Y§²¦/(ðTÖôÅeÊ¾xb ,ÀSYÓOx*kúâ² OeM_<1Pà©¬é'Ê<5}ñÄ@Y§²¦/(ðTÖôÅeÊ¾xb ,ÀSYÓOx*kúâ² OeM_<1Pà©¬é'Ê<5}ñÄ@Y§²¦/(ðTÖôÅeÊ¾xb ,ÀSYÓOx*kúâ² OeM_<1Pà©¬é'Ê<5}ñÄ@Y§²¦/(ðTÖôÅeÊ¾xb ,ÀSYÓOx*kúâ² OeM_<1Pà©¬é'Ê<5}ñÄ@Y§²¦/(ðTÖôÅeÊ¾xb ,ÀSYÓOx*kúâ² OeM_<1Pà©¬é'Ê<5}ñÄ@Y§²¦/(ðTÖôÅeÊ¾xb ,ÀSYÓOx*kúâ² OeM_<1Pà©¬é'Ê<5}ñÄ@Y§²¦/(ðTÖôÅeÊ¾xb ,ÀSYÓOx*kúâ² OeM_<1Pà©¬é'Ê<5}ñÄ@Y§²¦/(ðTÖôÅeÊ¾xb ,ÀSYÓOx*kúâ² OeM_<1Pà©¬é'Ê<5}ñÄ@Y§²¦/(ðTÖôÅeÊ¾xb ,ÀSYÓOx*kúâ² OeM_<1Pà©¬é'Ê<5}ñÄ@Y§²¦/(ðTÖôÅeÊ¾xb ,ÀSYÓOx*kúâ² OeM_<1Pà©¬é'Ê<5}ñÄ@Y§²¦/(ðTÖôÅeÊ¾xb ,ÀSYÓOx*kúâ² OeM_<1Pà©¬é'Ê<5}-e¢°àê-    IEND®B`
```

### 获取ut8编码格式数据

```
const u8arr = new Uint8Array(n)
while (n--) {
	u8arr[n] = bstr.charCodeAt(n)
}

{
    "0": 137,
    "1": 80,
    "2": 78,
    "3": 71,
    "4": 13,
    "5": 10,
    "6": 26,
    "7": 10,
    "8": 0,
    .......
}
```

### 返回file对象

或者也可以返回blob对象

```
return new File([u8arr], `${filename}.${suffix}`, {
	type: mime
})
```

