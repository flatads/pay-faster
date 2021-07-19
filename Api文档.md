# API文档
## 1. PayServiceNative类

### 1.1 getInstance()
作用：static方法，获取单例对象。
### 1.2 startPayTransaction(Activity activity, PayOrder payOrder, int requestCode)
作用：拉起支付，在UI线程中调用
参数：
Activity：当前Activity，不可为null；
PayOrder：用于支付的订单，保证必填参数都已填；
requestCode：用于启动支付Activity的requestCode，必须>0，在获取支付结果时需要使用。
### 1.3 onActivityResult(String orderId, int requestCode, int resultCode, Intent data, PayServiceListener listener)
作用：static方法，用于获取支付结果，放在Activity#onActivityResult()中，在UI线程中调用。
参数：
orderId：为此处付款的订单号，该值是接入方下单时的订单号；
requestCode：Activity#onActivityResult()中的参数，用于判断是否由Native支付Activity返回；
resultCode： Activity#onActivityResult()中的参数，用于获取状态；
data : Activity#onActivityResult()中的参数，用于获取状态；
listener ： 用于接收支付结果。
### 1.4 payBtnClickReport(Activity activity, String mid, String uid)
作用：事件上报。调用场景在于用户点击支付或充值按钮时，调用该方法上报数据，在UI线程中调用。
注意：该方法不可给用户短时间内频繁调用。
参数：
Activity：当前Activity，不可为null；
mid：分配的唯一值，不可为null；
uid：用户ID，不可为null；
### 1.5 paySucIssueReport(Activity activity, String mid, String uid, String orderId)
作用：事件上报。调用场景在于用户支付或者充值成功后，给用户奖励下发成功时调用，在UI线程中调用。
参数：
Activity：当前Activity，不可为null；
mid：分配的唯一值，不可为null；
uid：用户ID，不可为null；
orderId：为当前付款的订单号，该值是接入方下单时的订单号，即NPayOrder的m_order_id值。
### 1.6 setDebugMode(boolean isDebug)
作用：设置Debug状态，在拉起支付前设置或者初始化时设置。
参数：
isDebug ：传递boolean值。true代表测试环境，false代表正式环境。
### 1.7 isDebugMode()
作用：获取当前Debug状态：true代表测试环境，false代表正式环境。
## 2. PayServiceListener类
下单支付结果回调接口

建议:
这里回调成功的情况下，应该再次和服务器确认支付结果后，确保无误，再给用户下发奖励。确保与服务器数据保持一致
## 3. PayOrder类
该类为订单类，拉起支付传入订单，类中的必填参数请务必填入

## 4. PayError
错误码以及错误信息

## 5. LocationUtil
### 5.1 request(Context context, String mid, CallBack callBack)
作用：获取用户所在国，邦/省。用于产品对入口进入自行拦截，产品侧通过传输IP地址，支付SDK可以确认用户是否在可以支付的地区进行支付。
参数：
context ： 不可为null
mid : 分配的唯一值，不可为null
callBack : 用于接收结果
### 5.2 CallBack
获取用户所在国，邦/省位置信息的回调接口

