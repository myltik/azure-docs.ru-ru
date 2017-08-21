---
title: "Обработка событий из концентраторов событий с помощью Storm в HDInsight с использованием Java | Документация Майкрософт"
description: "Узнайте, как обрабатывать данные концентраторов событий, используя топологии Java Storm, созданные с помощью Maven."
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 453fa7b0-c8a6-413e-8747-3ac3b71bed86
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/13/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 2e8ebbdab2be7bed224a67facec798820615bb22
ms.contentlocale: ru-ru
ms.lasthandoff: 07/14/2017

---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (Java)

Узнайте, как использовать концентраторы событий Azure со Storm в HDInsight. В этом примере используются компоненты на основе Java для чтения и записи данных в концентраторах событий Azure.

Служба концентраторов событий Azure позволяет обрабатывать значительные объемы данных из веб-сайтов, приложений и устройств. Элемент spout концентратора событий упрощает использование Apache Storm в HDInsight для анализа этих данных в режиме реального времени. Вы также можете записывать данные в службу концентраторов событий из Storm с помощью сита службы концентраторов событий.

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Storm в кластере HDInsight версии 3.6. Дополнительные сведения см. в статье [Руководство по Apache Storm в HDInsight: начало работы с анализом больших объемов данных в HDInsight с помощью примеров Storm Starter](hdinsight-apache-storm-tutorial-get-started-linux.md).

    > [!IMPORTANT]
    > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Концентратор событий Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* Пакет [Oracle Java Developer Kit (JDK) версии 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) или аналогичный пакет, например [OpenJDK](http://openjdk.java.net/).

* [Maven](https://maven.apache.org/download.cgi) — это система сборки проектов Java.

* Текстовый редактор либо интегрированная среда разработки (IDE).

    > [!NOTE]
    > В редакторе или интегрированной среде разработки могут быть предусмотрены специальные функциональные возможности для работы с Maven, не описанные в настоящем документе. Информацию о возможностях среды редактирования см. в документации продукта, который вы используете.

    * Клиент SSH. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

* Команды `ssh` и `scp`. Они используются для копирования файлов в кластер HDInsight. В Windows вы можете получить их через Bash в Windows 10.

## <a name="understanding-the-example"></a>Общие сведения о примере

Пример [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) включает две топологии:

Топология `resources/writer.yaml` записывает случайные данные в концентратор событий Azure. Данные генерируются с помощью компонента `DeviceSpout` и представляют собой случайные идентификатор устройства и значение устройства. Определенное устройство получает команду на передачу идентификатора строки и числового значения.

Топология `resources/reader.yaml` считывает данные из концентратора событий (данные, записанные EventHubWriter), анализирует данные JSON, а затем регистрирует данные `deviceId` и `deviceValue`.

Перед записью в концентратор событий данные преобразуются в формат JSON, а при чтении содержимое файла JSON анализируется и преобразуется в кортежи. Используется следующий формат JSON:

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>Конфигурация проекта

Файл `POM.xml` содержит сведения о конфигурации проекта Maven. Интересные фрагменты:

#### <a name="event-hub-components"></a>Компоненты концентратора событий

Компонент, который считывает и записывает в концентраторы событий Azure, находится в [репозитории HDInsight](https://github.com/hdinsight/mvn-rep). Следующие разделы в файле `POM.xml` загружают компоненты из этого репозитория.

```xml
<repositories>
    <repository>
        <id>hdinsight-examples</id>
        <url>http://raw.github.com/hdinsight/mvn-repo/master</url>
    </repository>
</repositories>
```

#### <a name="the-eventhubs-storm-spout-dependency"></a>Зависимость EventHubs Storm Spout

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>${storm.eventhub.version}</version>
</dependency>
```

Этот XML-файл определяет зависимость для пакета eventhubs, который содержит как элемент spout для чтения данных из концентратора событий, так и элемент bolt для записи в него.

```xml
</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

Этот XML-файл настраивает проект для создания выходных данных для Java 8, которые использует HDInsight версии 3.5 или выше.

#### <a name="the-maven-shade-plugin"></a>maven-shade-plugin

```xml
<!-- build an uber jar -->
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-shade-plugin</artifactId>
<version>2.3</version>
<configuration>
    <transformers>
    <!-- Keep us from getting a can't overwrite file error -->
    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer"/>
    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
    </transformers>
    <!-- Keep us from getting a bad signature error -->
    <filters>
    <filter>
        <artifact>*:*</artifact>
        <excludes>
            <exclude>META-INF/*.SF</exclude>
            <exclude>META-INF/*.DSA</exclude>
            <exclude>META-INF/*.RSA</exclude>
        </excludes>
    </filter>
    </filters>
</configuration>
<executions>
    <execution>
    <phase>package</phase>
    <goals>
        <goal>shade</goal>
    </goals>
    </execution>
</executions>
</plugin>
```

Этот подключаемый модуль xml настраивает решение, чтобы упаковать выходные данные в файл типа uber jar. JAR-файл содержит код проекта и требуемые зависимости. Он также используется для:

* Переименования файлов лицензии для зависимостей.
* Исключения групп безопасности и подписей.
* Различные реализации одного и того же интерфейса должны быть объединены в одну запись.

Эти параметры конфигурации предотвращают ошибки во время выполнения.

#### <a name="topology-definitions"></a>Определения топологии

В этом примере используется платформа [Flux](https://storm.apache.org/releases/1.1.0/flux.html). Она использует YAML для определения топологий. Основное преимущество заключается в том, что вы не жестко кодируете топологию в коде Java. Так как определение типа YAML, вы можете изменить его перед отправкой топологии, не перекомпилируя все.

__writer.yaml__:

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubwriter"

components:
  # Configure the Event Hub spout
  - id: "eventhubbolt-config"
    className: "org.apache.storm.eventhubs.bolt.EventHubBoltConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.write.policy.name}"
      - "${eventhub.write.policy.key}"
      - "${eventhub.namespace}"
      - "servicebus.windows.net"
      - "${eventhub.name}"

spouts:
  - id: "device-emulator-spout"
    className: "com.microsoft.example.DeviceSpout"
    parallelism: ${eventhub.partitions}

bolts:
  - id: "eventhub-bolt"
    className: "org.apache.storm.eventhubs.bolt.EventHubBolt"
    constructorArgs:
      - ref: "eventhubbolt-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

# How data flows through the components
streams:
  - name: "spout -> eventhub" # just a string used for logging
    from: "device-emulator-spout"
    to: "eventhub-bolt"
    grouping:
        type: SHUFFLE

  - name: "spout -> logger"
    from: "device-emulator-spout"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

__reader.yaml__:

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubreader"

components:
  # Configure the Event Hub spout
  - id: "eventhubspout-config"
    className: "org.apache.storm.eventhubs.spout.EventHubSpoutConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.read.policy.name}"
      - "${eventhub.read.policy.key}"
      - "${eventhub.namespace}"
      - "${eventhub.name}"
      - ${eventhub.partitions}

spouts:
  - id: "eventhub-spout"
    className: "org.apache.storm.eventhubs.spout.EventHubSpout"
    constructorArgs:
      - ref: "eventhubspout-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

bolts:
  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

  # Parses from JSON into tuples
  - id: "parser-bolt"
    className: "com.microsoft.example.ParserBolt"
    parallelism: ${eventhub.partitions}

# How data flows through the components
streams:
  - name: "spout -> parser" # just a string used for logging
    from: "eventhub-spout"
    to: "parser-bolt"
    grouping:
        type: SHUFFLE

  - name: "parser -> log-bolt"
    from: "parser-bolt"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

#### <a name="tell-the-topology-about-event-hub"></a>Передача конфигурации концентратора событий в топологию

Во время выполнения файл `dev.properties` используется для передачи конфигурации концентратора событий в топологию. Ниже приведено содержимое этого файла по умолчанию:

```yaml
eventhub.write.policy.name: writer
eventhub.write.policy.key: your_key_here
eventhub.read.policy.name: reader
eventhub.read.policy.key: your_key_here
eventhub.namespace: your_namespace_here
eventhub.name: storm
eventhub.partitions: 2
```

## <a name="configure-environment-variables"></a>Настройка переменных среды

Во время установки Java и JDK на компьютере, где ведется разработка, могут быть установлены следующие переменные среды. Однако следует убедиться, что они существуют и что они содержат правильные значения для вашей системы.

* **JAVA_HOME** — эта переменная должна указывать на каталог, в который установлена среда выполнения Java (JRE). Например, в дистрибутиве Unix или Linux она должна иметь примерно такое значение: `/usr/lib/jvm/java-7-oracle` В Windows значение будет приблизительно таким: `c:\Program Files (x86)\Java\jre1.7`
* **PATH** — эта переменная должна содержать следующие пути:

  * **JAVA_HOME** или эквивалентный путь;
  * **JAVA_HOME\bin** или эквивалентный путь.
  * Каталог, в который установлено ПО Maven.

## <a name="configure-event-hub"></a>Настройка концентраторов Event Hub

Служба концентраторов событий используется в качестве источника данных для этого примера. Чтобы создать концентратор событий, выполните следующие действия.

1. На [классическом портале Azure](https://manage.windowsazure.com) выберите **Создать** > **Служебная шина** > **Концентратор событий** > **Настраиваемое создание**.

2. В диалоговом окне **Добавить новый концентратор событий** введите **имя концентратора событий**. Выберите **регион** для его создания и создайте пространство имен или выберите имеющееся. Затем щелкните **стрелку**, чтобы продолжить.

    ![страница мастера 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

   > [!NOTE]
   > Чтобы сократить задержки и затраты, в поле **Расположение** следует выбрать то же расположение, в котором находится сервер Storm в HDInsight.

3. В диалоговом окне **Настроить концентратор событий** введите значения параметров **Количество разделов** и **Хранение сообщений**. В этом примере числу разделов присвойте значение 10, а хранению сообщений — 1. Запомните количество разделов, так как позже вам понадобится это значение.

    ![страница мастера 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

4. После создания концентратора событий выберите пространство имен, выберите **Концентраторы событий**, а затем выберите концентратор событий, который был создан ранее.
5. Выберите **Настроить** и создайте две политики доступа, используя следующие сведения.

    <table>
    <tr><th>Имя</th><th>Разрешения</th></tr>
    <tr><td>Модуль записи</td><td>Отправка</td></tr>
    <tr><td>читатель.</td><td>Прослушивание</td></tr>
    </table>

    После создания разрешений выберите значок **Сохранить** в нижней части страницы. Эти политики общего доступа используются для чтения и записи в концентратор событий.

    ![политики](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

6. После сохранения политик используйте **генератор общего ключа доступа** в нижней части страницы для получения ключа для политик **writer** и **reader**. Сохраните эти значения.

## <a name="download-and-build-the-project"></a>Загрузка и сборка проекта

1. Скачайте проект [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) с сайта GitHub в виде ZIP-файла либо клонируйте проект локально с помощью [git](https://git-scm.com/).

2. Измените файл `dev.properties`, используя конфигурацию вашего концентратора событий.

3. Используйте следующую команду, чтобы собрать и упаковать проект:

        mvn package

    Эта команда скачивает необходимые зависимости, а также создает и упаковывает проект. Выходные данные сохраняются в файл **EventHubExample-1.0-SNAPSHOT.jar** в каталоге **/target**.

## <a name="test-locally"></a>Локальное тестирование

Так как эти топологии просто считываются и записываются в концентраторы событий, вы можете протестировать их локально, если у вас есть [среда разработки Storm](http://storm.apache.org/releases/current/Setting-up-development-environment.html). Для запуска локально в среде разработки выполните следующие действия:

1. Запустите средство записи:

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /writer.yaml --filter dev.properties

2. Запустите средство чтения:

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /reader.yaml --filter dev.properties

> [!TIP]
> * `--local`: запускает топологию в локальном режиме (нераспространяемый).
> * `-R /writer.yaml`: загружает определение топологии из раздела `resources`, упакованного в JAR-файл. Если топология — это файл в локальной файловой системе, укажите путь к нему в качестве последнего параметра.
> * `--filter dev.properties`: использование содержимого `dev.properties`, чтобы заполнить значения в определениях топологии. Пример: `${eventhub.read.policy.name}`.

Выходные данные регистрируются в консоли при локальном выполнении. Чтобы остановить топологию, нажмите клавиши __CTRL+C__.

## <a name="deploy-the-topologies"></a>Развертывание топологий

1. Используя SCP, скопируйте JAR-пакет в свой кластер HDInsight. Замените USERNAME именем пользователя SSH для кластера. Замените CLUSTERNAME именем кластера HDInsight.

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Если для учетной записи SSH используется пароль, вам потребуется его ввести. Если для учетной записи SSH используется ключ, возможно, вам потребуется использовать параметр `-i` , чтобы указать путь к файлу ключа. Например, `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

    Эта команда скопирует файл в основной каталог пользователя SSH в кластере.

2. После завершения передачи подключитесь к кластеру HDInsight по протоколу SSH. Замените **USERNAME** именем пользователя SSH. Замените **CLUSTERNAME** именем кластера HDInsight.

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!NOTE]
    > Если для учетной записи SSH используется пароль, вам потребуется его ввести. Если для учетной записи SSH используется ключ, возможно, вам потребуется использовать параметр `-i` , чтобы указать путь к файлу ключа. В следующем примере выполняется загрузка закрытого ключа из файла `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Запустите топологии, используя следующую команду:

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties

    > [!TIP]
    > * `--remote`: отправляет топологию в службу Nimbus, которая запускает ее на рабочих узлах кластера.

4. Чтобы просмотреть зарегистрированные данные, перейдите на страницу https://CLUSTERNAME.azurehdinsight.net/stormui, где __CLUSTERNAME__ — это имя вашего кластера HDInsight. Выберите топологии и перейдите к компонентам. Выберите запись __port__, чтобы просмотреть зарегистрированные сведения об этом компоненте.

5. Чтобы остановить топологии, используйте следующие команды:

        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Примеры топологий для Storm в HDInsight](hdinsight-storm-example-topology.md)

