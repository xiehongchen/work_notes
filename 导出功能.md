# 导出

```js
    expHandler () {
      // 导出消息提示
      const loading = this.$loading({
        lock: true,
        text: '正在导出',
        spinner: 'el-icon-loading',
        background: 'rgba(0, 0, 0, 0.7)'
      })
      // 导出状态，是否为加载中
      this.expLoading = true
      // 获取请求参数
      let params = JSON.parse(JSON.stringify(this.formData))
      if (params.time && params.time.length > 0) {
        params.startTime = params.time[0]
        params.endTime = params.time[1]
      }
      params.success = this.archiveStatus
      if (params.success === 0) params.success = ''
      if (!params.key) delete params.key
      if (!params.schoolId) delete params.schoolId
      if (!params.timePlatform) delete params.timePlatform
      delete params.time
      delete params.page
      // 发送请求
      this.$axios({
        // 请求方法为post
        method: 'post',
        // 请求头为application/octet-stream,二进制流，不知道下载文件类型
        header: { 'Content-Type': 'application/octet-stream' },
        // 响应类型为blob，二进制大对象，用于存储二进制数据
        responseType: 'blob',
        url: '/admin/system/api/exportArchiveLog',
        data: { ...params }
      }).then(res => {
        // 获取响应头中的content-disposition，获取文件名
        const disposition = res.headers['content-disposition']
        const disArr = disposition ? disposition.split(';') : []
        let fileName = ''
        for (let i = 0, len = disArr.length; i < len; i++) {
          if (disArr[i].indexOf('filename=') > -1) {
            fileName = decodeURIComponent(disArr[i].split('=')[1])
          }
        }
        // 创建a标签，设置href为blob对象地址，设置download为文件名
        const blob = new Blob([res.data], { type: 'application/octet-stream' })
        const link = document.createElement('a')
        link.href = window.URL.createObjectURL(blob)
        link.download = fileName ? fileName.replace(/"/g, '') : fileName
        // 模拟点击a标签，下载文件
        document.body.appendChild(link)
        link.click()
        // 删除a标签，释放内存
        document.body.removeChild(link)
        window.URL.revokeObjectURL(link.href)
        // 关闭导出提示
        loading.close()
        this.expLoading = false
        this.$message.success('已成功导出，留意浏览器下载内容')
      }).catch(() => {
        loading.close()
        this.expLoading = false
      })
    },
```

1. 获取请求数据数据
2. 发送请求，方法为post，header为application/octet-stream，responseType为blob
   - application/octet-stream：二进制流，不知道下载文件类型
   - blob：二进制大对象，用于存储二进制数据
3. 通过响应头中的content-disposition，获取文件名 数据为attachment;filename=archiveLog.xlsx
4. 获取文件名 archiveLog.xlsx
5. 使用blob对象存储数据 `const blob = new Blob([res.data], { type: 'application/octet-stream' })` 意思是这个blob对象存储的是二进制流数据
   - 格式为 `const blob = new Blob([数据], { type: '文件类型' })`
6. 创建一个a标签，然后这个a标签的href属性为blob对象的url，download属性为文件名
7. 生成a标签后，模拟点击a标签，下载文件
8. 然后删除a标签，释放内存