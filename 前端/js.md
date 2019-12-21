# 前端一些常见问题

## ajax请求下载

1. a标签下载
```html
<a href="url">点击链接下载</a>
```
2. form表单下载
```js
var form = $("<form></form>").attr("action", url).attr("method", "post");
        form.append($("<input></input>").attr("type", "hidden").attr("name", "参数1").attr("value", "值1"));
        form.append($("<input></input>").attr("type", "hidden").attr("name", "参数2").attr("value", "值2"));
        form.appendTo('body').submit().remove();//url为请求地址
```
3. XMLHttpRequest下载

```js
var xhr = new XMLHttpRequest();
var formData = new FormData();
formData.append('参数1', "值1");
formData.append('参数2',"值2");
xhr.open('POST', url, true);
xhr.setRequestHeader("token", token);     //可以带请求头
xhr.responseType = 'blob';
xhr.onload = function (e) {
    if (this.status == 200) {
        var blob = this.response;
        //var responseHeaders = xhr.getAllResponseHeaders();
        //var ContentDisposition = this.getResponseHeader("Content-Disposition");
        // 获取文件名，需要在后端响应头暴露("Access-Control-Expose-Headers", "Content-Disposition");
        //var filename = this.getResponseHeader("Content-Disposition").split('filename=')[1];

        var filename = "xxxx.xxx";
        if (window.navigator.msSaveOrOpenBlob) {
            navigator.msSaveBlob(blob, filename);
        } else {

            blob.type = "application/octet-stream";
            var url = URL.createObjectURL(blob);
            var a = document.createElement('a');
            a.href = url;
            a.download = filename;
            a.click();
            window.URL.revokeObjectURL(url);

        }
    }else if(this.status == 404){
        alert("file does not exist!");
    }else{
        alert("failed to download file! ");
    }

};
xhr.send(formData);  
```