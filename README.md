# autoBuryPoint

客户端需要记录诸如pv、uv、路径和事件等。如果通过硬编码的方式写入方法或者代码中，会引发埋点代码耦合紧，难以检查是否错埋漏埋等问题。autoBuryPoint提供一套自动化埋点的方案。

1、支持pv生命周期和页面路径追踪自动化埋点。
2、支持click事件的自动埋点（包含```butterknife```）
3、支持配置文件配置指定方法的埋点。
4、切面和注解等方式结合，有效的解耦代码。

## 使用 ##
### 初始化 ###
```java
  //初始化自动化埋点
  TraceDataAPI.getInstance(this)
      .setConfigureUrl("http://")
      .setDebugEnable(true)
      .setmFlushBulkSize(10);//上传的最大条数
```

### 基类自动埋点 ###
```java
public class BaseActivity extends AppCompatActivity {

    @PointLife(LifeType = LifeType.onCreate)
    @Override
    public void onCreate(Bundle savedInstanceState, PersistableBundle persistentState) {
        super.onCreate(savedInstanceState, persistentState);
    }

    @PointLife(LifeType = LifeType.onResume, PVType = PVType.onPageStart)
    @Override
    protected void onResume() {
        super.onResume();
        //自动埋点所有的onclick点击事件
        TraceDataAPI.getInstance(this).autoTrackClickEvent(findViewById(android.R.id.content));
    }

    @PointLife(LifeType = LifeType.onPause, PVType = PVType.onPageEnd)
    @Override
    protected void onPause() {
        super.onPause();
    }

    @PointLife(LifeType = LifeType.onStop)
    @Override
    protected void onStop() {
        super.onStop();
    }

}
```
@PointLife 使用aop切面编程的原理为生命周期自动埋点处理，参数LifeType，PVType分别用于生命周期和页面路径。


### 自定事件配置 ###
配置文件实例:
```
<?xml version="1.0" encoding="UTF-8"?>
<root>
    <!--event config-->
    <events>
        <event event_code="1001">
            <event_name>orderDetail</event_name>
            <event_method>testMethod</event_method>
            <event_desc>进入订单详情</event_desc>
        </event>
        <event event_code="1002">
            <event_name>testEvent</event_name>
            <event_method>hello2</event_method>
            <event_desc>测试</event_desc>
        </event>
    </events>

    <!--page config-->
    <pages>
        <page page_code="20001">
            <page_name>MainActivity</page_name>
            <page_desc>首页</page_desc>
        </page>

        <page page_code="20002">
            <page_name>OrderDetailActivity</page_name>
            <page_desc>订单详情页面</page_desc>
        </page>
    </pages>
</root>
```
配置文件可以用服务端下发。一般包含需要追踪的页面的配置以及需要事件方法的配置。

事件数据提供方式
```java
  @Point(key = "testEvent")
  public void hello2(View view){
    Log.d(TAG,"hello2");
  }
    
    
  @Override
  public JSONObject data(String key) throws JSONException{
    JSONObject data = new JSONObject();
        if (key.equals("testEvent")){
            data.put("product","A");
            data.put("price",223.9);
        }
        return data;
    }
```

###待完善##
·支持多种数据结构（目前是JSONObject）方式采集参数。添加初始化适配器（调研可以行）
·click埋点运行时方案（调研）
·原有埋点基础数据兼容（可行）





