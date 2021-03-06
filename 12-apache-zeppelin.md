# Установка и работа с Apache Zeppelin

Данный инструмент пришел к нам из мира Data Science и представляет собой интерактивную среду разработки со свтелым приятным фоном, являсь по сути веб-приложением с набором интерпритаторов, подключаемых к различным средам, таким как Spark, Cassandra, MongoDB, Ignite и т.д.

Зачастую это намного удобнее развертывания отдельного проекта в IDEA, а возможности визуализации и подавно превосходят то, что входит в состав стандартных сред разработки.

#### Установка Apache Zeppelin \(Ubuntu\)

Его можно ставить как на свою локальную машину, имея Spark далеко в кластере, так и на машину со Spark, много ресурсов он не сожрет.

На страничке [http://zeppelin.apache.org/download.html ](http://zeppelin.apache.org/download.html)находите актуальный url для скачки и затягиваете файл. На момент написания статьи была актуальна версия 0.7.2\* \(в ней не было поддержки свеженького Spark 2.2\). 

```
$ wget http://apache-mirror.rbc.ru/pub/apache/zeppelin/zeppelin-0.7.2/zeppelin-0.7.2-bin-all.tgz
$ tar xvf zeppelin-0.7.2-bin-all.tgz
$ mv zeppelin-0.7.2-bin-all.tgz /usr/local/zeppelin
```

Затянули, распаковали, перевезли в приятное место. Теперь самое время проверить работоспособность.

Запускаем либо сам серверок с приложением, либо демона - для проверки сгодится.

```
$ cd /usr/local/zeppelin/bin
$ ./zeppelin.sh

Log dir doesn't exist, create /usr/local/zeppelin/logs
Pid dir doesn't exist, create /usr/local/zeppelin/run
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=512m; support was removed in 8.0
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/local/zeppelin/lib/interpreter/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/zeppelin/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
Aug 01, 2017 1:19:33 PM com.sun.jersey.api.core.PackagesResourceConfig init
```

Можно сходить на [localhost:8080](localhost:8080) или по тому адресу, где вы его развернули и убедиться, что все работает, а Spark подключается, путем создания простейшего Note в вашем Notebook \(выбрав в качестве интерпретатора spark, разумеется\).

В этой "заметке" достаточно набрать spark и нажать кнопку справа, чтобы убедиться, что подключение к Spark произошло без проблем.

![](/assets/zeppelin_1.jpg)

Если проблемы возникли, то скорее всего дело в том, что у вас что-то не то с переменными окружения и Zeppelin не может найти Spark, для этого ему надо помочь, сходив в конфиги самого Zeppelin, подробности по [ссылке](http://zeppelin.apache.org/docs/0.7.2/interpreter/spark.html)

Чтобы жизнь казалась приятнее, пропишем Zeppelin в переменные окружения.

```
$ sudo nano /etc/environment
```

Там добавляем переменную окружения и патчим PATH

```
PATH="/usr/local/zeppelin/bin:/usr/local/Spark/bin:/usr/local/sbin:/ ...
ZEPPELIN_HOME=/usr/local/zeppelin
```

Применяем изменения \(если что-то идет не так, перечитайте предыдущий параграф\), убеждаемся в их работоспобности, запускаем Zeppelin снова

```
$ source /etc/environment
$ printenv PATH
$ zeppelin.sh

Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=512m; support was removed in 8.0
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/local/zeppelin/lib/interpreter/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/zeppelin/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
Aug 01, 2017 1:39:05 PM com.sun.jersey.api.core.PackagesResourceConfig init
INFO: Scanning for root resource and provider classes in the packages:
  org.apache.zeppelin.rest
Aug 01, 2017 1:39:05 PM com.sun.jersey.api.core.ScanningResourceConfig logClasses
INFO: Root resource classes found:
  class org.apache.zeppelin.rest.ZeppelinRestApi
```

\*В 0.7.3 она есть, но в интерпретаторе по умолчанию все равно стоит Spark 2.1.

#### 

#### Установка под Windows \(например, 10\)

Если вам, как и мне в моих демо нужно что-то красиво продемонстрировать под Windows, то важными будут следующие уточнения.

Само собой, у вас должна быть Java и на нее должна указывать системная переменная JAVA\_HOME.

Кроме того, Spark под Windows нужна некоторая зависимость на Hadoop \(О, наследие былых веков\). И хоть никакого Hadoop ставить не надо, но положить в папку \(C:\hadoop, например\) кое-что придется. Кое-что - это файлик winutlis.ext на который должна будет указывать переменная HADOOP\_HOME - тогда Zeppelin сможет стартовать нормально.

В папке hadoop создайте каталог bin и в него положите winutils.exe. Достать подобный файлик \(обязательно совместимый с желаемой версией Spark - в моем случае это Hadoop 2.7.1\) можно [тут](https://github.com/steveloughran/winutils).

Запустить Zeppelin просто - в директории zeppelin\bin есть скрипты под Windows. 

1. Жмёте на zeppelin.cmd
2. Ждёте минутку, пока все поднимется
3. Как только видите в консольке, что веб-приложение поднялось на jetty и интерпретаторы нашлись - можно идти на [localhost:8080](localhost:8080)
4. Если что-то не работает, долго висит и т.д. - проверьте права на запись в директории log и run, которые Zeppelin пытается создать и что-то туда написать.

Также полезно во встроенном Spark интерпретаторе сбросить настройку **zeppelin.spark.useHiveContext **в false, если у вас не планируется в этой связке Hive-кластера.



#### Все работает, но хочется мощи Spark 2.2 \(рецепт для Windows\)?

1. Качаем Spark, кладем куда следует.
2. Добавляем системную переменную ZEPPELIN\_HOME с указанием на нашу директорию
3. Идем в конфиг zeppelin-env.cmd и устанавливаем SPARK\_HOME там **set SPARK\_HOME    = C:\home\spark ** 
4. Если что-то не работает \(Spark не подцепляется\), то уберите кавычки в скриптах spark-submit и sprak2-submit
5. ```
   cmd /V /E /C "%~dp0spark-submit2.cmd" %* => cmd /V /E /C %~dp0spark-submit2.cmd %*
   ```

Идете в Zeppelin и проверяете, что spark.version возвращает вам то, что нужно.

