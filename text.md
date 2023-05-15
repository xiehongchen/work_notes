# 查询备案推送记录数据
## 查询格式
{
  "endTime": "2023-05-06T08:48:29.049Z",
  "key": "string",
  "page": 0,
  "pageSize": 0,
  "schoolId": "string",
  "starTime": "2023-05-06T08:48:29.049Z",
  "success": 0,
  "timePlatform": "string"
}
- endTime: 结束时间
- key: 关键字
- page: 当前页
- pageSize: 每页条数
- schoolId: 驾校id
- starTime: 开始时间
- success: 备案结果
- timePlatform: 平台
## 结果
{
  "current": 0,
  "hitCount": true,
  "pages": 0,
  "records": [
    {
      "certNo": "string",
      "id": "string",
      "orderNo": "string",
      "pushTime": "2023-05-06T08:49:09.303Z",
      "requestBody": "string",
      "response": "string",
      "schoolName": "string",
      "sendType": 0,
      "success": 0,
      "timePlatform": "string",
      "userJson": "string",
      "userName": "string"
    }
  ],
  "searchCount": true,
  "size": 0,
  "total": 0
}
- current: 当前页
- hitCount: 是否命中count缓存
- pages: 总页数
- records: 数据
- certNo: 身份证编号
- id: 订单主键
- orderNo: 订单号
- pushTime: 推送时间
- requestBody: 请求体
- response: 响应体
- schoolName: 驾校名称
- sendType: 推送类型
- success: 备案结果 
- timePlatform: 推送平台
- userJson: 用户信息
- userName: 用户名称
- searchCount: 是否命中count缓存
- size: 当前页条数
- total: 总条数

# 导出数据 
/admin/system/api/exportArchiveLog
## 请求格式
{
  "endTime": "2023-05-06T09:15:17.061Z",
  "key": "string",
  "page": 0,
  "pageSize": 0,
  "schoolId": "string",
  "starTime": "2023-05-06T09:15:17.061Z",
  "success": 0,
  "timePlatform": "string"
}


# 接口
# 增加退款操作
后端返回数据

# 班型类型 - 系统后台
没有接口，待开发

> 已增加列表项，后续根据接口返回的数据修改（school-components-calsstable 55）
> 已修改班型类型名称，后续根据接口返回的数据修改（school-class-detail 12）
>

# 自建订单 - 驾校后台

> 修改文字
>  - 新增订单页面需要添加注 ✅
>  - 自建订单页面取消订单功能 ✅待接口取消订单  使用删除订单接口 /end/order/api/removeZJOrder/{id}


# 订单管理 - 驾校和系统
没有接口，待开发

> 修改文字
> - 考试完成 - 培训完成（后端）
> - 存管金额 - 剩余存管金额（order-index 176）✅
> - 申请退款，如果是驾校退款就要显示一个（驾）后端数据返回
> - 优化字段 驾校后台 目前没有数据，后端返回数据后再修改


# 订单详情页面 - 驾校后台 已提交
> 已完成，没有数据，未测试
没有接口，待开发

> 修改文字
> - 

> 学费信息
> - c1可修改为c2，c2可修改为c1，仅一次
> - 状态修改

- 已付款未备案状态下 c1和c2可修改，仅限一次
- 已付款已备案状态下 c2可修改为c1，仅限一次，c1不可修改

- 已付款状态下显示 
- 已付款未备案正常显示弹窗，互相修改（文本覆写）
- 已付款备案只显示修改成c1

逻辑
- 显示按钮，根据备案状态和当前驾培车型修改文本，（需要备案状态、当前驾培车型数据）
1. 弹窗，如果未备案，显示修改相反的
2. 弹窗，如果已备案，显示修改成c1

# 总结
1. 驾校后台 订单详情修改c1或c2 已完成，没有数据，未测试
2. 系统后台 权限设置增加退款操作，后端添加数据即可
3. 驾校后台 自建订单页面添加注，已完成
4. 驾校后台 自建页面详情添加取消订单按钮，待后端返回数据，设置是否显示按钮，未测试
5. 驾校、系统后台 班型信息，已完成，已填充数据，待后端数据返回，修改文字
6. 系统后台 订单管理修改文字，考试完成->培训完成，存管金额->剩余存管金额，剩下的没有数据，还没有做
   这个待后端添加是否驾校申请退款字段，根据该字段是否显示文字
   待后端添加一些数据，然后进行测试即可
7. 驾校后台 订单详情-款项划拨记录-增加划拨结果备注 这个根据后端的hasDissent字段判断，
   1. <span style="color=#ff0000">{{ hasDissent ? (待学员确认) : (学员有异议) }}</span>


# 5.10
1. 自建订单 取消订单  | 后端还没有接口，到时候转换接口就行
2. 订单详情 | 后端增加字段控制显示哪一个，目前我使用hasDissent，true显示学员有异议，false显示待学员确认。后面再修改
3. 班型类型 | 后端还没有字段，待后端增加字段
4. 订单管理 添加标识  | 后端还没字段控制，待后端增加字段

