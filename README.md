# R语言每日学习（Daily Study of R）
初步更新：R语言50题（50题原网址：github.com/renkun-ken/r-data-practice）( 50 R data exercises)  
之前已经有人完成，并公布了答案以及解法。（如github.com/Ravin515/r-data-practice），我尽量在使用基础包的情况下完成50题，希望以此来学习R.  
(Although many people have done 50 exercises and provide the key,I will try my best to finish this practise with the basic package by myself in order to learn R.)  
Day1 1、哪些股票的代码中包含 8 这个数字？   
exercise1<-subset(`stock-market-data`,grepl("^.*8.*$",symbol))  ##利用grepl函数和正则表达式筛选出所有含有8的“symbol”   
exercise1<-unique(exercise1$symbol)  ##利用unique函数去掉筛选结果中重复的值  
subset；从某一数据框选出某符合条件的数据或者相关列，本例具体用法为subset(数据框，条件，显示指定列#不指定则全部列输出，-用来排除指定列)  
grepl：相近的还有grep函数，grep函数返回找到的位置，grepl函数返回要查找的内容。本例具体用法为grepl(条件，在何对象中查找)  
unique:去重复值，本例具体用法为unique(需要去重复值的对象)  
  
Day2 2、每天上涨和下跌的股票各有多少？  
exercise2<-`stock-market-data`  ##备份原数据  
exercise2$sum=ifelse(`stock-market-data`$open<`stock-market-data`$close,"UP","DOWN")  ##利用ifelse函数判断股票涨跌，如果涨了，即开盘价小于收盘价，那么就为“up”,反之则为“DOWN”；数据存储在exercise2数据框新增的sum列。  
exercises2<-aggregate(x=exercise2$sum,by=list(exercise2$date,exercise2$sum),FUN=length)  ##注意，是exercises2，与之前的不同。利用aggregate函数进行分类汇总，这里限制条件有两个，一是要按照日期分类，二是要把上涨的和下跌的分开，所以by后面的限制条件是两个；FUN=length则是计数作用，分组情况下实际是求行数，length即为此作用（存疑）。
涉及到分组，R自身的分组函数在data.table和dplyr面前败下阵来。尤其是dplyr包的group_by函数，与SQL相似，颇为方便。  
当然，原题设给出了当日股票收益率的计算方式，为此我还特意询问了金融专业的同学，最后选用了本文的这种计算涨跌的方式，故答案会有所不同。  
ifelse：条件选择函数，本例具体用法为ifelse(判断条件，满足执行，不满足执行)；if与此相似，具体用法为if(判断条件，满足执行)   
aggregate:分类汇总函数，本例具体用法为aggregate(分类数据，by=分类条件，FUN=执行条件)注意的是，分类条件类型必须为list，但分类条件可以和分类数据相同，本例即为此：不仅要以日期为基准对股票涨跌情况进行分类，还要按照股票涨跌情况进行分类。即：20120104涨多少只，20120104跌多少只。此外在运行过程中还曾出现过“参数的长度必须一致”报错，原因不明，怀疑是一开始存数据的列表list1只有1个向量的缘故，后来改为直接list()函数后未出现此问题；执行条件不能为空，已知的执行条件有mean sum length
  
Day3 3、每天每个交易所上涨、下跌的股票各有多少？4、沪深300成分股中，每天上涨、下跌的股票各有多少？5、每天每个行业各有多少只股票？6、股票数最大的行业和总成交额最大的行业是否总是同一个行业？   
3、每天每个交易所上涨、下跌的股票各有多少？  
exercise3<-exercise2  ##备份数据,因为exercise2中我们已经新增了sum一列反映股票涨跌情况，所以这里选用exercise2  
exercise3$SE<-substr(exercise3$symbol,8,9)  ##利用substr函数对字符串进行选取，选取exercise$symbol一栏中字符串第8-9位，存放在新列exercise3$SE中  
exercises3<-aggregate(x=exercise2$sum,by=list(exercise3$date,exercise3$sum,exercise3$3SE),FUN=length)  ##根据要求分类汇总  
4、沪深300成分股中，每天上涨、下跌的股票各有多少？  
exercise4<-exercise2  ##备份exercise2数据  
exercises4<-subset(exercise4,exercise4$index_w300>0)  ##选取所有沪深300成分股（即index_w300>0）  
exercises4<-aggregate(exercises4$sum,by=list(exercises4$date,exercises4$sum),FUN=length)  ##同Day2  
5、每天每个行业各有多少只股票？  
exercises5<-aggregate(x=exercise2$industry,by=list(exercise2$date,exercise2$industry，),FUN=length)  ##同Day2,换一下分类条件就行了，我这里数据直接用了exercise2的。  
6、股票数最大的行业和总成交额最大的行业是否总是同一个行业？   
exercises6<-aggregate(x=exercise2$amount,by=list(exercise2$date,exercise2$industry),FUN=sum)  ##这里把成交额都加起来，得各个行业的总成交额。  
实际上，如果要满足题目要求的话 只需要排序exercises6和exercises5即可，而如果想要更直观的看到，恐怕需要借用apply函数。  
rm:删除函数，具体用法为rm(对象名)  
substr：字符串选取函数，具体用法为substr(选取对象，选取开始位数，选取结束位数)    
  
Day4 7、每天涨幅超过5%、跌幅超过5%的股票各有多少？  
exercise7<-`stock-market-data`  ##备份原数据    
attach(exercise7)  ##建立数据表内数据索引，因为之后要用到多次  
exercise7$ret<-(close-open)/open  ##我们在exercise7里建立一个新列来存放计算的股票涨跌率  
exercises7<-subset(exercise7,ret>0.05|ret< -0.05)  ##注意这里负数符号和“<”之间一定要加空格，否则R语言会把它默认为赋值符号，出现“not found”的报错。这里利用subset函数筛选出满足题目要求的。subset函数中“&”是“且”的意思，而“|”是“或”  
exercises7$updown<-ifelse(exercises7$ret>0.05,"up","Down")  ##给这些股票创建一列数据存储  
exercises7<-aggregate(exercises7$updown,by=list(exercises7$date,exercises7$updown),FUN=length)  ## 利用aggregate函数进行分类即可。
