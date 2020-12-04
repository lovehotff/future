### poi导出问题记录

* Windows因为doc的历史原因，不区分大小写，linux和unix因为文件系统不同，区分大小写
  * ClassLoader.getSystemResourceAsStream(url)报错的时候，首先会看在系统存不存在，再去项目内查看是不是相对路径，会拼一层，导致报错的时候会嵌套一层
    * **getSystemResourceAsStream()方法**在java.lang包中可用
    * **getSystemResourceAsStream()方法**用于获取资源作为参数并将资源转换为InputStream，换句话说，可以说此方法用于表示InputStream来扫描给定资源。
    * **getSystemResourceAsStream()方法**是一个静态方法，可以使用类名进行访问，如果尝试使用类对象访问该方法，则不会出现任何错误。
    * 从资源到InputStream对象的转换时， **getSystemResourceAsStream()方法**不会引发异常。