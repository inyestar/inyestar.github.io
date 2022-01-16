---
layout: post
title: Java Apache POI
tags: [Java]
---

### Disclaimer
I am a junior java engineer so my posts are very experimental. If you find any incorrect and improper information on them please feel free to contact me and let me know! It would be very glad :)

### Introduction
Have you heard about [Download as Excel] in your admin page? I'm pretty sure you do because it has been very important and necessary feature and you never doubt alomost everyone in your office do their work with Excel. It is not complicated if you just want to transfer a smal size data into Excel in Java with Apache POI support. But what if the size of data is getting bigger and bigger and finally has become over 500mb? You don't know well? I don't know too! So let's figure this out together.

### Dependencies
- Spring Boot Starter Data JDBC
  - Since data is stored in DB mostly.
- Apache POI
  - They have great support on Microsoft OLE2 Compound Document Format.
- h2
  - It is huge work to install and configure RDBMS and additionally I don't want to start my virtual machine.
- Spring Boot Start Test
  - To make my test simple!

### Cases
#### Under 100mb
```java
try(Workbook wb = new SXSSFWorkbook();
    OutputStream output = new FileOutputStream(file)) {
    ...
    workbook.write(output);
}
```
- It was easy to make an excel file by SXSSFWorkbook Object.
  - HSSFWorkbook (Excel 97 ~ 2003)
  - XSSFWorkbook (over EXcel 2007)
  - SXSSWorkbook (Most Recent)
- seems that Excel in my computer doesn't want to open over 100 mb ^^;


### References
- [https://www.baeldung.com/java-read-lines-large-file](https://www.baeldung.com/java-read-lines-large-file)
- [https://github.com/HomoEfficio/dev-tips/blob/master/Direct%20Memory%20Access%EB%A5%BC%20%ED%99%9C%EC%9A%A9%ED%95%9C%20%EB%8C%80%EC%9A%A9%EB%9F%89%20%ED%8C%8C%EC%9D%BC%20%ED%96%89%20%EB%8B%A8%EC%9C%84%EB%A1%9C%20%EC%AA%BC%EA%B0%9C%EA%B8%B0.md](https://github.com/HomoEfficio/dev-tips/blob/master/Direct%20Memory%20Access%EB%A5%BC%20%ED%99%9C%EC%9A%A9%ED%95%9C%20%EB%8C%80%EC%9A%A9%EB%9F%89%20%ED%8C%8C%EC%9D%BC%20%ED%96%89%20%EB%8B%A8%EC%9C%84%EB%A1%9C%20%EC%AA%BC%EA%B0%9C%EA%B8%B0.md)
