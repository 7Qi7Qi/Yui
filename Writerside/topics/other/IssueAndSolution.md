# IssueAndSolution

## 重复请求api，如何判断？

> [帖子](https://v2ex.com/t/839954#reply18)
> 除了发送时间不同，数据都是一模一样的。

### Solution
+ 使用{token+url}做分布式锁key，1秒之后释放；否则拒绝掉其中一个请求，提示请求过于频繁
+ Post接口幂等性，前端做防抖
  + debounce 去抖动。当事件被触发时，设定一个周期延迟执行动作，若期间又被触发，则重新设定周期，直至周期结束执行操作。
+ api加个uuid和时间戳，比较时间范围和uui重复
+ 用seq标记，客户端发起post请求时带上seq，服务器回复时seq+1，客户端更新seq。后端过滤掉重复的seq

## 验证短信码被盗刷

[todo](https://v2ex.com/t/845136)