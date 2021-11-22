1. 在com.hm.tax.personal.common.constant.TaxFileCodeEnum添加接口平台和SAAS平台的枚举映射关系，个人税的code取表名的大写。如

```java
   TAXFILECODE_N01("IIT_A_1", "IIT_MONTH_INCOME", "个税-本期收入"),
   TAXFILECODE_N02("IIT_A_2", "IIT_ABROAD_DISPATCH", "个税-海外派遣");
```

2. 新建需要导入的excel模板的包名和类名用表名，类似IIT_ABROAD_DISPATCH，包名为：iitabroaddispatch，类名为IitAbroadDispatch，包层级参照

```
com.hm.tax.personal.fileupload.handler.reporthandler.iitabroaddispatch.excel.IitAbroadDispatch
```

3. 具体的处理类参照com.hm.tax.personal.fileupload.handler.reporthandler.iitabroaddispatch.excel.IitAbroadDispatch

```
package com.hm.tax.personal.fileupload.handler.reporthandler.iitabroaddispatch.excel;

import com.hm.tax.personal.fileupload.entity.excel.ExcelParsingFeignEntity;
import com.hm.tax.personal.fileupload.handler.reporthandler.ReportHandler;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * @Author: caokang
 * @Date: Created in 13:41 2021/7/14
 * @annotation:海外派遣Excel接口
 * @version:1.0
 * @TableName:iit_abroad_dispatch
 */
public class IitAbroadDispatch extends ReportHandler {

    private static final Logger logger = LoggerFactory.getLogger(IitAbroadDispatch.class);

    @Override
    public Object handle(ExcelParsingFeignEntity excelParsingFeignEntity) {
        //对数据开始进行解析操作
        logger.info(excelParsingFeignEntity.toString());
        //执行入库操作
        return null;
    }
}

```

