# 使用Hutool工具类自定义多sheet情况下去除默认的空sheet1

## 问题描述

最近项目需求遇到下载表格多sheet的情况，使用hutool工具类，发现导出总是会带一个默认的空sheet1，看官方文档说这样解决

```java
//初始化时定义表名
ExcelWriter writer = new ExcelWriter("d:/aaa.xls", "表1");
//切换sheet，此时从第0行开始写
writer.setSheet("表2");
...
writer.setSheet("表3");
...
```

但貌似不太符合我下载表格的需求，所以我自己用了一种方法，封装了一个工具类，也算是解决了，特此贴出来分享给大家

```java
/**
 *
 * @param response
 * @param fileName 导出文件名
 * @param listMap  导出文件内容，key为sheet名，value为导出内容
 * @param map 自定义标题
 * @param <K>
 * @param <V>
 */
public static <K,V> void exportExcel(HttpServletResponse response, String fileName, Map<K, List<V>> listMap, Map<String, String> map) {
	ExcelWriter writer = cn.hutool.poi.excel.ExcelUtil.getWriter(true);
	Set<Map.Entry<String, String>> entrySet = map.entrySet();
	for (Map.Entry<String, String> entry : entrySet) {
		writer.addHeaderAlias(entry.getKey(), entry.getValue());
	}
	writer.setOnlyAlias(true);
	boolean flag = true;
	for (Map.Entry<K, List<V>> entry : listMap.entrySet()) {
		if (flag) {
			writer.renameSheet(entry.getKey().toString());
			flag = false;
		} else {
			writer.setSheet(entry.getKey().toString());
		}
		writer.write(entry.getValue(), true);
		setSizeColumn(writer.getSheet(), map.size() - 1);
	}
	response.setContentType("application/vnd.ms-excel");
	response.setCharacterEncoding("utf-8");
	ServletOutputStream out = null;
	try {
		fileName = URLEncoder.encode(fileName, "UTF-8");
		response.setHeader("Content-disposition", "attachment;filename=" + fileName + ".xlsx");
		out = response.getOutputStream();
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		writer.flush(out, true);
		writer.close();
		IoUtil.close(out);
	}
}
```

ExcelWriter有一个修改sheet名的方法，public ExcelWriter renameSheet(String sheetName)，第一次写数据将sheet名字修改掉，后续再进行创建sheet

![image-20210102121405180](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210102121405180.png)