# API文档
## 1. PayServiceNative类
```Java
// 获取单例对象
public static PayServiceNative getInstance();

public void startPayTransaction(Activity activity, PayOrder payOrder, int requestCode) 
public static void onActivityResult(String orderId, int requestCode, int resultCode, Intent data, PayServiceListener listener);
    
public void payBtnClickReport(Activity activity, String mid, String uid);
public void paySucIssuedReport(Activity activity, String mid, String uid, String mOrderId);

public void setDebugMode(boolean isDebug);
public boolean isDebugMode();
```
### 1.1 getInstance()
作用：static方法，获取单例对象。  
### 1.2 startPayTransaction(Activity activity, PayOrder payOrder, int requestCode)
作用：拉起支付在UI线程中调用。  
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
作用：事件上报。调用场景在于用户支付或者充值成功后，给用户奖励下发成功时调用，<font color="#dd0000">在UI线程中调用</font>。
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
```Java
下单支付回调接口
/**
* error
* @param  orderId the m_order_id
* @param  payError error code and error msg
* 下单支付失败回调
*/
void onPayError(String m_order_id, PayError payError);
/**
 * backpressed to cancel
 * @param orderId the m_order_id
 * 下单支付时，用户取消支付回调
 */
void onBackPressedCancel(String m_order_id);
/**
 * transaction cancel due to server err
 * @param orderId the m_order_id
 * @param errMsg error code and error msg
 * 下单支付时，由于服务器等原因导致下单支付等待，回调
 */
void onTranPending(String m_order_id, String errMsg);
/**
 * success
 * @param orderId the m_order_id
 * 下单支付成功回调
 */
void onPaySuccess(String m_order_id);
```
建议:
这里回调成功的情况下，应该再次和服务器确认支付结果后，确保无误，再给用户下发奖励。确保与服务器数据保持一致
## 3. PayOrder类
该类为订单类，拉起支付传入订单，类中的必填参数请务必填入
```Java
//分配的mid值，必填
private String mid;

//订单号，必填
private String mOrderId;

//用户id，必填
private String uid;

//支付金额，最多小数后两位，必填
private String payAmount;

//货币单位，默认"INR"，选填
private String unit;

//国家，选填，不填默认 in
private String country;

//电话号码，必填
private String phoneNumber;

//是否是真实手机号，默认false，请传入true，才有卡包功能
private boolean isRealPhone;

//回调url，需要回调填入，或填入空字符串，不可为null
private String callbackUrl;

//订单名或者产品名，必填
private String orderName;

//渠道信息，必填
private String channelId;

//已弃用 客户端类型
private String clientType;

//用户昵称，必填
private String userName;

//邮箱，有就填，建议填入，部分支付要求需要
private String email;

//checksum，必填，服务器生成
private String checksum;
```
## 4. PayError
错误码以及错误信息
```Java
public static final int NETWORK_ERROR = 1;  // 网络错误
public static final int UI_ERROR = 2;       // UI错误
public static final int AUTH_ERROR = 3;     // 授权错误
public static final int TRAN_FAIL = 4;      // 交易失败
public static final int SERVER_ERROR = 5;   // 服务器错误
public static final int LOGIC_ERROR = 6;    // 逻辑错误
public static final int UNKNOWN_ERROR = 7;  // 其他错误
private String mMsg;
private int mCode;
public PayError(int code, String msg) {
    this.mMsg = msg;
    this.mCode = code;
}
public String getMsg() {
    return mMsg;
}
public void setMsg(String msg) {
    this.mMsg = msg;
}
public int getCode() {
    return mCode;
}
public void setCode(int code) {
    this.mCode = code;
}
```
## 5. LocationUtil
### 5.1 request(Context context, String mid, CallBack callBack)
作用：获取用户所在国，邦/省。用于产品对入口进入自行拦截，产品侧通过传输IP地址，支付SDK可以确认用户是否在可以支付的地区进行支付。
参数：
context ： 不可为null
mid : 分配的唯一值，不可为null
callBack : 用于接收结果
### 5.2 CallBack
获取用户所在国，邦/省位置信息的回调接口
```Java
public interface CallBack {
    void onSuccess(@Nullable String country, @Nullable String state, @Nullable String city);

    void onFail(@Nullable String msg);
}
```
