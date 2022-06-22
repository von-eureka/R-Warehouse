# R语言每日学习（Daily Study of R）
初步更新：R语言50题（50题原网址：github.com/renkun-ken/r-data-practice）( 50 R data exercises)  
之前已经有人完成，并公布了答案以及解法。（如github.com/Ravin515/r-data-practice），我尽量在使用基础包的情况下完成50题，希望以此来学习R.  
(Although many people have done 50 exercises and provide the key,I will try my best to finish this practise with the basic package by myself in order to learn R.)
Day1 哪些股票的代码中包含 8 这个数字？  
exercise1<-subset(`stock-market-data`,grepl("^.*8.*$",symbol))  ##利用grepl函数和正则表达式筛选出所有含有8的“symbol” 
exercise1<-unique(exercise1$symbol)  ##利用unique函数去掉筛选结果中重复的值  
