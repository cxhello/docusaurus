用 axios 发送请求下载文件请求后端会出现以下问题，返回的是二进制流格式，如图所示：

![image-20210416164450516](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210416164450516.png)

比较纳闷，后来网上查了各种资料才知道，前端需要 blob 就可以将二进制流文件下载到本地了。

### 实现思路

1. 在页面新建 a 标签 或者按钮点击事件；
2. 创建 Blob 对象，在 Blob 中传入后端返回的 response.data，这一步中 Blob 需要的是一个数组类型的参数, 后端二进制流这边接收到的 response.data 使用查看发现是String, 所以我把 response.data 放进一个长度1的数组, 再传入 Blob 对象，此外需要规定文件类型， 可以是 doc 的`application/vnd.openxmlformats-officedocument.wordprocessingml.document`, 也可以是二进制流的`application/actet-stream`，但是我在项目中并没有指定，也是可以的。
3. 获取下载文件名字，并使用 decodeURI() 函数进行解析；
4. 创建下载链接 window.URL.createObjectURL()；
5. 把步骤四创建的链接变量赋值给 a 标签的 href 属性；
6. 使用 document.body.appendChild() 方法把 a 标签挂上去，再调用 a 标签的 .click() 事件；
7. document.body.removeChild() 移除 a 标签；
8. window.URL.revokeObjectURL() 释放 blob 对象。

### 备注

上面步骤三的操作，从响应头里 `content-disposition` 属性可以获取到文件名，下面代码也给出了解决方案，当然前提是后端接口是要设置这个属性的。

![image-20210416170752440](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210416170752440.png)

### 代码

```vue
axios({
  method: 'post',
  data: parameter,
  url: `/download`,
  headers: {
    'Content-Type': 'application/json',
  },
  responseType: 'blob'
}).then(res => {
  let blob = new Blob([res.data])
  let contentDisposition = res.headers['content-disposition']
  let pattern = new RegExp('filename=([^;]+\\.[^\\.;]+);*')
  let result = pattern.exec(contentDisposition)
  // 使用decodeURI对名字进行解码
  let fileName = decodeURI(result[1])
  let downloadElement = document.createElement('a')
  // 创建下载的链接
  let href = window.URL.createObjectURL(blob)
  downloadElement.style.display = 'none'
  downloadElement.href = href
  // 下载后文件名
  downloadElement.download = fileName
  document.body.appendChild(downloadElement)
  // 点击下载
  downloadElement.click()
  // 下载完成移除元素
  document.body.removeChild(downloadElement)
  // 释放掉blob对象
  window.URL.revokeObjectURL(href)
  }).catch(err => {
    console.log('下载失败')
})
```

### 参考文章

> https://blog.csdn.net/kebi99/article/details/109806643