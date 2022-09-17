# 判断非空

## 集合非空

```
CollectionUtils.isEmpty(productDOS)
```

# 公共生成id的工具

```java
String id = UUIDUtils.getUUID();
```

> 这个类是自己创建的

```java
package com.youkeda.dewu.util;

import java.util.UUID;


public class UUIDUtils {
    public static final String getUUID() {
        return UUID.randomUUID().toString().replace("-", "");
    }
}
```

自动生成一个32位（36位去掉4位空格）的id

API如何传入不带注解的参数

```java
@ResponseBody
@GetMapping("/page")
public Result<Paging<Product>> pageQuery(BasePageParam param) {

    Result<Paging<Product>> result = new Result<>();

    result.setSuccess(true);
    result.setData(productService.pageQueryProduct(param));
    return result;
}
```

# dao包

## 常见方法分析

1. 增

   * 单个对象插入
   * 多对象插入（xml文件利用foreach）

2. 删

   * 一般就是根据主键删除

3. 改

   * 一般就是传新的对象

4. 查

   * 根据主键查询

   * 根据主键的List查询（查询多个对象）

     > 用foreach

   * 查询所有

   * 根据其他属性查询

   * 模糊查询（利用CONCAT拼接）

   * 对固定时间段的查询

     > 利用<![CDATA[ >= ]]>

   * 分页模型查询

     > 利用limit
     >
     > ```xml
     > <bind name="currentIndex" value="pagination * pageSize"/>
     > limit #{currentIndex},#{pageSize}
     > ```

   * 返回记录总数

     > ```xml
     > select count(1)
     > ```

## 多参数查询

利用@Param就不需要声明变量类型了

# service包

## UserService

### register方法

```java
public Result<User> register(String userName, String pwd) 
```

#### 1判断变量是否为空

```java
if (StringUtils.isEmpty(userName)) {
    result.setCode("600");
    result.setMessage("用户名不能为空");
    return result;
}
if (StringUtils.isEmpty(pwd)) {
    result.setCode("601");
    result.setMessage("密码不能为空");
    return result;
}
```

#### 2查找是否已存在该对象

```java
UserDO userDO = userDAO.findByUserName(userName);
```

#### 3判断该对象是否已存在

```java
if (userDO != null) {
    result.setCode("602");
    result.setMessage("用户名已经存在");
    return result;
}
```

#### 4对密码加密

```java
// 密码加自定义盐值，确保密码安全
String saltPwd = pwd + "_ykd2050";
// 生成md5值，并转大写字母
String md5Pwd = DigestUtils.md5Hex(saltPwd).toUpperCase();
```

#### 5封装对象

```java
UserDO userDO1 = new UserDO();
userDO1.setUserName(userName);
// 初始昵称等于用户名
userDO1.setNickName(userName);
userDO1.setPwd(md5Pwd);

userDAO.add(userDO1);

result.setSuccess(true);

result.setData(userDO1.toModel());

return result;
```

### login方法

```java
public Result<User> login(String userName, String pwd)
```

#### 1判断参数

```java
if (StringUtils.isEmpty(userName)) {
    result.setCode("600");
    result.setMessage("用户名不能为空");
    return result;
}
if (StringUtils.isEmpty(pwd)) {
    result.setCode("601");
    result.setMessage("密码不能为空");
    return result;
}

UserDO userDO = userDAO.findByUserName(userName);
if (userDO == null) {
    result.setCode("602");
    result.setMessage("用户名不存在");
    return result;
}
```

#### 2获得密码

```java
// 密码加自定义盐值，确保密码安全
String saltPwd = pwd + "_ykd2050";
// 生成md5值，并转大写字母
String md5Pwd = DigestUtils.md5Hex(saltPwd).toUpperCase();
```

#### 3验证

```java
if (!StringUtils.equals(md5Pwd, userDO.getPwd())) {
    result.setCode("603");
    result.setMessage("密码不对");
    return result;
}
```

