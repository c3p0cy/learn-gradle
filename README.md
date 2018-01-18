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
  * Gradle 並不會按 build.gradle 的內容順序執行，而是分為兩個階段：
    1. 配置階段：讀取 build.gradle 的所有內容來設定 Project 和 Task 等，如設置 Project 的 Property，處理 Task 之間的依賴關係等。
    2. 執行階段
    * 可以從 [gradle build](#gradle_build) 和 [dependsOn](#dependsOn) 中看到執行結果有 Configure project 和 Task: XXXX

* 基本指令：
  * gradle
  * gradle tasks --all: 列出所有 Tasks
  * gradle properties: 列出所有 Property
  * gradle [taskA]: 執行 taskA
    * gradle [taskA] -x [taskB]: 執行 taskA 但排除 taskB
    * 執行 [task] 只要輸入名稱到可識別的長度即可，例如只有 task_copy, task_delete 兩個 tasks，只要輸入 gradle task_c 即可執行 task_copy；也可用 camel case 縮寫
    ```groovy
    task copyFile {
    doLast { println 'copyFile.doLast'}
    }
    task deleteFile {
    doLast { println 'deleteFile.doLast'}
    }
    =================
    // gradle c
    // Note: This is a predefined task for Gradle
    > Task :components
    =================
    // gradle copy
    > Task :copyFile
    > copyFile.doLast
    =================
    // gradle dF
    > Task :deleteFile
    > deleteFile.doLast
    ```
---
## doLast
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
---
## doLast vs doFirst
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
---
## copy
```groovy
import java.time.LocalDateTime
import java.time.format.DateTimeFormatter

// Backup root/*.md and root/build.gradle to backup/
// Append now datetime pattern to each backuped file
// Note: The root path is based on the directory of build.gradle
task backupFiles(type:Copy) {
  from '.'
  into 'backup'
  include '*.md','build.gradle'
  String now = LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyyMMdd-HHmmss"))
  rename { String fileName -> fileName.concat('.').concat(now).concat('.').concat('bk') }  
}
```
---
## <a name="gradle_build"></a>gradle build v.s. gradle [task name]
```groovy
// build.gardle
task task1 {
  println 'task1'
  doLast { println 'task1.doLast'}
}
task task2 {
  println 'task2'
  doLast { println 'task2.doLast'}
}
task task3 {
  println 'task3'
  doLast { println 'task3.doLast'}
}
==============
// gradle build
> Configure project :
task1
task2
task3

> Task :buildEnvironment
==============
// gradle task2
> Configure project :
task1
task2
task3

> Task :task2
task2.doLast
```
---
## <a name="dependsOn"></a>dependsOn
``` groovy
task task1 {
  doFirst { println 'task1.doFirst'}
  println 'task1'
  doLast { println 'task1.doLast'}
}
task task2(dependsOn: task1) {
  doFirst { println 'task2.doFirst'}
  println 'task2'
  doLast { println 'task2.doLast'}
}
task task3 {
  dependsOn task2
  doFirst { println 'task3.doFirst'}
  println 'task3'
  doLast { println 'task3.doLast'}
}
======================
>gradle task1

> Configure project :
task1
task2
task3

> Task :task1
task1.doFirst
task1.doLast
======================
>gradle task2

> Configure project :
task1
task2
task3

> Task :task1
task1.doFirst
task1.doLast

> Task :task2
task2.doFirst
task2.doLast
======================
>gradle task3

> Configure project :
task1
task2
task3

> Task :task1
task1.doFirst
task1.doLast

> Task :task2
task2.doFirst
task2.doLast

> Task :task3
task3.doFirst
task3.doLast
```
---
## 
---
##  References:
1. [Gradle User Guide - 4.4.1](https://docs.gradle.org/4.4.1/userguide/userguide.html)
2. [Gradle学习系列之一——Gradle快速入门](https://www.cnblogs.com/davenkin/p/gradle-learning-1.html)

