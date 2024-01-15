# 导入

* 加载依赖

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>easyexcel</artifactId>
    <version>2.2.4</version>
</dependency>
```

* 创建监听器文件

```java
import com.alibaba.excel.context.AnalysisContext;
import com.alibaba.excel.event.AnalysisEventListener;
import com.alibaba.fastjson.JSON;
import lombok.Data;
import lombok.EqualsAndHashCode;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

@EqualsAndHashCode(callSuper = true)
@Data
public class ExcelListener<T> extends AnalysisEventListener<T> {
    private static final Logger LOGGER = LoggerFactory.getLogger(ExcelListener.class);
    /**
     * 自定义用于暂时存储data
     */
    private List<T> dataList = new ArrayList<>();

    /**
     * 导入表头
     */
    private Map<String, Integer> importHeads = new HashMap<>(16);

    /**
     * 这个每一条数据解析都会来调用
     */
    @Override
    public void invoke(T data, AnalysisContext context) {
        String headStr = JSON.toJSONString(data);
        System.out.println(headStr);
        dataList.add(data);
    }

    /**
     * 这里会一行行的返回头
     */
    @Override
    public void invokeHeadMap(Map<Integer, String> headMap, AnalysisContext context) {
        for (Integer key : headMap.keySet()) {
            if (importHeads.containsKey(headMap.get(key))) {
                continue;
            }
            importHeads.put(headMap.get(key), key);
        }
    }

    /**
     * 所有数据解析完成了 都会来调用
     */
    @Override
    public void doAfterAllAnalysed(AnalysisContext context) {
        LOGGER.info("Excel解析完毕");
    }
}
```

* 导入代码

```java
@PostMapping("/importExcel")
@ApiOperation("导入")
public Object importExcel(@RequestParam("file")MultipartFile file) {
    ExcelListener listener = new ExcelListener();
    ExcelReader excelReader = null;
    try {
        excelReader = EasyExcel.read(file.getInputStream(), ChannelArchives.class, listener).build();
    } catch (IOException e) {
        e.printStackTrace();
    }
    ReadSheet readSheet = EasyExcel.readSheet(0).build();
    excelReader.read(readSheet);
    //获取导入表头
    Map importHeads = listener.getImportHeads();
    //总数据
    List<ChannelArchives> dataList = listener.getDataList();
    return "导入成功";
}
```

# 导出

* 创建导出模板文件

```java
import com.alibaba.excel.annotation.ExcelProperty;
import lombok.Data;

@Data
public class DownloadTemplate {
    @ExcelProperty(value = "通道名称",index = 0)
    private String channelName;
    @ExcelProperty(value = "KPI英文标识",index = 1)
    private String kpiEnName;
    @ExcelProperty(value = "KPI值",index = 2)
    private String kpiValue;
    @ExcelProperty(value = "计算时间",index = 3)
    private String calculateTime;
    @ExcelProperty(value = "开始时间",index = 4)
    private String startTime;
    @ExcelProperty(value = "结束时间",index = 5)
    private String endTime;
}
```

* 导出代码

```java
@GetMapping("month")
public Object month(HttpServletResponse response, @RequestParam Map map) {
    List<DownloadTemplate> result = new ArrayList<>();
    modelOutput(response, "月统计表", DownloadTemplate.class,result);
    return "月统计表下载成功";
}
```

```java
public void modelOutput(HttpServletResponse response, String modelName, Class modelClass, List<DownloadTemplate> list) {
    //指定outputStream和 excel映射实体
    try {
        response.setContentType("application/octet-stream; charset=utf-8");
        response.setHeader("Content-Disposition", URLEncoder.encode(modelName+".xlsx", "UTF8"));
        EasyExcel.write(response.getOutputStream(), modelClass)
            //注册 WriteHandler ，getDefaultWriteHandler() 见下
            .registerWriteHandler(setMyCellStyle())
            //注入原生 或 自定义转换器
            .registerConverter(new DateStringConverter())
            .registerConverter(new BigDecimalStringConverter())
            .sheet(modelName)
            .doWrite(list);
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

```java
// 创建我的cell  策略
private static HorizontalCellStyleStrategy setMyCellStyle() {

    // 头的策略
    WriteCellStyle headWriteCellStyle = new WriteCellStyle();
    // 设置表头居中对齐
    headWriteCellStyle.setHorizontalAlignment(HorizontalAlignment.CENTER);
    // 颜色
    headWriteCellStyle.setFillForegroundColor(IndexedColors.GREY_25_PERCENT.getIndex());
    WriteFont headWriteFont = new WriteFont();
    headWriteFont.setFontHeightInPoints((short) 10);
    // 字体
    headWriteCellStyle.setWriteFont(headWriteFont);
    headWriteCellStyle.setWrapped(true);

    // 内容的策略
    WriteCellStyle contentWriteCellStyle = new WriteCellStyle();
    // 设置内容靠中对齐
    contentWriteCellStyle.setHorizontalAlignment(HorizontalAlignment.CENTER);
    // 这个策略是 头是头的样式 内容是内容的样式 其他的策略可以自己实现
    HorizontalCellStyleStrategy horizontalCellStyleStrategy = new HorizontalCellStyleStrategy(headWriteCellStyle, contentWriteCellStyle);
    // 这里 需要指定写用哪个class去写，然后写到第一个sheet，名字为模板 然后文件流会自动关闭

    return horizontalCellStyleStrategy;
}
```

