[TOC]

#### 一、关键技术点

1. 阿里开源EasyExcel
2. 策略模式
3. ThreadFactory线程池



#### 二、层级结构说明

![image-20200804112330510](/Users/kevin/Documents/00-kevin/Github/kevin/knowledge-md/00-images/image-20200804112330510.png)



##### 1、alertcommon 包

存放公共的导出实体对象。

##### 2、alertconfig

存放全局配置。

##### 3、bean

###### 3.1、alertcenter

alert center 导出对象存放处。

###### 3.2、task

task导出对象存放处。

#### 4、handler

自定义数据处理器存放处。

###### 4.1、cellwrite

自定义样式处理器

###### 4.2、sheetwrite

自定义单元格处理器

##### 5、strategy

业务数据策略处理类存放处

###### 5.1.impl.alertcenter

alertcenter业务数据处理类

###### 5.1.impl.task

task业务数据处理类



#### 三、主入口

###### 3.1、GetExportAlertCenterService

Alert Center导出入口类

###### 3.2、GetExportTaskService

task导出入口类



#### 四、业务开发关注点

1、bean包下新建导出对象

```
com.bayconnect.superlop.service.fileexport.bean
```

2、strategy包下新建业务数据处理类

```
com.bayconnect.superlop.service.fileexport.strategy.impl
```

2.1、关键实现方法

```
/**
   * alertItemList数据处理服务.alertType不同数据结构不一样.
   *
   * @param alertItemList 待处理数据.
   * @param rs 结果集.
   * @return List
   */
  List<? extends DefaultBaseBean> alertItemListAfter(List<Map<String, Object>> alertItemList, String alertType,
      Map<String, ? extends DefaultOutput> pageGroupingMap, BaseConfigService configService, IResult rs);
 
/**
   * 初始化.
   */
void afterPropertiesSet() throws Exception;
```



#### 五、其它

1、导出字段忽略

```
//1.指定忽略字段
Set<String> ignoreExcludeColumnFiledNames = new HashSet<>();
ignoreExcludeColumnFiledNames.add("taskCode");
ignoreExcludeColumnFiledNames.add("alertType");
    
//2.设置忽略字段
StrategyServiceContext.setIgnoreExcludeColumnFiledNamesMap(alertType,ignoreExcludeColumnFiledNames,configService);
```

示例