#### 4封装数据

```java
result.setSuccess(true);

result.setData(userDO.toModel());

return result;
```

## ProductService

### pageQueryProduct

```java
public Paging<Product> pageQueryProduct(BasePageParam param)
```

#### 1判断参数

```java
if (param == null) {
    return result;
}

if (param.getPagination() < 0) {
    param.setPagination(0);
}

if (param.getPageSize() < 0) {
    param.setPageSize(10);
}
```

#### 2查询总数

```java
//查询数据总数
int counts = productDAO.selectAllCounts();
```

#### 3判断总数是否合理

```java
if (counts < 0) {
    return result;
}
```

#### 4获得总页数

```java
int totalPage = (int)Math.ceil(counts / (param.getPageSize() * 1.0));
```

#### 5获得数据

```java
List<ProductDO> productDOS = productDAO.pageQuery(param);
List<Product> productList = new ArrayList<>();
if (!CollectionUtils.isEmpty(productDOS)) {
    for (ProductDO productDO : productDOS) {
        productList.add(productDO.convertToModel());
    }
}
```

#### 6封装数据

```java
result.setTotalCount(counts);
result.setPageSize(param.getPageSize());
result.setPageNum(param.getPagination());
result.setTotalPage(totalPage);
result.setData(productList);O
```

## OrderService

### updateProductPersonNumber

```java
public Order updateProductPersonNumber(String orderNumber)
```

> 用于更新库存量和销量

#### 1判断参数(这一步少了，方法里应该缺了)

#### 2判断获得的订单对象

```java
OrderDO orderDO = orderDAO.selectByOrderNumber(orderNumber);
if (orderDO == null) {
    return null;
}
```

#### 3获得分布式锁

```java
RLock transferLock = redisson.getLock("PURCHASE");
transferLock.lock();
```

#### 4查询货物详情对象

```java
ProductDetail productDetail = productDetailService.get(orderDO.getProductDetailId());
if (productDetail == null) {
    return null;
}
```

#### 5减少其库存量并保存

```java
productDetail.setStock(productDetail.getStock() - 1);
productDetailService.save(productDetail);
```

#### 6获得产品对象，并增加其销售量，进行保存

```java
Product product = productService.get(productDetail.getProductId());
product.setPurchaseNum(product.getPurchaseNum() + 1);
productService.save(product);
```

#### 7解锁

```java
catch (Exception e) {
    logger.error("", e);
} finally {
    transferLock.unlock();
}
```

### queryRecentPaySuccess

```java
public Paging<Order> queryRecentPaySuccess(QueryOrderParam queryOrderParam) 
```

> 查询最近的成功订单

#### 1判断参数

```java
if (queryOrderParam == null) {
    queryOrderParam = new QueryOrderParam();
}
```

#### 2获得订单总数

```java
int counts = orderDAO.selectCounts(queryOrderParam);

if (counts < 1) {
    result.setTotalCount(0);
    return result;
}
```

#### 3查询相应的订单

```java
List<OrderDO> orderDOS = orderDAO.pageQuery(queryOrderParam);
```

#### 4获得订单中的用户和产品对象

```java
for (OrderDO orderDO : orderDOS) {
    orders.add(orderDO.convertToModel());
    productDetailIds.add(orderDO.getProductDetailId());
    userIds.add(Long.parseLong(orderDO.getUserId()));
}
List<User> users = userService.queryUser(userIds);
        List<ProductDetail> productDetails = productDetailService.queryProductDetail(productDetailIds);
        Map<String, ProductDetail> productDetailMap = productDetails.stream().collect(
            Collectors.toMap(ProductDetail::getId, t -> t));
        Map<Long, User> userMap = users.stream().collect(Collectors.toMap(User::getId, t -> t));
```

#### 5封装进订单的属性中

```java
for (Order order : orders) {
    order.setProductDetail(productDetailMap.get(order.getProductDetailId()));
    order.setUser(userMap.get(Long.parseLong(order.getUserId())));
}
```

