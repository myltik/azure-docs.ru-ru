---
title: Развертывание приложения Spring Boot в Microsoft Azure Service Fabric | Документация Майкрософт
description: В этом кратком руководстве вы развернете приложение Spring Boot для Azure Service Fabric, используя пример приложения Spring Boot.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: e41a7754e6e170dda7818bceadab7858a9d9fa76
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-azure"></a>Краткое руководство: развертывание приложения Java Spring Boot в Azure
Azure Service Fabric — это платформа распределенных систем для развертывания микрослужб и контейнеров и управления ими. 

В этом руководстве объясняется, как развернуть приложение Spring Boot в Service Fabric. Это краткое руководство использует пример [Начало работы](https://spring.io/guides/gs/spring-boot/) с веб-сайта Spring Boot. В этом кратком руководстве приведены пошаговые инструкции по развертыванию Spring Boot в виде приложения Service Fabric с использованием знакомых средств командной строки. Когда вы завершите работу с руководством, у вас будет пример Spring Boot, работающий на Service Fabric. 

![Снимок экрана приложения](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

Из этого краткого руководства вы узнаете, как выполнять следующие задачи:

* Развертывание приложения Spring Boot в Service Fabric
* Развертывание приложения в локальном кластере. 
* Развертывание приложения в кластере Azure
* Масштабирование приложения на несколько узлов
* отработка отказа службы без ущерба для ее доступности.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим кратким руководством сделайте следующее:
1. [Установите пакет SDK и интерфейс командной строки Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods).
2. [установите Git](https://git-scm.com/);
3. [установите Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables);
4. [настройте среду Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development).

## <a name="download-the-sample"></a>Скачивание примера приложения
В окне терминала выполните следующую команду, чтобы клонировать пример приложения Spring Boot Getting Started на локальный компьютер.
```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>Упаковка приложения Spring Boot 
1. В клонированном экземпляре каталога `gs-spring-boot` выполните команду `yo azuresfguest`. 

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
    
    ![Работоспособное состояние локального кластера](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

2. Перейдите в папку `gs-spring-boot/SpringServiceFabric`.
3. Выполните следующую команду, чтобы подключиться к локальному кластеру. 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. Выполните скрипт `install.sh`. 

    ```bash
    ./install.sh
    ```

5. Откройте любой веб-браузер и перейдите к приложению по адресу **http://localhost:8080**. 

    ![Локальная клиентская часть приложения](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)
    
Теперь вы можете получить доступ к приложению Spring Boot, которое развернуто в кластере Service Fabric.  

## <a name="deploy-the-application-to-azure"></a>Развертывание приложения в Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Настройка кластера Azure Service Fabric
Чтобы развернуть приложение в кластере Azure, создайте собственный кластер.

Общедоступные кластеры — это размещенные в Azure бесплатные временные кластеры Service Fabric, которыми управляет команда Service Fabric. Общедоступные кластеры можно использовать для развертывания приложений и изучения возможностей платформы. Кластер использует один самозаверяющий сертификат для обеспечения безопасности при взаимодействии между узлами и между клиентом и узлом.

Войдите в систему и [присоедините кластер Linux](http://aka.ms/tryservicefabric). Загрузите сертификат PFX на компьютер, щелкнув ссылку **PFX**. Щелкните ссылку на **файл сведений**, чтобы найти пароль для сертификата и инструкции о том, как настраивать различные среды для использования сертификата. Не закрывайте страницу **приветствия** и **файл сведений**. Некоторые из этих инструкций будут использованы на следующих шагах. 

> [!Note]
> В течение одного часа доступно ограниченное число общедоступных кластеров. Если при попытке регистрации в общедоступном кластере появляется сообщение об ошибке, подождите немного и повторите попытку или следуйте инструкциям из раздела о [создании кластера Service Fabric в Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md), чтобы создать кластер в подписке. 
>
> Служба Spring Boot прослушивает входящий трафик через порт 8080. Убедитесь, что порт открыт в кластере. При использовании кластера сообщества этот порт открыт.
>

Service Fabric предоставляет ряд средств, которые можно использовать для управления кластером и его приложениями:

- средство Service Fabric Explorer на основе браузера;
- интерфейс командной строки (CLI) Service Fabric, который работает на базе Azure CLI 2.0;
- команды PowerShell. 

При работе с этим кратким руководством используется интерфейс командной строки Service Fabric и Service Fabric Explorer. 

Чтобы использовать CLI, создайте PEM-файл на основе скачанного PFX-файла. Для преобразования файла используйте приведенную ниже команду. (Для общедоступных кластеров можно скопировать команду, указанную для PFX-файла, из инструкций на странице **файла сведений**.)

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ``` 

Чтобы использовать Service Fabric Explorer, необходимо импортировать PFX-файл сертификата, скачанный с веб-сайта общедоступного кластера, в хранилище сертификатов (Windows или Mac) или в сам браузер (Ubuntu). Вам потребуется пароль закрытого ключа PFX-файла, который можно найти на странице **файла сведений**.

Для импорта сертификата на компьютер можно использовать любой удобный метод. Например: 

- В Windows: дважды щелкните PFX-файл и следуйте инструкциям на экране для установки сертификата в личном хранилище, `Certificates - Current User\Personal\Certificates`. Кроме того, можно использовать команду PowerShell из инструкций в **файле сведений**.
- В Mac: дважды щелкните PFX-файл и следуйте инструкциям на экране, чтобы установить сертификат в цепочке ключей.
- В Ubuntu: браузером по умолчанию в Ubuntu 16.04 является Mozilla Firefox. Чтобы импортировать сертификат в Firefox, нажмите кнопку меню в правом верхнем углу браузера, а затем щелкните **Options** (Параметры). На странице **Preferences** (Настройки) введите в поле поиска слово "сертификаты". Нажмите кнопку **View Certificates** (Просмотр сертификатов), выберите вкладку **Your Certificates** (Ваши сертификаты), щелкните **Import** (Импорт) и следуйте инструкциям на экране, чтобы импортировать сертификат.
 
   ![Установка сертификата в Firefox](./media/service-fabric-quickstart-java-spring-boot/install-cert-firefox.png) 


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

4. Откройте браузер и введите в адресную строку адрес приложения: **http://\<IP-адрес_или_URL-адрес_подключения>:8080**. 

    ![Локальная клиентская часть приложения](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)
    
Теперь вы можете получить доступ к приложению Spring Boot, работающему в кластере Service Fabric в Azure.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Масштабирование приложений и служб в кластере
Службы могут легко масштабироваться в кластере с учетом изменения нагрузки на них. Масштабирование службы осуществляется путем изменения числа экземпляров, запущенных в кластере. Существует множество способов масштабирования служб. Например, можно использовать скрипты или команды интерфейса командной строки Service Fabric (sfctl). В приведенных далее шагах используйте Service Fabric Explorer.

Средство Service Fabric Explorer работает во всех кластерах Service Fabric. Чтобы его открыть, укажите адрес кластера и порт управления HTTP (19080) для кластера в адресной строке браузера, например `http://localhost:19080`.

Для масштабирования службы веб-интерфейса сделайте следующее:

1. Откройте Service Fabric Explorer в своем кластере (например, по ссылке `http://localhost:19080`).
2. Щелкните многоточие рядом с узлом **fabric:/SpringServiceFabric/SpringGettingStarted** в представлении в виде дерева и выберите **Масштабировать службу**.

    ![Масштабирование службы в Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Теперь вы можете выбрать нужное количество экземпляров службы.

3. Укажите здесь число **3** и щелкните **Масштабировать службу**.

    Масштабирование службы можно настроить и с помощью командной строки, как описано ниже.

    ```bash 
    # Connect to your local cluster
    sfctl cluster select --endpoint http://localhost:19080

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 3 --stateless 
    ``` 

4. Щелкните узел **fabric:/SpringServiceFabric/SpringGettingStarted** в представлении в виде дерева и разверните узел раздела (здесь отображается его идентификатор GUID).

    ![Завершение масштабирования службы в Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    У службы есть три экземпляра. Представление в виде дерева демонстрирует, на каких узлах они запущены.

С помощью этой простой задачи управления вы удвоили количество ресурсов для обработки пользовательской нагрузки для службы веб-интерфейса. Важно понимать, что для надежной работы службы не требуется запускать несколько экземпляров службы. При сбое в работе службы Service Fabric запускает новый экземпляр службы в кластере.

## <a name="fail-over-services-in-a-cluster"></a>Службы отработки отказа в кластере 
Чтобы продемонстрировать отработку отказа службы, с помощью Service Fabric Explorer моделируется перезагрузка узла. Убедитесь, что запущен только один экземпляр службы. 

1. Откройте Service Fabric Explorer в своем кластере (например, по ссылке `http://localhost:19080`).
2. Нажмите кнопку с многоточием рядом с узлом, на котором запущен экземпляр этой службы, и выберите команду перезапуска узла. 

    ![Service Fabric Explorer с командой перезапуска узла](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. Этот экземпляр службы перемещается в другой узел без простоев в работе приложения. 

    ![Service Fabric Explorer после успешного перезапуска узла](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие действия:

* Развертывание приложения Spring Boot в Service Fabric
* Развертывание приложения в локальном кластере. 
* Развертывание приложения в кластере Azure
* Масштабирование приложения на несколько узлов
* отработка отказа службы без ущерба для ее доступности.

Дополнительные сведения о работе с приложениями Java в Service Fabric см. в руководстве по приложениям Java.

> [!div class="nextstepaction"]
> [Развертывание приложения Java](./service-fabric-tutorial-create-java-app.md)
