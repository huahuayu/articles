[//title]:(java-classPathResource-FileNotFoundException解决办法)
[//englishTitle]:(java-classPathResource-FileNotFoundException)
[//category]:(java,problem-solved)
[//tags]:(java,classPathResource,FileNotFoundException)
## 概述
今天通过java程序读取resource文件夹里面的一些文件，文件明明放到了指定的文件夹，通过`classPathResource`来读取，但是程序报文件不存在。  

读文件的代码：  
``` java
ClassPathResource classPathResource = new ClassPathResource("foo.txt");
InputStream input = classPathResource.getInputStream();
```

错误信息：  
``` text
java.io.FileNotFoundException: class path resource [foo.txt] cannot be opened because it does not exist
```

## 问题原因
文件只在源代码中，不在target文件夹中，而`classPathResource.getInputStream()`是读编译后的文件，不是源代码文件:   
![](https://cdn.liushiming.cn/img/20200226161523.png)

## 解决方案
rebuild project（也可以先把target删了再rebuild），确保target中有相关的文件  