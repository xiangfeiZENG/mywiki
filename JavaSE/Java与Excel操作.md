# Java与Excel操作

常用的Excel电子表格处理库是：**Apache POI和JExcel API**

这两个库都可用于动态读取，写入和修改Excel电子表格的内容，并提供将Microsoft Excel集成到Java应用程序中的有效方法。

- 在**Apache的POI库支持\*的.xls\*和\*的.xlsx\*文件**，并且比其他Java库与Excel文件的工作更复杂的库。
- JExcel库是一个轻量级的库，其优点是比Apache POI易于使用，但缺点是它仅支持处理*.xls*（1997-2003）格式的Excel文件。

基本使用方法参考：https://www.baeldung.com/java-microsoft-excel



但是Apache POI在高并发量下存在占用内存过大的问题。EasyExcel是一个基于Java的简单、省内存的读写Excel的开源项目。在尽可能节约内存的情况下支持读写百M的Excel。

reference地址：https://www.yuque.com/easyexcel/doc



