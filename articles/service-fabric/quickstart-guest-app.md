---
title: Быстрое развертывание имеющегося приложения в кластере Azure Service Fabric
description: Используйте кластер Azure Service Fabric, чтобы разместить имеющееся приложение Node.js с помощью Visual Studio.
services: service-fabric
documentationcenter: nodejs
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: adegeo
ms.openlocfilehash: 3cb7fdf309c935c21f4e80c1d1ba529838935acc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213016"
---
# <a name="host-a-nodejs-application-on-azure-service-fabric"></a>Размещение приложения Node.js в Azure Service Fabric

Это краткое руководство поможет вам развернуть имеющееся приложение (Node.js в этом примере) в кластере Service Fabric, выполняющемся в Azure.

## <a name="prerequisites"></a>предварительным требованиям

Перед началом работы [настройте среду разработки](service-fabric-get-started.md). Эта настройка включает установку пакета SDK для Service Fabric и Visual Studio 2017 или 2015.

У вас также должно быть имеющееся приложение Node.js для развертывания. В этом кратком руководстве используется простой веб-сайт Node.js, который можно загрузить [здесь][download-sample]. Извлеките этот файл в папку `<path-to-project>\ApplicationPackageRoot\<package-name>\Code\` после создания проекта на следующем шаге.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure][create-account].

## <a name="create-the-service"></a>Создание службы

Запустите Visual Studio от имени **администратора**.

Создайте проект, нажав клавиши `CTRL`+`SHIFT`+`N`.

В диалоговом окне **Создание проекта** выберите **Облако > Приложение Service Fabric**.

Присвойте приложению имя **MyGuestApp** и нажмите кнопку **ОК**.

>[!IMPORTANT]
>Node.js может легко преодолеть лимит в 260 знаков для путей Windows. Используйте короткий путь для самого проекта, например **c:\code\svc1**. При необходимости можно выполнить **[эти инструкции](https://stackoverflow.com/a/41687101/1664231)**, чтобы включить длинные пути к файлам в Windows 10.
   
![Диалоговое окно "Новый проект" в Visual Studio][new-project]

Вы можете создать любой тип службы Service Fabric из следующего диалогового окна. В рамках этого краткого руководства выберите **Гостевой исполняемый файл**.

Назовите службу **MyGuestService** и задайте параметрам справа следующие значения:

| Параметр                   | Значение |
| ------------------------- | ------ |
| Папка пакета кода       | _&lt;папка с вашим приложением Node.js&gt;_ |
| Поведение пакета кода     | Скопируйте содержимое папки в проект |
| Программа                   | node.exe |
| Аргументы                 | server.js |
| Рабочая папка            | CodePackage |

Нажмите кнопку **ОК**.

![Диалоговое окно "Новая служба" в Visual Studio][new-service]

Visual Studio создаст проект приложения и проект службы субъекта, а затем отобразит их в обозревателе решений.

Проект приложения (**MyGuestApp**) не содержит никакой код. Он только ссылается на набор проектов служб. Также он содержит данные еще трех типов.

* **Профили публикации**  
Средства настройки для различных сред.

* **Сценарии**  
Сценарий PowerShell для развертывания и обновления приложения.

* **Определение приложения**  
Содержит манифест приложения в разделе *ApplicationPackageRoot*. Связанные файлы параметров приложения расположены в разделе *ApplicationParameters*. Они определяют характеристики этого приложения, и с помощью них можно настроить приложение для конкретной среды.
    
Обзор содержимого проекта службы вы найдете в статье [Начало работы со службами Reliable Services в Service Fabric](service-fabric-reliable-services-quick-start.md).

## <a name="set-up-networking"></a>Настройка сети

Пример приложения Node.js, которое мы развертываем, использует порт **80**, и нам нужно сообщить Service Fabric о необходимости использования этого порта.

Откройте файл **ServiceManifest.xml** в проекте. В нижней области манифеста находится `<Resources> \ <Endpoints>` с уже определенной записью. Измените эту запись, чтобы добавить `Port`, `Protocol` и `Type`. 

```xml
  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyGuestAppServiceTypeEndpoint" Port="80" Protocol="http" Type="Input" />
    </Endpoints>
  </Resources>
```

## <a name="deploy-to-azure"></a>Развернуть в Azure

Если вы нажмете клавишу **F5** и запустите проект, он будет развернут в локальном кластере. Тем не менее давайте лучше развернем его в Azure.

Щелкните проект правой кнопкой мыши и выберите **Опубликовать**, чтобы открыть диалоговое окно для публикации в Azure.

![Публикация в диалоговом окне Azure для службы Service Fabric][publish]

Выберите целевой профиль **PublishProfiles\Cloud.xml**.

Выберите учетную запись Azure для развертывания, если вы этого еще не сделали. Если у вас ее нет, [зарегистрируйте ее][create-account].

В разделе **Конечная точка подключения:** выберите кластер Service Fabric для развертывания. Если у вас нет кластера, выберите **&lt;Создать новый кластер&gt;**. Откроется окно веб-браузера на портале Azure. Дополнительные сведения см. в разделе [Создание кластера на портале Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal). 

При создании кластера Service Fabric обязательно установите для параметра **настраиваемых конечных точек** значение **80**.

![Конфигурация типа узла Service Fabric с настраиваемой конечной точкой][custom-endpoint]

Создание нового кластера Service Fabric займет некоторое время. После его создания вернитесь в диалоговое окно публикации и выберите **&lt;Обновить&gt;**. Новый кластер появится в окне раскрывающегося списка. Выберите его.

Щелкните **Опубликовать** и дождитесь завершения развертывания.

Это может занять несколько минут. После завершения развертывания необходимо подождать некоторое время, чтобы приложение стало полностью доступным.

## <a name="test-the-website"></a>Тестирование веб-сайта

После публикации службы проверьте ее в веб-браузере. 

Сначала откройте портал Azure и найдите службу Service Fabric.

Просмотрите колонку обзора адреса службы. Используйте доменное имя из свойства _конечной точки подключения клиента_. Например, `http://mysvcfab1.westus2.cloudapp.azure.com`.

![Колонка обзора Service Fabric на портале Azure][overview]

Перейдите на этот адрес, где вы увидите ответ `HELLO WORLD`.

## <a name="delete-the-cluster"></a>Удаление кластера

Не забудьте удалить все ресурсы, которые вы создали в рамках этого краткого руководства, так как за использование этих ресурсов взимается плата.

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с дополнительными сведениями о [гостевых исполняемых файлах](service-fabric-guest-executables-introduction.md).

<!-- Image References -->

[new-project]: ./media/quickstart-guest-app/new-project.png
[new-service]: ./media/quickstart-guest-app/template.png
[solution-exp]: ./media/quickstart-guest-app/solution-explorer.png
[publish]: ./media/quickstart-guest-app/publish.png
[overview]: ./media/quickstart-guest-app/overview.png
[custom-endpoint]: ./media/quickstart-guest-app/custom-endpoint.png

[download-sample]: https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/service-fabric-node-website.zip
[create-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
