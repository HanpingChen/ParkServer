# 后台接口使用说明
这里的请求参数中的key。都是在keydic里面对应的。

1. 发送订单请求 CreateOrder
请求参数
```
{
  "orderposition":"要去的地址",
  "ordernumber":"号码",
  "arrivetime":"到达时间"
 }
```
请求示范
```java
        //测试发单
       //需要的请求参数的map
       Map<String,Object>ordermap = new HashMap();
       //号码
        ordermap.put(KeyDic.KEY_ORDERNUMBER,"18170074810");
        //位置对象
        Position position = new Position();
        position.setLatitude(12);
        position.setLongtitude(123);
        position.setCityname("dsfd");
        //调用位置对象重写的tostring方法放入参数map
        ordermap.put(KeyDic.KEY_ORDERPOSITION,position.toString());
        //到达时间（预计达到时间）
        ordermap.put(KeyDic.KEY_ARRIVETIME,"124324");
        //初始化，并实例化返回接口
        CreateOrder createOrder = new CreateOrder(ordermap, new onResponseListener() {
            @Override
            public void onSuccess(JSONObject response) {
                //服务器返回的数据
                System.out.println(response.toString());
            }

            @Override
            public void fail(String msg) {
                System.out.println(msg);
            }
        });

        //发送请求
        createOrder.doPostRequest();
```

服务器响应数据,json格式
```
{
  "status":4,
  "orderid":"181700748101169231555",
  "msg":"订单创建完成，等待接单"
}
```
注意，这里返回的orderid是f订单的id，查询等功能都依赖于这个id，所以客户端要做保存

2. 订单查询接口 OrderQuery
请求参数
```
{
  "ordernumber":"发订单的电话号码",
  "orderid":"订单的id号"（可选）
}
```
请求示范
```java
        //测试查询
        Map<String,Object>requestmap = new HashMap<>();
        requestmap.put(KeyDic.KEY_ORDERID,"181700748101169231610");
        requestmap.put(KeyDic.KEY_ORDERNUMBER,"18170074810");
        OrderQuery query = new OrderQuery(requestmap, new onResponseListener() {
            @Override
            public void onSuccess(JSONObject response) {
                System.out.println(response.toString());
            }

            @Override
            public void fail(String msg) {
                System.out.println(msg);
            }
        });
        query.doPostRequest();
```
服务器相应数据 json格式
```
{
  "status":4,
  "ordernumber":"18170074810",
  "orderid":"181700748101169231631"
  "msg":"正在等待接单"
 }
 这里返回orderid是为了如果出现网络故障而在发单的时候没有接收到服务器的orderid的时候m，也可以调用这个方法查询到。
```

3. 接单者的查询 ProviderQuery

从服务器中拿到当前可以被接的单
请求参数
```
{
  "providerposition":"地址",
  "providernumber":“提供者的手机号"
}
```

请求示范
```
final Map<String,Object>requestmap = new HashMap<>();
        requestmap.put(KeyDic.KEY_PROVIDERNUMBER,"1243243");
        Position position = new Position();
        position.setLatitude(43);
        position.setLongtitude(54);
        position.setCityname("杭州fuuh");
        requestmap.put(KeyDic.KEY_PROVIDERPOSITION,position.toString());
       ProviderQuery query = new ProviderQuery(requestmap, new onProviderQeuryListener() {
           @Override
           public void onSuccess(JSONArray response) {
               System.out.println(response.toString());
           }

           @Override
           public void onFail(String msg) {

           }
       });
        query.doPostRequest();
```

服务器返回数据 jsonarray
```
[
  {
    "status":4,
    "orderposition":"dsfd,123.0,12.0",
    "ordernumber":"18170074810",
    "orderid":"181700748101169231631",
    "arrivetime":"124324"
    }
 ]
```
注，这里得到的orderpostion是一个字符串，可以调用Positon.castStringToPosition(string)将字符串转化为u对象

4. 接单请求 ProviderCreate

请求参数
```
{
  "providerposition":"提供者的地址",
  "ordernumber":"要接的单的手机号",
  "orderid":"要d接的单号",
  "providernumber":"提供者的手机号"
}
```

请求示范
```java
final Map<String,Object>requestmap = new HashMap<>();
        requestmap.put(KeyDic.KEY_PROVIDERNUMBER,"1243243");
        requestmap.put(KeyDic.KEY_ORDERNUMBER,"18170074810");
        requestmap.put(KeyDic.KEY_ORDERID,"181700748101169231648");
        Position position = new Position();
        position.setLatitude(43);
        position.setLongtitude(54);
        position.setCityname("杭州");
        requestmap.put(KeyDic.KEY_PROVIDERPOSITION,position.toString());

        ProviderCreate providerCreate = new ProviderCreate(requestmap, new onResponseListener() {
            @Override
            public void onSuccess(JSONObject response) {
                System.out.println(response.toString());
            }

            @Override
            public void fail(String msg) {

            }
        });
       providerCreate.doPostRequest();
```
服务器响应示范
```
{
  "status":0,
  "ordernumber":"18170074810",
  "msg":"接单成功",
  "arrivetime":"124324",
  "desposition":"dsfd,123.0,12.0"
 }
```

5. 用户确认到达停车地 OrderArrive

请求参数
```
{
  "ordernumber":"1434",
  "orderid":"18170074810116923171"
 }
```

请求示范
```java
Map<String ,Object>resuestmap = new HashMap<>();
        resuestmap.put(KeyDic.KEY_ORDERID,orderid);
        resuestmap.put(KeyDic.KEY_ORDERNUMBER,"1434");
        OrderArrive arrive = new OrderArrive(resuestmap, new onResponseListener() {
            @Override
            public void onSuccess(JSONObject response) {
                System.out.println(response.toString());
            }

            @Override
            public void fail(String msg) {

            }
        });
        arrive.doPostRequest();
```

服务器返回数据实例 json
```
{
  "starttime":"17:47",
  "status":1,
  "msg":"确认到达成功"
 }
```

6. 离开车位，完成订单 OrderFinish

请求参数
```
{
  "ordernumber":"133334",
  "orderid":"18170074810116923171"
 }
```

请求示范
```java
        Map<String ,Object>resuestmap = new HashMap<>();
        resuestmap.put(KeyDic.KEY_ORDERID,orderid);
        resuestmap.put(KeyDic.KEY_ORDERNUMBER,"133334");
        OrderFinish arrive = new OrderFinish(resuestmap, new onResponseListener() {
            @Override
            public void onSuccess(JSONObject response) {
                System.out.println(response.toString());
            }

            @Override
            public void fail(String msg) {

            }
        });
        arrive.doPostRequest();
```
服务器响应
```
{
  "finishtime":"116:9:23:18:59",
  "price":5,
  "timelength":"0小时12分",
  "orderid":"18170074810116923171"
 }
```

