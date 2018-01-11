---
title: "Развертывание приложения Azure Service Fabric с непрерывной интеграцией (Team Services) | Документы Майкрософт"
description: "Общие сведения о том, как настроить непрерывную интеграцию и развертывание для приложения Service Fabric с помощью Visual Studio Team Services.  Разверните приложение в кластере Service Fabric в Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/13/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 2fb7ab906208a58c0b5cd3af8b53188fbab94029
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2017
---
# <a name="deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Развертывание приложения с непрерывной интеграцией и развертыванием в кластере Service Fabric
Это руководство из цикла. В нем описано, как настроить непрерывные интеграцию и развертывание для приложения Azure Service Fabric с помощью Visual Studio Team Services.  Вам потребуется приложение Service Fabric. В качестве примера используется приложение, созданное в разделе [Создание приложения .NET](service-fabric-tutorial-create-dotnet-app.md).

В третьей части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Добавление проекта в систему управления версиями
> * Создание определения сборки в Team Services
> * Создание определения выпуска в Team Services
> * Автоматическое развертывание и обновление приложения

Из этого цикла руководств вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * [Создание приложения .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md).
> * [Развертывание приложения в удаленном кластере](service-fabric-tutorial-deploy-app-to-party-cluster.md).
> * Настройка непрерывной интеграции и непрерывного развертывания с помощью Visual Studio Team Services.
> * [Настройка мониторинга и диагностики приложения](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Технические условия
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Установите Visual Studio 2017](https://www.visualstudio.com/), а также рабочие нагрузки **разработка Azure** и **ASP.NET и веб-разработка**.
- [Установка пакета SDK для Service Fabric](service-fabric-get-started.md)
- Создайте кластер Service Fabric с Windows, например с помощью [этого руководства](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Создайте [учетную запись Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

## <a name="download-the-voting-sample-application"></a>Скачивание примера приложения для голосования
Если вы не создавали пример приложения для голосования [в первой части этой серии руководств](service-fabric-tutorial-create-dotnet-app.md), вы можете скачать его. В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Подготовка профиля публикации
После [создания приложения](service-fabric-tutorial-create-dotnet-app.md) и [развертывания приложения в Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md) вы можете настроить непрерывную интеграцию.  Сначала подготовьте профиль публикации для развертывания приложения в Team Services.  Профиль публикации следует настроить для целевого кластера, который был создан ранее.  Запустите Visual Studio и откройте существующий проект приложения Service Fabric.  Щелкните правой кнопкой мыши приложение в **обозревателе решений** и выберите **Опубликовать...**

Выберите в проекте приложения целевой профиль, который будет использоваться для рабочего процесса непрерывной интеграции, например "Облако".  Укажите конечную точку подключения кластера.  Установите флажок **Обновлять приложение**, чтобы ваше приложение обновлялось при каждом развертывании в Team Services.  Нажмите ссылку **Сохранить**, чтобы сохранить параметры профиля публикации, а затем нажмите кнопку **Отменить**, чтобы закрыть диалоговое окно.  

![Принудительная отправка профиля][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Поделитесь своим решением Visual Studio в новом репозитории Git Team Services
Поделитесь исходными файлами приложения в проекте команды в Team Services, чтобы создавать сборки.  

Создайте новый локальный репозиторий Git, выбрав **Добавить в систему управления версиями** -> **Git** в строке состояния в правом нижнем углу Visual Studio. 

В представлении **Принудительная отправка** в **Team Explorer** выберите **Опубликовать репозиторий Git** в разделе **Принудительная отправка в Visual Studio Team Services**.

![Принудительная отправка репозитория Git][push-git-repo]

Проверьте почту и выберите свою учетную запись в раскрывающемся списке **Домен Team Services**. Введите имя репозитория и выберите **Опубликовать репозиторий**.

![Принудительная отправка репозитория Git][publish-code]

При публикации репозитория в вашей учетной записи создается новый командный проект с тем же именем, что и у локального репозитория. Чтобы создать репозиторий в существующем командном проекте, нажмите кнопку **Дополнительно** рядом с именем **репозитория** и выберите командный проект. Код можно просматривать в Интернете, выбрав **Просмотреть на веб-сайте**.

## <a name="configure-continuous-delivery-with-vsts"></a>Настройка непрерывной поставки с помощью VSTS
Определение сборки Team Services описывает рабочий процесс, состоящий из набора шагов сборки, которые выполняются последовательно. Создайте определение сборки, который создает пакет приложения Service Fabric и другие артефакты, которые будут развернуты в кластер Service Fabric. Дополнительные сведения об [определениях сборок Team Services](https://www.visualstudio.com/docs/build/define/create). 

Определение выпуска Team Services описывает рабочий процесс развертывания пакета приложения в кластере. При совместном использовании определение сборки и определение выпуска выполняют весь рабочий процесс начиная с исходных файлов и заканчивая запуском приложения в кластере. Узнайте больше об [определениях выпуска](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)Team Services.

### <a name="create-a-build-definition"></a>Создание определения сборки
Откройте веб-браузер и перейдите к новому командному проекту в: [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting). 

Перейдите на вкладку **Сборка и выпуск**, выберите **Сборки** и щелкните **+Новое определение**.  В области **Выбор шаблона** выберите шаблон **Приложение Azure Service Fabric** и нажмите кнопку **Применить**. 

![Выбор шаблона сборки][select-build-template] 

В **задачи**, введите «Размещенные VS2017» в качестве **очереди агента**. 

![Выберите задачи][save-and-queue]

В разделе **триггеры**, непрерывной интеграции, задав **активировать состояние**.  Выберите **сохранить и очередь** запуск сборки вручную.  

![Выберите триггеры][save-and-queue2]

Основан на также триггер push или возврата. Чтобы проверить ход сборки, перейдите на вкладку **Сборки**.  Проверив, что сборка запускается успешно, создайте определение выпуска, которое развертывает приложение в кластер. 

### <a name="create-a-release-definition"></a>Создание определения выпуска  

Перейдите на вкладку **Сборка и выпуск**, выберите **Выпуски** и щелкните **+Новое определение**.  В **выберите шаблон**выберите **развертывание структуры службы Azure** шаблон из списка и затем **применить**.  

![Выберите шаблон выпуска][select-release-template]

Выберите **задачи**->**1 среды** и затем **+ создать** для добавления нового подключения кластера.

![Добавление подключения к кластеру][add-cluster-connection]

В **добавить новое подключение к службе структуры** просмотра выберите **на основе сертификатов** или **Azure Active Directory** проверки подлинности.  Укажите имя подключения «mysftestcluster» и к конечной точке кластера «tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000» (или конечную точку кластера, который выполняется развертывание). 

Для проверки подлинности на основе сертификатов, добавьте **отпечаток сертификата сервера** сертификата сервера, используемого для создания кластера.  В **сертификат клиента**, добавьте кодировку base-64 файла сертификата клиента. Всплывающее окно справки см. на этом поле, сведения о том, как получить кодированное представление сертификата, base-64. Кроме того, добавить **пароль** для сертификата.  При отсутствии отдельные клиентский сертификат можно использовать сертификат кластера или сервера. 

Учетные данные Azure Active Directory, добавьте **отпечаток сертификата сервера** сертификата сервера, используемый для создания кластера и учетные данные, необходимо использовать для подключения к кластеру в **имяпользователя** и **пароль** поля. 

Нажмите кнопку **добавить** сохранения подключения кластера.

Добавьте артефакт сборки в конвейер, чтобы определение выпуска можно найти выходные данные сборки. Выберите **конвейера** и **артефакты**->**+ добавить**.  В **источника (определение сборки)**, выберите определение сборки, созданной ранее.  Нажмите кнопку **добавить** сохранить артефакт сборки.

![Добавление артефакта][add-artifact]

Включение триггера непрерывного развертывания, чтобы выпуск создается автоматически после завершения построения. Щелкните значок артефакт, включить триггер и нажмите кнопку **Сохранить** сохранить определение выпуска.

![Включение триггера][enable-trigger]

Чтобы создать выпуск вручную, последовательно выберите **+Выпуск** -> **Создать выпуск** -> **Создать**.  Убедитесь, что развертывание выполнено успешно и приложение выполняется в кластере.  Откройте веб-браузер и перейдите к [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/обозреватель/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Обратите внимание на версию приложения, в данном случае "1.0.0.20170616.3". 

## <a name="commit-and-push-changes-trigger-a-release"></a>Фиксация и отправка изменений, создание выпуска
Чтобы проверить работу конвейера непрерывной интеграции, отправьте некоторые изменения кода в Team Services.    

При написании кода в Visual Studio изменения отслеживаются автоматически. Зафиксировать изменения в локальном репозитории Git можно, щелкнув значок ожидающих изменений (![Ожидает][pending]) в строке состояния в нижнем правом углу.

В представлении **Изменения** в Team Explorer добавьте сообщение с описанием обновления и зафиксируйте изменения.

![Фиксация всех изменений][changes]

Щелкните значок неопубликованных изменений в строке состояния (![Неопубликованные изменения][unpublished-changes]) или в представлении "Синхронизация" в Team Explorer. Выберите **Отправить изменения**, чтобы обновить код в Team Services/TFS.

![Отправка изменений][push]

При отправке изменений в Team Services автоматически запускается сборка.  После создания определения сборки автоматически создается выпуск и начинается обновление приложения в кластере.

Чтобы проверить ход сборки, перейдите на вкладку **Сборки** в **Team Explorer** в Visual Studio.  Проверив, что сборка запускается успешно, создайте определение выпуска, которое развертывает приложение в кластер.

Убедитесь, что развертывание выполнено успешно и приложение выполняется в кластере.  Откройте веб-браузер и перейдите к [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/обозреватель/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Обратите внимание на версию приложения, в этом случае — 1.0.0.20170815.3.

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Обновление приложения
Измените код в приложении.  Сохраните и зафиксируйте изменения, выполнив указанные выше действия.

После начала обновления вы сможете отслеживать ход обновления в Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

Обновление приложения может занять несколько минут. После завершения обновления будет запущена новая версия приложения.  В этом примере — 1.0.0.20170815.4.

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Добавление проекта в систему управления версиями
> * Создание определения сборки
> * Создание определения выпуска
> * Автоматическое развертывание и обновление приложения

Перейдите к следующему руководству:
> [!div class="nextstepaction"]
> [Настройка мониторинга и диагностики приложения](service-fabric-tutorial-monitoring-aspnet.md) 


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
