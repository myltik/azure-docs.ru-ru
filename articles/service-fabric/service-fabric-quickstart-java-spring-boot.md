---
title: "Развертывание приложения Spring Boot в Microsoft Azure Service Fabric | Документация Майкрософт"
description: "Разверните приложение Spring Boot в Azure Service Fabric с помощью шаблона \"Начало работы\" для Spring Boot."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: d34862d96744e038d7c1890f703ead79c416ddfa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-a-spring-boot-application"></a>Развертывание приложения Spring Boot
Azure Service Fabric — это платформа распределенных систем для развертывания микрослужб и контейнеров и управления ими. 

В этом руководстве описано, как развернуть приложение Spring Boot в Service Fabric. Это краткое руководство использует пример [Начало работы](https://spring.io/guides/gs/spring-boot/) с веб-сайта Spring Boot. Это краткое руководство описывает на примере знакомых средств командной строки процесс развертывания Spring Boot в виде приложения Service Fabric. Когда вы завершите работу с руководством, у вас будет пример Spring Boot, работающий на Service Fabric. 

![Снимок экрана приложения](./media/service-fabric-quickstart-java-spring-boot/springbootsf.png)

Из этого краткого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Развертывание приложения Spring Boot в Service Fabric
> * Развертывание приложения в локальном кластере. 
> * Развертывание приложения в кластере Azure
> * Масштабирование приложения на несколько узлов
> * отработка отказа службы без ущерба для ее доступности.

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим кратким руководством сделайте следующее:
1. [Установите пакет SDK и интерфейс командной строки Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods).
2. [установите Git](https://git-scm.com/);
3. [установите Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables);
4. [настройте среду Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development).

## <a name="download-the-sample"></a>Скачивание примера приложения
В окне терминала выполните следующую команду, чтобы клонировать пример приложения "Начало работы" для Spring Boot на локальный компьютер.
```
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>Упаковка приложения Spring Boot 
1. В клонированном каталоге `gs-spring-boot` запустите команду `yo azuresfguest`. 

2. Введите следующие сведения для каждого запроса. 

    ![Записи Yeoman](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. В папке `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` создайте файл с именем `entryPoint.sh`. Добавьте в файл следующий код. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

Итак, на данный момент у вас есть приложение Service Fabric, созданное на основе примера "Начало работы" для Spring Boot, которое теперь можно развернуть в Service Fabric.

## <a name="run-the-application-locally"></a>Локальный запуск приложения
1. Запустите локальный кластер, выполнив следующую команду:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Запуск локального кластера занимает некоторое время. Чтобы убедиться, что кластер является рабочим, откройте Service Fabric Explorer по адресу **http://localhost:19080**. Наличие пяти работоспособных узлов означает, что локальный кластер запущен и работает. 
    
    ![Работоспособное состояние локального кластера](./media/service-fabric-quickstart-java/localclusterup.png)

2. Перейдите в папку `gs-spring-boot/SpringServiceFabric`.
3. Выполните следующую команду, чтобы подключиться к локальному кластеру. 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. Выполните скрипт `install.sh`. 

    ```bash
    ./install.sh
    ```

5. Откройте избранный веб-браузер и получите доступ к приложению по адресу **http://localhost: 8080**. 

    ![Локальная клиентская часть приложения](./media/service-fabric-quickstart-java-spring-boot/springbootsf.png)
    
Теперь вы можете получить доступ к приложению Spring Boot, которое развернуто в кластере Service Fabric.  

## <a name="deploy-the-application-to-azure"></a>Развертывание приложения в Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Настройка кластера Azure Service Fabric
Чтобы развернуть приложение в кластере Azure, создайте собственный кластер.

Кластеры сообщества — это бесплатные временные кластеры Service Fabric, размещенные в Azure. Их запускает команда Service Fabric. Любой пользователь может развертывать приложения в этих кластерах и изучать платформу. Чтобы получить доступ к кластеру сообщества, следуйте инструкциям в [этом разделе](http://aka.ms/tryservicefabric). 

См. дополнительные сведения о [создании кластера Service Fabric в Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

> [!Note]
> Служба Spring Boot прослушивает входящий трафик через порт 8080. Убедитесь, что порт открыт в кластере. При использовании кластера сообщества этот порт открыт.
>

### <a name="deploy-the-application-using-cli"></a>Развертывание приложения с помощью CLI
Теперь у вас готово приложение и кластер для него, и вы можете развернуть их в кластер прямо из командной строки.

1. Перейдите в папку `gs-spring-boot/SpringServiceFabric`.
2. Выполните следующую команду, чтобы подключиться к кластеру Azure. 

    ```bash
    sfctl cluster select --endpoint http://<ConnectionIPOrURL>:19080
    ```
    
    Если кластер защищен самозаверяющим сертификатом, используйте такую команду: 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. Выполните скрипт `install.sh`. 

    ```bash
    ./install.sh
    ```

4. Откройте любой веб-браузер и введите в адресную строку IP-адрес или URL-адрес приложения: **http://\<адрес_подключения>:8080**. 

    ![Локальная клиентская часть приложения](./media/service-fabric-quickstart-java-spring-boot/springsfazure.png)
    
Теперь вы можете получить доступ к приложению Spring Boot, которое развернуто в кластере Service Fabric.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Масштабирование приложений и служб в кластере
Службы могут легко масштабироваться в кластере с учетом изменения нагрузки на службы. Масштабирование службы осуществляется путем изменения числа экземпляров, запущенных в кластере. Существует несколько способов масштабирования служб — вы можете использовать сценарии или команды интерфейса командной строки Service Fabric (sfctl). В этом примере мы используем Service Fabric Explorer.

Service Fabric Explorer выполняется во всех кластерах Service Fabric. Чтобы его открыть, укажите адрес кластера и порт управления кластерами HTTP (19080) в адресной строке, например `http://demolinuxsecure.westus.cloudapp.azure.com:19080`.

Для масштабирования службы веб-интерфейса выполните следующие действия:

1. Откройте Service Fabric Explorer в своем кластере (например, по ссылке `http://demolinuxsecure.westus.cloudapp.azure.com:19080`).
2. Щелкните многоточие рядом с узлом **fabric:/SpringServiceFabric/SpringGettingStarted** в представлении в виде дерева и выберите **Масштабировать службу**.

    ![Масштабирование службы в Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/springbootsfhowtoscale.png)

    Теперь вы можете выбрать нужное количество экземпляров службы.

3. Укажите здесь число **5** и щелкните **Масштабировать службу**.

    Масштабирование службы можно настроить и с помощью командной строки, как описано ниже.

    ```bash 
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 5 --stateless 
    ``` 

4. Щелкните узел **fabric:/SpringServiceFabric/SpringGettingStarted** в представлении в виде дерева и разверните узел раздела (здесь отображается его идентификатор GUID).

    ![Завершение масштабирования службы в Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/springsfscaled.png)

    Теперь вы видите, что у службы есть пять экземпляров и представление в виде дерева позволяет определить, на каких узлах они запущены.

С помощью этой простой задачи управления мы увеличили количество ресурсов, доступных для обработки обращений пользователей к службе Spring. Важно понимать, что для надежной работы службы не требуется запускать несколько экземпляров службы. При сбое в работе службы Service Fabric запускает новый экземпляр службы в кластере.

## <a name="failover-services-in-a-cluster"></a>Службы отработки отказа в кластере 
Чтобы продемонстрировать отработку отказа службы, мы смоделируем перезагрузку узла с помощью Service Fabric Explorer. Убедитесь, что запущен только один экземпляр службы. 

1. Откройте Service Fabric Explorer в своем кластере (например, по ссылке `http://demolinuxsecure.westus.cloudapp.azure.com:19080`).
2. Нажмите кнопку с многоточием рядом с узлом, на котором запущен экземпляр этой службы, и выберите команду перезапуска узла. 

    ![Service Fabric Explorer с командой перезапуска узла](./media/service-fabric-quickstart-java-spring-boot/springbootsfrestart.png)
3. Теперь этот экземпляр службы переместится на другой узел, при этом приложение не испытывает никаких простоев. 

    ![Service Fabric Explorer после успешного перезапуска узла](./media/service-fabric-quickstart-java-spring-boot/springbootsfrestartsucceed.png)

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как выполнить следующие действия:

> [!div class="checklist"]
> * Развертывание приложения Spring Boot в Service Fabric
> * Развертывание приложения в локальном кластере. 
> * Развертывание приложения в кластере Azure
> * Масштабирование приложения на несколько узлов
> * отработка отказа службы без ущерба для ее доступности.

* Дополнительные сведения [о разработке микрослужб Java с помощью моделей программирования Service Fabric](service-fabric-quickstart-java-reliable-services.md)
* См. дополнительные сведения о [настройке непрерывной интеграции и развертывания с использованием Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).
* Просмотрите другие [примеры Java](https://github.com/Azure-Samples/service-fabric-java-getting-started).