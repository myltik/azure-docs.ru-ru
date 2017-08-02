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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: c528974951d3b4a83111cb92b931810a91f660f4
ms.contentlocale: ru-ru
ms.lasthandoff: 07/15/2017

---

# <a name="deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Развертывание приложения с непрерывной интеграцией и развертыванием в кластере Service Fabric
В этом руководстве описано, как настроить непрерывную интеграцию и развертывание для приложения Azure Service Fabric с помощью Visual Studio Team Services.  Вам потребуется приложение Service Fabric. В качестве примера используется приложение, созданное в разделе [Создание приложения .NET](service-fabric-tutorial-create-dotnet-app.md).

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Добавление проекта в систему управления версиями
> * Создание определения сборки в Team Services
> * Создание определения выпуска в Team Services
> * Автоматическое развертывание и обновление приложения

Руководство разбито на три статьи, эта статья — третья в серии.

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Установите Visual Studio 2017](https://www.visualstudio.com/), а также рабочие нагрузки **разработка Azure** и **ASP.NET и веб-разработка**.
- [Установка пакета SDK для Service Fabric](service-fabric-get-started.md)
- Создайте приложение Service Fabric, например с помощью [этого руководства](service-fabric-tutorial-create-dotnet-app.md). 
- Создайте кластер Service Fabric с Windows, например с помощью [этого руководства](service-fabric-tutorial-create-cluster-azure-ps.md).
- Создайте [учетную запись Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

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
Используйте встроенный мастер в Visual Studio для настройки непрерывной поставки с помощью VSTS.

1. Щелкните правой кнопкой мыши проект приложения **MyApplication** в **обозревателе решений**, а затем выберите **Добавить** -> **Непрерывная поставка с помощью VSTS**. Откроется диалоговое окно для ввода требуемой конфигурации.

    ![Непрерывная поставка с помощью VSTS][continuous-delivery-with-VSTS]

    Значения параметров **Учетная запись**, **Проект** и **Репозиторий Git** заполняются автоматически.

2. Выберите **Размещается в VS2017** в качестве значения параметра "Очередь агента".

3. Выберите **Создать подключение** в раскрывающемся списке **Подключение кластера**. Откроется веб-сайт для настройки конечной точки службы в VSTS. 

4. Переведите фокус на окно браузера и выберите **Создать конечную точку службы** -> **Service Fabric**.

    ![Новая конечная точка службы][new-service-endpoint]

    Появится диалоговое окно для добавления нового подключения Service Fabric.
    
5. Выберите **На основе сертификата** и закройте диалоговое окно:

    ![Диалоговое окно создания конечной точки службы][new-service-endpoint-dialog]

    1. Введите **Имя подключения**.
    2. Введите конечную точку управления кластера в поле **Конечная точка кластера** (например, "tcp://mycluster.eastus.cloudapp.azure.com:19000"). Укажите "tcp://" в качестве протокола.  Порт конечной точки управления по умолчанию — 19000.
    3. Введите **Отпечаток сертификата сервера**.  Отпечаток можно получить, открыв Service Fabric Explorer для кластера (https://mycluster.eastus.cloudapp.azure.com:19080).
        - Выберите узел **Кластер** в дереве и вкладку **Манифест** в области справа.
        - Найдите элемент **<ServerCertificate>** в XML-файле и скопируйте содержимое атрибута **X509FindValue**.
    4. В поле **Сертификат клиента** введите сертификат клиента в виде строки в кодировке Base64. Это необходимо для установки сертификата на агенте сборки:
        - Убедитесь, что у вас есть сертификат в виде PFX-файла.
        - Для вывода PXF-файла в виде строки в кодировке Base64 в буфер обмена выполните следующую команду PowerShell: `[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(C:\path\to\certificate.pfx)) | clip`
        - Вставьте (CTRL+V) значение из буфера обмена в поле в диалоговом окне.
    5. Введите **Пароль сертификата** в поле **Пароль** и нажмите кнопку **ОК**.

6. В Visual Studio выберите **<Refresh>** в поле **Подключение кластера** в диалоговом окне **Непрерывная поставка с помощью VSTS**. Созданная конечная точка кластера должна появиться в раскрывающемся списке.

7. Выберите имена сборки и определения выпуска по умолчанию или измените имена, указанные в диалоговом окне. Закончив, нажмите кнопку **OK**.

Через некоторое время в Visual Studio появится диалоговое окно с вопросом, следует ли открыть определение сборки и выпуска в браузере. Это можно сделать для проверки того, как они настроены. Но для выполнения действий, описанных в этом руководстве, это не требуется.

- Определение сборки Team Services описывает рабочий процесс, состоящий из набора шагов сборки, которые выполняются последовательно. Создайте определение сборки, который создает пакет приложения Service Fabric и другие артефакты, которые будут развернуты в кластер Service Fabric. Дополнительные сведения об [определениях сборок Team Services](https://www.visualstudio.com/docs/build/define/create).
- Определение выпуска Team Services описывает рабочий процесс развертывания пакета приложения в кластере. При совместном использовании определение сборки и определение выпуска выполняют весь рабочий процесс начиная с исходных файлов и заканчивая запуском приложения в кластере. Узнайте больше об [определениях выпуска](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)Team Services.

## <a name="commit-and-push-changes-trigger-a-release"></a>Фиксация и отправка изменений, создание выпуска
Чтобы проверить работу конвейера непрерывной интеграции, отправьте некоторые изменения кода в Team Services.    

При написании кода в Visual Studio изменения отслеживаются автоматически. Зафиксировать изменения в локальном репозитории Git можно, щелкнув значок ожидающих изменений (![Ожидает][pending]) в строке состояния в нижнем правом углу.

В представлении **Изменения** в Team Explorer добавьте сообщение с описанием обновления и зафиксируйте изменения.

![Фиксация всех изменений][changes]

Щелкните значок неопубликованных изменений в строке состояния (![Неопубликованные изменения][unpublished-changes]) или в представлении "Синхронизация" в Team Explorer. Выберите **Отправить изменения**, чтобы обновить код в Team Services/TFS.

![Отправка изменений][push]

При отправке изменений в Team Services автоматически запускается сборка.  После создания определения сборки автоматически создается выпуск и начинается обновление приложения в кластере.

Чтобы проверить ход сборки, перейдите на вкладку **Сборки** в **Team Explorer** в Visual Studio.  Проверив, что сборка запускается успешно, создайте определение выпуска, которое развертывает приложение в кластер.

Убедитесь, что развертывание выполнено успешно и приложение выполняется в кластере.  Откройте браузер и перейдите по ссылке [http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/).  Обратите внимание на версию приложения, в данном случае "1.0.0.20170616.3".

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Обновление приложения
Измените код в приложении.  Сохраните и зафиксируйте изменения, выполнив указанные выше действия.

После начала обновления вы сможете отслеживать ход обновления в Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

Обновление приложения может занять несколько минут. После завершения обновления будет запущена новая версия приложения.  В данном случае "1.0.0.20170616.4".

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Добавление проекта в систему управления версиями
> * Создание определения сборки
> * Создание определения выпуска
> * Автоматическое развертывание и обновление приложения

Теперь, когда вы развернули приложение и настроили непрерывную интеграцию, попробуйте сделать следующее:
- [Обновление приложения](service-fabric-application-upgrade.md)
- [Тестирование приложения](service-fabric-testability-overview.md) 
- [Мониторинг и диагностика](service-fabric-diagnostics-overview.md)


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[add-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddTask.png
[new-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewTask.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
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
