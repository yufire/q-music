## cookie长期有效方案

> tips：所说的cookie就是musickey这个参数。

### 一、场景

qq音乐某些接口需要使用cookie才能获取到数据，cookie目前只能通过手动登录，然后复制进行获取。导致非常麻烦，且有效时间很短。



### 二、长期有效方案研究

**1. 现有方案**

网上目前有且能成功的方案都是基于网页自动化实现的方案，这种方案有许多弊端，如：需要浏览器支持、需要输入账号密码、可能会遇到验证码拦截。且并不能保证成功率，可能遇到页面改版，html元素位置改变导致无法进行模拟操作。

**2. 升级方案**

我们可以把网页登录流程提取出来，不借助网页自动化的工具，直接进行http请求进行模拟登录。登录成功后获取musickey，借助定时任务或其它方式定时给musickey续期即可保证”永不”过期。

<p style="color:red">方案我这已经跑通并验证过了，非常持续稳定。🔥</p>

**大概流程如下：**

```mermaid
flowchart LR
      A([获取登录二维码])-->B[用户扫码授权]
      B-->C[Server模拟登录]
      C-->E[获取musickey]
      E-->F[Server定时刷新musickey]
```



### 三、实现效果

1. 先获取二维码的图片，接口返回的是base64格式的，我们暂且借助java的窗体给展示出来，方便扫描。
2. 轮询二维码扫描状态，监听是否完成扫码。

![image-20241009173219005](https://oss.yufire.cn/picGoimage-20241009173219005.png)

3. 轮询获取状态，判断是否为授权成功状态，以下为部分日志，登录成功后会获取到一个code，这个code用于获取musickey。

```tex
2024-10-09 17:33:30状态：二维码未失效。
2024-10-09 17:33:31状态：二维码认证中。
2024-10-09 17:33:37状态：登录成功！
status：302 code：CEFF415C4B3C55S5F6FE317F1AD90999
```

4. 调用接口获取musickey和其它信息
   1. musickey就是我们需要的cookie里的参数，refresh_token用于给musickey进行续期
   2. musickey示例：`Q_H_L_7x5Lk9YAz-3rPjQmH80MdNf-u8tVYf6OoJq4x7Hn5TzFvRkGQWwPvS9oN1VdZgKpTwX9QjL3H7Oe5bJ8cLfFg3M`

![image-20241009173903133](https://oss.yufire.cn/picGoimage-20241009173903133.png)

5. 写一个定时任务，通过refresh_token进行续期。

---

### 四、沟通讨论

email：yufirem@vip.qq.com

> 备注：github cookie讨论

