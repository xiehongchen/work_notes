# 关于时间的处理&Date对象

## 创建Date对象

```javascript
const date = new Date()
//Wed Nov 15 2023 17:07:32 GMT+0800 (中国标准时间)
const date = new Date(12312312312)//传时间戳
//Sat May 23 1970 20:05:12 GMT+0800 (中国标准时间)
const date = new Date('2023/2/2')//传日期的字符串值，2023-2-2也行，2023/2/2/2:10:10也行
//hu Feb 02 2023 00:00:00 GMT+0800 (中国标准时间)
const date = new Date(2023, 2, 10, 10, 20, 30)//传对应的数值，年月日时分秒
//Fri Mar 10 2023 10:20:30 GMT+0800 (中国标准时间)
```

## Date对象方法

一般都是使用`getFullYear()`年、`getMonth()`月、`getDate()`日、`getDay()`星期、`getHours()`时、`getMinutes()`分、`getSeconds()`秒、`getMilliseconds()`毫秒，其他的看看就行

### `getDate()`

从Date对象返回一个月中的某一天(1 ~ 31)。

```javascript
console.log(new Date().getDate())
//15
```

### `getDay()`

从 Date 对象返回一周中的某一天 (0 ~ 6)。

```javascript
console.log(new Date().getDay())
//3 就是星期三
```

### `getFullYear()`

从 Date 对象以四位数字返回年份

```javascript
console.log(new Date().getFullYear())
//2023
```

### `getHours()`

返回 Date 对象的小时 (0 ~ 23)。

```javascript
console.log(new Date().getHours())
//17 就是下午五点
```

### `getMilliseconds()`

返回 Date 对象的毫秒(0 ~ 999)。

```javascript
console.log(new Date().getMilliseconds())
//666
```

### `getMinutes()`

返回 Date 对象的分钟 (0 ~ 59)。

```javascript
console.log(new Date().getMinutes())
//20
```

### `getMonth()`

从 Date 对象返回月份 (0 ~ 11)。

```javascript
console.log(new Date().getMonth())
//10 就是十一月份，返回的月份是从0开始的
```

### `getSeconds()`

返回 Date 对象的秒数 (0 ~ 59)。

```javascript
console.log(new Date().getSeconds())
//24
```

### `getTime()`

返回 1970 年 1 月 1 日至今的毫秒数。

```javascript
console.log(new Date().getTime())
//1700040502196
```

### `getTimezoneOffset()`

返回本地时间与格林威治标准时间 (GMT) 的分钟差。

```javascript
console.log(new Date().getTimezoneOffset())
//-480 就是差八个小时
```

### `getUTCDate()`

根据世界时从 Date 对象返回月中的一天 (1 ~ 31)。

```javascript
console.log(new Date().getUTCDate())
//15 这个是世界时间，和我们的时间相差8小时
```

### `getUTCDay()`

根据世界时从 Date 对象返回周中的一天 (0 ~ 6)。

```javascript
console.log(new Date().getUTCDay())
//3 就是星期三
```

### `getUTCFullYear()`

根据世界时从 Date 对象返回四位数的年份。

```javascript
console.log(new Date().getUTCFullYear())
//2023
```

### `getUTCHours()`

根据世界时返回 Date 对象的小时 (0 ~ 23)。

```javascript
console.log(new Date().getUTCFullYear())
//9	当前时间为17，相差8个小时
```

### `getUTCMilliseconds()`

根据世界时返回 Date 对象的毫秒(0 ~ 999)。

```javascript
console.log(new Date().getUTCMilliseconds())
//666
```

### `getUTCMinutes()`

根据世界时返回 Date 对象的分钟 (0 ~ 59)。

```javascript
console.log(new Date().getUTCMinutes())
//23
```

### `getUTCMonth()`

根据世界时从 Date 对象返回月份 (0 ~ 11)。

```javascript
console.log(new Date().getUTCMonth())
//10 就是十一月份
```

### `getUTCSeconds()`

根据世界时返回 Date 对象的秒钟 (0 ~ 59)。

```javascript
console.log(new Date().getUTCSeconds())
//20
```

### `setDate()`

设置 Date 对象中月的某一天 (1 ~ 31)。

- 0为上一个月的最后一天
- -1为上一个月最后一天之前的一天
- 如果当前月份有30天，那么31就是下个月的第一天

```javascript
console.log(new Date().setDate(20))
//1700474167301	返回时间戳
```



## 一些处理方法

### 获取当前月份的所有日期，包括上个月和下个月的总计42天

```typescript
const getVisitDate = computed(() => {
  let arr: Date[] = []
  //获取当前时间的年月
  const { year, month } = formatDate(date.value)
  //获取当月的第一天
  let firstDay = new Date(year, month - 1, 1)
  //获取当月第一天的星期
  let firstWeek = firstDay.getDay()
  //获取42天中的第一天
  const firstVisitDay = new Date(
    firstDay.getTime() - firstWeek * 24 * 60 * 60 * 1000,
  )
  //循环加入即可，每次增加一天
  for (let i = 0; i < 42; i++) {
    let day = new Date(firstVisitDay.getTime() + i * 24 * 60 * 60 * 1000)
    arr.push(day)
  }
  return arr
})
```