#### 6封装结果

```java
result.setData(orders);
result.setTotalCount(counts);
result.setPageNum(orders.size());
result.setPageSize(queryOrderParam.getPageSize());
result.setTotalPage(orders.size() / queryOrderParam.getPageNum());
```

### createOrderNumber

```java
private String createOrderNumber()
```

> 生成唯一的订单号

```java
private String createOrderNumber() {
    LocalDateTime localDateTime = LocalDateTime.now();
    DateTimeFormatter dtf2 = DateTimeFormatter.ofPattern("yyyyMMddHHmmssSSS");
    String date = dtf2.format(localDateTime);
    RAtomicLong aLong = redisson.getAtomicLong("myOrderPartNum" + date);
    aLong.expire(10, TimeUnit.SECONDS);
    long count = aLong.incrementAndGet();
    String orderId = date + count;
    return orderId;
}
```

## PaymentRecordService

#### save

```java
public PaymentRecord save(PaymentRecord paymentRecord)
```

1. 判断参数

   ```java
   if (paymentRecord == null) {
       return null;
   }
   ```

2. 判断其Id是否存在，是更新还是添加

3. 添加的话，就对其设置id

   ```java
   if (StringUtils.isEmpty(paymentRecord.getId())) {
       PaymentRecordDO paymentRecordDO = new PaymentRecordDO(paymentRecord);
       paymentRecordDO.setId(UUIDUtils.getUUID());
       int insertSize = paymentRecordDAO.insert(paymentRecordDO);
       if (insertSize < 1) {
           return null;
       }
       paymentRecord.setId(paymentRecordDO.getId());
       return paymentRecord;
   } 
   ```

4. 更新的话，就直接更改内容

   ```java
   PaymentRecordDO paymentRecordDO = new PaymentRecordDO(paymentRecord);
   int updateSize = paymentRecordDAO.update(paymentRecordDO);
   if (updateSize < 1) {
       return null;
   }
   return paymentRecord;
   ```

## PayService

### aliPay

```java
public Result aliPay(String orderId, PaymentParam paymentParam)
```

#### 1实例化客户端

```java
AlipayClient alipayClient = new DefaultAlipayClient("https://openapi.alipay.com/gateway.do", this.aliPayAppId,
                                                    this.aliPayAppPrivateKey, "json", "UTF-8",
                                                    this.aliPayPublicKey, "RSA2");
```

#### 2创建API对应的request

```java
AlipayTradeWapPayRequest request = getAlipayTradeWapPayRequest(orderId, paymentParam);
```

#### 3调用SDK生成表单

```java
response = alipayClient.pageExecute(request);
```

#### 4更新支付记录

```java
String channelId = response.getTradeNo();
//更新支付记录
updatePayRecord(null, channelId, PayType.ALIPAY.toString(), paymentParam.getOrderNumber(),
                PaymentStatus.PENDING);
```

### alipayCallBack

```java
public Result alipayCallBack(Map<String, String> mapParam)
```

> 支付回调

#### 1取得response得到的各个参数

```java
String status = mapParam.get("trade_status");
String orderNum = mapParam.get("out_trade_no");
String endTime = mapParam.get("gmt_close");
```

#### 2根据订单号获得对应的订单

```java
Order order = orderService.getByOrderNumber(orderNum);
```

#### 3判断对应的订单是否为空

#### 4更新订单状态

#### 5更新支付流水

#### 6更新库存和销量

