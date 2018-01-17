# Learning Gradle
----------------------------
## Gradle 基本概念：
* 概念：
  * Gradle is a DSL(Domain Specific Language):
    * Project: 為 Task 提供執行的 context
    * Task: 
      * 一個 Task 表示一個邏輯上較為獨立的執行過程，比如編譯 Java、拷貝檔案、打包 Jar、執行一個系統指令或是調用 Ant。
      * 一個 Task 可以讀取和設置 Project 的 Property 以完成特定的操作。
    * Propertiy: Project 的屬性設定
    * Plugin: 提供特定功能的外掛，該功能要麼是向 Project 中添加不同的 Task，要麼向 Project中添加用於配置的 Property。
  * 預設的專案設定檔：build.gradle
    * 執行方式： 在該檔目錄下使用 gradle <指令>
* 基本指令：
  * gradle
  * gradle tasks: 列出所有 Tasks
  * gradle properties: 列出所有 Property
* doLast
```groovy
task hello << { // Deprecated in Gradle 5.0, just use doLast{} to replace it.
   println "Hello Gradle!"
}
------------------------
task hello {
  ...
  doLast {
    println "Hello Gradle!"
  }
}
```
  * 「<<」表示向 helloWorld 中加入執行程式碼——其實就是 groovy。
  * 執行 Task：gradle hello
* doLast vs doFirst
```groovy
task hello {
 println "hello: Hello Gradle!"
 doLast{
	println "hello.doLast: Hello Gradle!"
 }
 doFirst{
	println "hello.doFirst: Hello Gradle!"
 }
}
---------------------
>gradle hello

hello: Hello Gradle!

> Task :hello
hello.doFirst: Hello Gradle!
hello.doLast: Hello Gradle!
```
* copy
```groovy
import java.time.LocalDateTime
import java.time.format.DateTimeFormatter

// Backup root/*.md and root/build.gradle to backup/
// Append now datetime pattern to each backuped file
task backupFiles(type:Copy) {
  from '.'
  into 'backup'
  include '*.md','build.gradle'
  String now = LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyyMMdd-HHmmss"))
  rename { String fileName -> fileName.concat('.').concat(now).concat('.').concat('bk') }  
}
```
* ​
----------------------------
## References:
1. [Gradle学习系列之一——Gradle快速入门](https://www.cnblogs.com/davenkin/p/gradle-learning-1.html)