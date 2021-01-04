# ORACLE分析函数

## 分析函数语法

```sql
function_name(<argument>,<argument>...) over(<partition_Clause><order by_Clause><windowing_Clause>);
```

function_name()：函数名称

argument：参数

over( )：开窗函数

partition_Clause：分区子句，数据记录集分组，group by...

order by_Clause：排序子句，数据记录集排序，order by...

windowing_Clause：开窗子句，定义分析函数在操作行的集合，三种开窗方式：rows、range、Specifying

## 分析函数汇总

* count() over()：统计分区中各组的行数，partition by 可选，order by 可选

  ```sql
  SELECT distinct a.managecom,count(*) over(partition by a.managecom order by a.managecom) FROM tmagentinfotozbx a;
  ```

  ![image-20201231171346672](C:\Users\kgcaox\AppData\Roaming\Typora\typora-user-images\image-20201231171346672.png)

* sum() over()  ：统计分区中记录的总和，partition by 可选，order by 可选

  ```sql
  SELECT a.wageno,a.agentcode,sum(a.payment) over(partition by a.agentcode,a.wageno) FROM tmwagetotal a ;
  ```

  ![image-20201231172346574](C:\Users\kgcaox\AppData\Roaming\Typora\typora-user-images\image-20201231172346574.png)

* avg() over()  ：统计分区中记录的平均值，partition by 可选，order by 可选

  ```sql
  SELECT a.wageno,a.agentcode,avg(a.payment) over(partition by a.agentcode) FROM tmwagetotal a ;
  ```

  

* min() over() ：统计分区中记录的最小值，partition by 可选，order by 可选

  ```sql
  SELECT a.wageno,a.agentcode,min(a.payment) over(partition by a.agentcode) FROM tmwagetotal a ;
  ```

* max() over() ：统计分区中记录的最大值，partition by 可选，order by 可选

  ```sql
  SELECT a.wageno,a.agentcode,max(a.payment) over(partition by a.agentcode) FROM tmwagetotal a ;
  ```

* rank() over()  ：跳跃排序，partition by 可选，order by 必选(跳跃排序,有并列名次的情况下,整个排序序号不连续)

  ```sql
  SELECT a.wageno,a.agentcode,rank() over(partition by a.agentcode order by a.payment) FROM tmwagetotal a ;
  ```

* dense_rank() ：连续排序，partition by 可选，order by 必选(有并列名次的情况下,整个排序序号是连续)

  ```sql
  SELECT a.wageno,a.agentcode,dense_rank() over(partition by a.agentcode order by a.payment) FROM tmwagetotal a ;
  ```

* row_number() over() ：排序，无重复值，partition by 可选，order by 必选

  ```sql
  SELECT a.wageno,a.agentcode,row_number() over(partition by a.agentcode order by a.payment) FROM tmwagetotal a ;
  ```

* ntile(n) over() ：partition by 可选，order by 必选,n表示将分区内记录平均分成n份，多出的按照顺序依次分给前面的组

* first_value() over() ：取出分区中第一条记录的字段值，partition by 可选，order by 可选

* last_value() over() ：取出分区中最后一条记录的字段值，partition by 可选，order by 可选

* first ：从DENSE_RANK返回的集合中取出排在最前面的一个值的行

* last ：从DENSE_RANK返回的集合中取出排在最后面的一个值的行

* lag() over() ：取出前n行数据，partition by 可选，order by 必选

* lead() over() ：取出后n行数据，partition by 可选，order by 必选

* ratio_to_report(a) over(partition by b) ：求按照b分组后a的值在所属分组中总值的占比,a的值必须为数值或数值型字段partition by 可选，order by 不可选

* percent_rank() over()  ：partition by 可选，order by 必选所在组排名序号-1除以该组所有的行数-1，排名跳跃排序

* cume_dist() over() ：partition by 可选，order by必选所在组排名序号除以该组所有的行数，注意对于重复行，计算时取重复行中的最后一行的位置

* precentile_cont( x ) within group(order by ...) over()   ：over()中partition by可选，order by 不可选x为输入的百分比，是0-1之间的一个小数，返回该百分比位置的数据，若没有则返回以下计算值（r）：

  a=1+( x *(N-1) )  x为输入的百分比，N为分区内的记录的行数

  b=ceil ( a )  向上取整

  c = floor( a ) 向下取整

  r=a * 百分比位置上一条数据 + b * 百分比位置下一条数据

* precentile_disc( x ) within group(order by ...) over()  ：over()中partition by可选，order by 不可选

  x为输入的百分比，是0-1之间的一个小数，返回百分比位置对应位置上的数据值，若没有对应数据值，就取大于该分布值的下一个值

* stddev() over()：计算样本标准差，只有一行数据时返回0，partition by 可选，order by 可选

     stddev_samp() over()：计算样本标准差，只有一行数据时返回null，partition by 可选，order by 可选

     stddev_pop() over()：计算总体标准差，partition by 可选，order by 可选

* variance() over()：计算样本方差，只有一行数据时返回0，partition by 可选，order by 可选

  ​    var_samp() over()：计算样本方差，只有一行数据时返回null，partition by 可选，order by 可选

  ​    var_pop() over()：计算总体方差，partition by 可选，order by 可选

* covar_samp over()：返回一对表达式的样本协方差，partition by 可选，order by 可选

  ​    covar_pop over()： 返回一堆表达式的总体协方差，partition by 可选，order by 可选

* corr() over() ：返回一对表达式的相关系数，partition by 可选，order by 可选

* REGR_ (Linear Regression) Functions：这些线性回归函数适合最小二乘法回归线，有9个不同的回归函数可使用

* ```sql
  SELECT a.wageno,a.agentcode,rank() over(partition by a.agentcode order by a.payment) FROM tmwagetotal a ;
  ```

  