```java
if (order != null) {
    //交易成功
    if ("TRADE_SUCCESS".equals(status)) {
        // 更新订单状态
        orderService.updateOrderStatus(orderNum, OrderStatus.TRADE_PAID_SUCCESS);

        //更新支付流水
        PaymentRecordQueryParam queryParam = new PaymentRecordQueryParam();
        queryParam.setOrderNumber(orderNum);
        List<PaymentRecord> paymentRecords = paymentRecordService.query(queryParam);
        if (!CollectionUtils.isEmpty(paymentRecords)) {
            PaymentRecord paymentRecord = paymentRecords.get(0);
            paymentRecord.setPayStatus(PaymentStatus.SUCCESS);
            //更新支付流水状态
            paymentRecordService.updateStatus(paymentRecord);
        }
        orderService.updateProductPersonNumber(orderNum);
    }

    //交易关闭
    if ("TRADE_CLOSED".equals(status)) {
        // 更新订单状态
        orderService.updateOrderStatus(orderNum, OrderStatus.TRADE_PAID_FAILED);

        //更新支付流水
        PaymentRecordQueryParam queryParam = new PaymentRecordQueryParam();
        queryParam.setOrderNumber(orderNum);
        List<PaymentRecord> paymentRecords = paymentRecordService.query(queryParam);
        if (!CollectionUtils.isEmpty(paymentRecords)) {
            PaymentRecord paymentRecord = paymentRecords.get(0);
            paymentRecord.setPayStatus(PaymentStatus.FAILURE);
            //更新支付流水状态
            paymentRecordService.updateStatus(paymentRecord);
        }
    }
}
```

### getAlipayTradeWapPayRequest

```java
private AlipayTradeWapPayRequest getAlipayTradeWapPayRequest(String orderId, PaymentParam paymentParam)
```

> 封装request

#### 1设置相应的非Map参数

```java
request.setApiVersion("1.0");
request.setReturnUrl("");
request.setNotifyUrl("");
```

#### 2设置Map参数

```java
bizContentMap.put("out_trade_no", orderId);
bizContentMap.put("total_amount", paymentParam.getPayAmount());
bizContentMap.put("quit_url", "www.youkeda.com");
bizContentMap.put("product_code", "QUICK_WAP_WAY");
```

#### 3转化Map

```java
request.setBizContent(objectMapper.writeValueAsString(bizContentMap));
```

# control包

## PaymentController

### alipaycallback

```java
@PostMapping(path = "/alipaycallback")
void alipaycallback(HttpServletRequest request, HttpServletResponse response)
```

#### 1获取Map

```java
Map requestParams = request.getParameterMap();
```

#### 2组装所需要的数据

```java
for (Iterator iter = requestParams.keySet().iterator(); iter.hasNext(); ) {
    String name = (String)iter.next();
    String[] values = (String[])requestParams.get(name);
    String valueStr = "";
    for (int i = 0; i < values.length; i++) {
        valueStr = (i == values.length - 1) ? valueStr + values[i] : valueStr + values[i] + ",";
    }
    //乱码解决，这段代码在出现乱码时使用。如果mysign和sign不相等也可以使用这段代码转化
    //valueStr = new String(valueStr.getBytes("ISO-8859-1"), "gbk");
    params.put(name, valueStr);
}
```

#### 3调用回调接口

```java
Result payResult = payService.alipayCallBack(params);
```

4打印是否成功

```java
try {
    if (payResult.isSuccess()) {
        response.getWriter().print("success");
        response.getWriter().flush();
    }
} catch (IOException e) {
    logger.error("", e);
}
```

## PaymentApi

### payOrder

```java
public Result payOrder(@RequestBody PaymentParam paymentParam) 
```

#### 1判断id

```java
if (StringUtils.isEmpty(paymentParam.getUserId())) {
    result.setSuccess(false);
    result.setMessage("userId is null");
    return result;
}
```

2判断金额

```java
if (paymentParam.getPayAmount() <= 0) {
    result.setMessage("支付金额不能小于0");
    return result;
}
```

3组装消费流水记录

```java
PaymentRecord paymentRecord = initPaymentRecord(paymentParam);
PaymentRecord saveResult = paymentRecordService.save(paymentRecord);
```

4执行支付行为

```java
return payService.payOrder(paymentParam);
```
