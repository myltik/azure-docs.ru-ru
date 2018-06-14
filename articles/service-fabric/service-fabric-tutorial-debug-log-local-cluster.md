---
title: Отладка приложения Java в локальном кластере Azure Service Fabric | Документация Майкрософт
description: Из этого руководства вы узнаете, как выполнить отладку и получить журналы из приложения Java в Service Fabric, которое запущено в локальном кластере.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 7e06048da4db121136bbbb7cd155532f86015da1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29949809"
---
#  <a name="tutorial-debug-a-java-application-deployed-on-a-local-service-fabric-cluster"></a>Руководство по отладке приложения Java, развернутом в локальном кластере Service Fabric 
Это руководство представляет собой вторую часть цикла. Вы узнаете, как подключить удаленный отладчик с помощью Eclipse для приложения Service Fabric. Кроме того, вы узнаете, как перенаправлять журналы из запущенных приложений в расположение, удобное для разработчика.

Из второй части цикла вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * отладка приложения Java с помощью Eclipse;
> * перенаправление журналов в настраиваемое расположение.

Из этого цикла руководств вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> *  [Развертывание приложения Java служб Service Fabric Reliable Services в Azure.](service-fabric-tutorial-create-java-app.md)
> * Развертывание и отладка приложения в локальном кластере.
> * [Развертывание приложения в кластере Azure.](service-fabric-tutorial-java-deploy-azure.md)
> * [Настройка мониторинга и диагностики приложения](service-fabric-tutorial-java-elk.md)
> * [Настройка процесса непрерывной интеграции и доставки](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы с этим руководством выполните следующие действия:
- Настройте среду разработки для [Mac](service-fabric-get-started-mac.md) или [Linux](service-fabric-get-started-linux.md). Следуя инструкциям, установите подключаемый модуль Eclipse, Gradle, пакет SDK для Service Fabric и интерфейс командной строки Service Fabric (sfctl).

## <a name="download-the-voting-sample-application"></a>Скачивание примера приложения для голосования
Если вы не создавали пример приложения для голосования [в первой части этой серии руководств](service-fabric-tutorial-create-java-app.md), вы можете скачать его. В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

[Создайте и разверните](service-fabric-tutorial-create-java-app.md#deploy-application-to-local-cluster) приложение в локальном кластере разработки.

## <a name="debug-java-application-using-eclipse"></a>Отладка приложения Java с помощью Eclipse

1. Откройте интегрированную среду разработки (IDE) Eclipse на компьютере и выберите **File -> Import** (Файл -> Импорт).

2. Во всплывающем окне выберите параметры **General -> Existing Projects into Workspace** (Общие -> Существующие проекты в рабочую область) и нажмите кнопку "Next" (Далее). 

3. В окне импорта проектов выберите параметр **Select root directory** (Выбрать корневой каталог) и выберите каталог **Voting**. Если вы работали с руководством из первой части цикла, то каталог **Voting** находится в каталоге **Eclipse-workspace**. 

4. Обновите файл entryPoint.sh службы, которую необходимо отладить, так, чтобы он запускал процесс Java с параметрами удаленной отладки. В этом руководстве используется внешний интерфейс без отслеживания состояния: *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh*. Для отладки в этом примере задается порт 8001.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -jar VotingWeb.jar
    ```

5. Обновите манифест приложения, задав для отлаживаемой службы число экземпляров или реплик, равное единице. Эта настройка позволяет избежать конфликтов для порта, используемого для отладки. Например, для служб без отслеживания состояния, задайте ``InstanceCount="1"``, а для служб с отслеживанием состояния задайте целевые и минимальные размеры набора реплик, равные 1, следующим образом: `` TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

6. В среде IDE Eclipse выберите **Run -> Debug Configurations -> Remote Java Application** (Запустить -> Конфигурации отладки -> Удаленное приложение Java), нажмите кнопку **New** (Создать), затем установите свойства, как показано ниже, и щелкните **Apply** (Применить).

    ```
    Name: Voting
    Project: Voting
    Connection Type: Standard
    Host: localhost
    Port: 8001
    ```

7. Установите точку останова на строке 109 в файле *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java*. 

8. В обозревателе пакетов щелкните правой кнопкой мыши проект **Voting** и выберите **Service Fabric -> Publish Application** (Публикация приложения). 

9. В окне **публикации приложения** выберите **Local.json** в раскрывающемся списке и нажмите кнопку **Publish** (Опубликовать).

10. В среде IDE Eclipse выберите **Run -> Debug Configurations -> Remote Java Application** (Запустить -> Конфигурации отладки -> Удаленное приложение Java), щелкните конфигурацию **Voting**, которую вы создали ранее, и нажмите кнопку **Debug** (Отладить).

11. Откройте веб-браузер и перейдите по адресу **localhost: 8080**, чтобы активировать точку останова и перейти к **перспективе отладки** в Eclipse.

## <a name="redirect-application-logs-to-custom-location"></a>Перенаправление журналов приложения в пользовательское расположение

Ниже приведены пошаговые инструкции по перенаправлению журналов из расположения по умолчанию (*/var/log/syslog*) в пользовательское расположение. 

1. Сейчас приложения, работающие в кластерах Service Fabric для Linux, поддерживают принятие одного файла журнала. Поэтому журналы сохраняются в папке */tmp/mysfapp0.0.log*. Создайте файл с именем logging.properties в расположении *Voting/VotingApplication/VotingWebPkg/Code/logging.properties* и добавьте следующее содержимое.

    ```
    handlers = java.util.logging.FileHandler
    
    java.util.logging.FileHandler.level = ALL
    java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
    
    # This value specifies your custom location. You will have to ensure this path has read and write access by the process running the SF Application
    java.util.logging.FileHandler.pattern = /tmp/mysfapp0.0.log
    ```

2. Добавьте в файл *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh* следующий параметр для команды выполнения Java:

    ```bash
    -Djava.util.logging.config.file=logging.properties
    ```
    
    Ниже показан пример выполнения:
    
    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=logging.properties -jar VotingWeb.jar
    ```

На этом этапе вы узнали, как выполнить отладку и получить доступ к журналам приложений при разработке приложений Java в Service Fabric. 

## <a name="next-steps"></a>Дополнительная информация
В этой части руководства вы узнали, как выполнить следующие действия:

> [!div class="checklist"]
> * отладка приложения Java с помощью Eclipse;
> * перенаправление журналов в настраиваемое расположение.

Перейдите к следующему руководству:
> [!div class="nextstepaction"]
> [Развертывание приложения в Azure](service-fabric-tutorial-java-deploy-azure.md)