```
package com.bayconnect.superlop.service.fileexport.strategy.impl.alertcenter;

import com.alibaba.fastjson.JSONObject;
import com.bayconnect.superlop.common.util.StringUtil;
import com.bayconnect.superlop.service.fileexport.alertconfig.BaseConfigService;
import com.bayconnect.superlop.service.fileexport.apireqparambean.DefaultOutput;
import com.bayconnect.superlop.service.fileexport.apireqparambean.GetAlertCenterPageListOutput;
import com.bayconnect.superlop.service.fileexport.bean.DefaultBaseBean;
import com.bayconnect.superlop.service.fileexport.bean.alertcenter.CenterBean321;
import com.bayconnect.superlop.service.fileexport.strategy.StrategyService;
import com.bayconnect.superlop.service.fileexport.strategy.StrategyServiceContext;
import com.google.common.base.CaseFormat;
import com.google.common.collect.ImmutableMap;
import com.google.common.collect.Lists;
import com.google.common.collect.Maps;
import com.websuites.core.response.IResult;
import java.util.List;
import java.util.Map;
import java.util.Map.Entry;
import java.util.Optional;
import java.util.Set;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

/**
 * @author 刘瑞奎(kevin).
 * @Title superlop-parent.
 * @Package com.bayconnect.superlop.service.fileexport.strategy.impl.
 * @Description 321.
 * @date 2020/7/2.
 */
@SuppressWarnings("unchecked")
@Service
public class CenterStrategyImpl321 implements StrategyService {

  /**
   * 配置服务.
   */
  private final BaseConfigService configService;

  @Autowired
  public CenterStrategyImpl321(@Qualifier("alertCenterConfigService") BaseConfigService configService) {
    this.configService = configService;
  }

  private final static Map<String, String> YES_NO = ImmutableMap.of("1", "YES", "0", "NO");

  /** 转义字段 1 YES 0 NO. **/
  private final static String PREV_IS_REPORT_ANY_POSITION = "PREV_IS_REPORT_ANY_POSITION";
  private final static String CONFIRM_IS_WATCH = "CONFIRM_IS_WATCH";


  /**
   * alertItemList数据处理服务.alertType不同数据结构不一样.
   *
   * @param alertItemList 待处理数据.
   * @param rs 结果集.
   * @return List<Map < String, Object>>
   */
  @Override
  public List<? extends DefaultBaseBean> alertItemListAfter(List<Map<String, Object>> alertItemList, String alertType,
      Map<String, ? extends DefaultOutput> pageGroupingMap, BaseConfigService configService, IResult rs) {
    List<CenterBean321> resultList = Lists.newArrayList();
    if (Optional.ofNullable(alertItemList).isEmpty() || alertItemList.isEmpty()) {
      return resultList;
    }
    //1.数据转驼峰形式,并转义字段.
    alertItemList.forEach(alterMap -> {
      String alterId = String.valueOf(Optional.ofNullable(alterMap.get("ALERT_ID")).orElse(""));
      if (Optional.ofNullable(pageGroupingMap).isPresent() && StringUtil.isNotBlank(alterId)
          && pageGroupingMap
          .containsKey(alterId)) {
        GetAlertCenterPageListOutput pageListAlertCenterOutput = (GetAlertCenterPageListOutput) pageGroupingMap
            .get(alterId);
        Map<String, ?> outMap = JSONObject.parseObject(JSONObject.toJSONString(pageListAlertCenterOutput), Map.class);
        alterMap.putAll(outMap);
      }
      Set<Entry<String, Object>> entrySet = alterMap.entrySet();
      Map<String, Object> resultMap = Maps.newHashMap();
      entrySet.forEach(entry -> {
        if (PREV_IS_REPORT_ANY_POSITION.equals(entry.getKey()) || CONFIRM_IS_WATCH.equals(entry.getKey())) {
          resultMap
              .put(CaseFormat.LOWER_UNDERSCORE.to(CaseFormat.LOWER_CAMEL, entry.getKey().toLowerCase()),
                  StringUtil.isBlank(YES_NO.get(String.valueOf(entry.getValue()))) ? YES_NO.get("0")
                      : YES_NO.get(String.valueOf(entry.getValue())));
        } else {
          resultMap
              .put(CaseFormat.LOWER_UNDERSCORE.to(CaseFormat.LOWER_CAMEL, entry.getKey().toLowerCase()),
                  entry.getValue());
        }
      });

      CenterBean321 centerNewAccountBean321 = JSONObject
          .parseObject(JSONObject.toJSONString(resultMap), CenterBean321.class);
      String alertStatusDesc =
          Optional.ofNullable(centerNewAccountBean321.getAlertStatusDesc()).orElse("").contains(",") ? "Multiple"
              : centerNewAccountBean321.getAlertStatusDesc();
      centerNewAccountBean321.setAlertStatusDesc(alertStatusDesc);
      resultList.add(centerNewAccountBean321);
    });



    return resultList;
  }

  @Override
  public void afterPropertiesSet() throws Exception {
    //321-New Account
    StrategyServiceContext.setAlertTypeBeanMap("321", CenterBean321.class, configService);
    StrategyServiceContext.setAlertTypeFileNameMap("321", "New Account_2101.xlsx", configService);
    StrategyServiceContext.setStrategyAlertServiceMap("321", "centerStrategyImpl321", configService);

    //322-New Account
    StrategyServiceContext.setAlertTypeBeanMap("322", CenterBean321.class, configService);
    StrategyServiceContext.setAlertTypeFileNameMap("322", "New Account_2102.xlsx", configService);
    StrategyServiceContext.setStrategyAlertServiceMap("322", "centerStrategyImpl321", configService);

    //323-New Account
    StrategyServiceContext.setAlertTypeBeanMap("323", CenterBean321.class, configService);
    StrategyServiceContext.setAlertTypeFileNameMap("323", "New Account_2103.xlsx", configService);
    StrategyServiceContext.setStrategyAlertServiceMap("323", "centerStrategyImpl321", configService);

    //324-New Account
    StrategyServiceContext.setAlertTypeBeanMap("324", CenterBean321.class, configService);
    StrategyServiceContext.setAlertTypeFileNameMap("324", "New Account_2105.xlsx", configService);
    StrategyServiceContext.setStrategyAlertServiceMap("324", "centerStrategyImpl321", configService);
  }
}
```

2、英文日期格式转换

```
示例：yyyyMMdd ===> 15-Mar-2019
@ExcelProperty(value = "Trade Date",converter= CustomStringDateConverter.class)
@DateTimeFormat(DateUtil.DATE_FMT_DATE)
private String tradeDate;

示例：yyyy-MM-dd HH:mm:ss ===> 04-Aug-2020 09:30:19
@ExcelProperty(value = "Alert Timestamp",converter= CustomStringDateConverter.class)
@DateTimeFormat(DateUtil.DATE_FMT_DATETIME_1)
private String alertTime;

示例：yyyyMMdd(yyyyMMdd) ===> 15-Mar-2019(15-Mar-2019)
@ExcelProperty(value = "Trade Date",converter= CustomStringDateConverter.class)
@DateTimeFormat(DateUtil.DATE_FMT_DATE)
private String tradeDate;
```

3、数字格式化

```
示例：1000 ===> 1,000
@NumberFormat(pattern="#,###")
private Integer salary;
```