---
title: Развертывание и обновление микрослужб Azure локально | Документация Майкрософт
description: Установка локального кластера Service Fabric, развертывание в нем существующего приложения и обновление этого приложения.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 60a1f6a5-5478-46c0-80a8-18fe62da17a8
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/03/2017
ms.author: ryanwi
ms.openlocfilehash: 244a26df25e39fd4000a8a2692edd8205e354582
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643249"
---
# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Начало развертывания и обновления приложений в локальном кластере
Пакет SDK Service Fabric включает полную локальную среду разработки, которая позволяет быстро приступить к развертыванию приложений в локальном кластере и управлению ими. Следуя инструкциям в этой статье и используя Windows PowerShell, вы создадите локальный кластер, развернете в нем имеющееся приложение, а затем обновите его до новой версии.

> [!NOTE]
> В этой статье предполагается, что вы уже [настроили среду разработки](service-fabric-get-started.md).
> 
> 

## <a name="create-a-local-cluster"></a>Создание локального кластера
Кластер Service Fabric представляет собой набор аппаратных ресурсов, на которых можно развернуть приложение. Как правило, в состав кластера входит от пяти до нескольких тысяч компьютеров. Однако пакет SDK Service Fabric включает конфигурацию кластера, которая может выполняться на одном компьютере.

Важно понимать, что локальный кластер Service Fabric не является ни эмулятором, ни имитатором. Он выполняет тот же код платформы, что и кластеры, состоящие из нескольких компьютеров. Он отличается только тем, что выполняет на одном компьютере процессы платформы, которые обычно распределены между пятью компьютерами.

Пакет SDK можно установить двумя способами: с помощью сценария Windows PowerShell и с помощью приложения области уведомлений в диспетчере локального кластера. В этом руководстве используется сценарий PowerShell.

> [!NOTE]
> Если вы уже создали локальный кластер путем развертывания приложения из Visual Studio, можете пропустить этот раздел.
> 
> 

1. Откройте новое окно PowerShell от имени администратора.
2. Запустите сценарий установки кластера из папки пакета SDK:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```
   
    Настройка кластера занимает несколько минут. После завершения установки вы увидите примерно такой результат:
   
    ![Результат установки кластера][cluster-setup-success]
   
    Теперь вы можете попробовать развернуть приложение в кластере.

## <a name="deploy-an-application"></a>Развертывание приложения
Пакет SDK Service Fabric предлагает широкий выбор платформ и средств разработки для создания приложений. Если вы хотите научиться создавать приложения в Visual Studio, см. статью [Создание первого приложения Azure Service Fabric](service-fabric-create-your-first-application-in-visual-studio.md).

В этом руководстве вы будете использовать существующий пример приложения (которое называется WordCount), что позволит сосредоточиться на особенностях управления платформой, включая ее развертывание, мониторинг и обновление.

1. Откройте новое окно PowerShell от имени администратора.
2. Импортируйте модуль PowerShell для пакета SDK Service Fabric.
   
    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```
3. Создайте каталог для хранения приложения, которое будет скачано и развернуто, например С:\ServiceFabric.
   
    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```
4. [Загрузите приложение WordCount](http://aka.ms/servicefabric-wordcountapp) в созданную папку.  Примечание. Браузер Microsoft Edge сохранит файл с расширением *ZIP*.  Его необходимо изменить на расширение *SFPKG*.
5. Подключитесь к локальному кластеру:
   
    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```
6. Создайте приложение с помощью команды развертывания SDK. Для этого укажите имя пакета приложения и путь к нему.
   
    ```powershell  
   Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```
   
    Если все работает правильно, вы увидите такой результат:
   
    ![Развертывание приложения в локальном кластере][deploy-app-to-local-cluster]
7. Чтобы увидеть приложение в действии, откройте браузер и перейдите по адресу [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Вы должны увидеть следующее:
   
    ![Пользовательский интерфейс развернутого приложения][deployed-app-ui]
   
    Приложение WordCount простое. Оно состоит из клиентского кода JavaScript, создающего случайные пятизначные «слова», которые затем ретранслируются в приложение с помощью веб-API ASP.NET. Служба с отслеживанием состояния фиксирует количество подсчитанных слов. Все слова делятся на разделы по первому символу. Исходный код для приложения WordCount см. в статье [с классическими примерами для начала работы](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/WordCount).
   
    Приложение, которое мы развернули, содержит четыре раздела: слова, начинающиеся на буквы A–G, хранятся в первом разделе, слова на H–N — во втором разделе и т. д.

## <a name="view-application-details-and-status"></a>Просмотр сведений о приложении и его состояния
Когда приложение будет развернуто, некоторые сведения о нем можно просмотреть в PowerShell.

1. Запрос всех развернутых приложений в кластере:
   
    ```powershell
    Get-ServiceFabricApplication
    ```
   
    Если вы развернули только приложение WordCount, должен отобразиться примерно такой результат:
   
    ![Запрос всех развернутых приложений в PowerShell][ps-getsfapp]
2. Перейдите на следующий уровень, запросив набор служб, включенных в приложение WordCount.
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Список служб для приложения в PowerShell][ps-getsfsvc]
   
    Приложение состоит из двух служб — веб-интерфейса и службы с отслеживанием состояния, которая управляет словами.
3. Наконец, просмотрите список разделов для WordCountService.
   
    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```
   
    ![Просмотр разделов службы в PowerShell][ps-getsfpartitions]
   
    Набор использованных команд (например, все команды PowerShell для Service Fabric) доступен для любого локального или удаленного кластера, к которому вы можете подключиться.
   
    Чтобы визуально контролировать взаимодействие с кластером, используйте веб-инструмент Service Fabric Explorer. Для этого в браузере перейдите по адресу [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
   
    ![Просмотр сведений о приложении в обозревателе Service Fabric][sfx-service-overview]
   
   > [!NOTE]
   > Дополнительные сведения о Service Fabric Explorer см. в статье [Визуализация кластера с помощью обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md).
   > 
   > 

## <a name="upgrade-an-application"></a>Обновление приложения
Платформа Service Fabric предоставляет обновления без простоя. Для этого она отслеживает работоспособность приложения по мере его развертывания в кластере. Выполните обновление приложения WordCount.

Новая версия приложения теперь будет подсчитывать слова, начинающиеся с гласных. По мере того как развертывается обновление, мы можем увидеть в поведении приложения два изменения. Во-первых, скорость увеличения счетчика должна уменьшиться, так как подсчитывается меньшее количество слов. Во-вторых, так как первый раздел содержит две гласные буквы (A и E), а остальные разделы только по одной, показатели счетчика первого раздела со временем начнут опережать показатели других счетчиков.

1. [Скачайте пакет WordCount версии 2](http://aka.ms/servicefabric-wordcountappv2) в то же расположение, куда был скачан пакет версии 1.
2. Вернитесь в окно PowerShell и запустите команду обновления пакета SDK, чтобы зарегистрировать новую версию в кластере. Начнется обновление приложения fabric:/WordCount.
   
    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```
   
    Когда начнется обновление, в окне PowerShell должен отобразиться примерно такой результат:
   
    ![Ход обновления в PowerShell][ps-appupgradeprogress]
3. Если обновление будет продолжаться, вам может быть проще отслеживать его состояние из обозревателя Service Fabric. Запустите окно браузера и перейдите по адресу [http://localhost:19080/Explorer](http://localhost:19080/Explorer). В дереве слева разверните узел **Приложения** и последовательно выберите **WordCount** и **fabric:/WordCount**. На вкладке с основными сведениями отобразится состояние обновления во всех доменах обновления кластера.
   
    ![Ход обновления в обозревателе Service Fabric][sfx-upgradeprogress]
   
    При переходе процесса обновления от домена к домену выполняются проверки работоспособности и правильного поведения приложения; обновление продолжается только после этого.
4. Если вы повторно запустите предыдущий запрос для набора служб в приложении fabric:/WordCount, вы заметите, что версия WordCountService изменилась, а версия WordCountWebService — нет.
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Запрос служб приложений после обновления][ps-getsfsvc-postupgrade]
   
    Этот пример демонстрирует, как платформа Service Fabric управляет обновлениями: они применяются только к набору служб (либо к пакетам кода или конфигурации в этих службах), которые были изменены. Таким образом, процесс обновления становится быстрым и надежным.
5. Теперь вернитесь в браузер, чтобы наблюдать за поведением новой версии приложения. Как и ожидалось, показания счетчика увеличиваются медленнее, а объем первого раздела немного превышает объем остальных разделов.
   
    ![Просмотр новой версии приложения в браузере][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Очистка.
Завершая работу, важно помнить, что локальный кластер работает по-настоящему. Приложения будут продолжать работать в фоновом режиме, пока вы не удалите их.  В зависимости от свойств приложения могут использовать разный объем ресурсов компьютера. Доступно несколько вариантов управления кластером и приложениями.

1. Чтобы удалить определенное приложение и все его данные, выполните следующую команду:
   
    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```
   
    Кроме того, приложение можно удалить в меню **Действия** Service Fabric Explorer или в контекстном меню в представлении списка приложений в области слева.
   
    ![Удаление приложения в обозревателе Service Fabric][sfe-delete-application]
2. Удалив приложение из кластера, можно отменить регистрацию приложения WordCount версии 1.0.0 и 2.0.0. Это действие удаляет пакеты приложения, в том числе их коды и конфигурацию, из хранилища образов кластера.
   
    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```
   
    Вы можете также **отменить подготовку типа** для приложения в Service Fabric Explorer.
3. Чтобы завершить работу кластера, но сохранить данные приложения и трассировки, в приложении области уведомлений выберите команду **Stop Local Cluster** (Остановить локальный кластер).
4. Чтобы полностью удалить кластер, в приложении области уведомлений щелкните **Remove Local Cluster** (Удалить локальный кластер). Выбор этого параметра приведет к медленному развертыванию при последующем нажатии клавиши F5 в Visual Studio. Удаляйте локальный кластер только в том случае, если вы не собираетесь использовать его в течение некоторого времени или если необходимо освободить ресурсы.

## <a name="one-node-and-five-node-cluster-mode"></a>Режимы кластера с одним и с пятью узлами
При разработке приложений часто бывает необходимо быстро написать, изменить код или выполнить его отладку. Чтобы оптимизировать этот процесс, можно выбрать подходящий режим работы локального кластера: с одним узлом или с пятью узлами. Каждый из этих режимов имеет свои преимущества. В режиме кластера с пятью узлами вы можете работать с реальным кластером. Вы можете тестировать сценарии отработки отказа и работать с несколькими экземплярами и репликами служб. Режим кластера с одним узлом позволяет быстро развернуть и зарегистрировать службы, чтобы проверить код в среде выполнения Service Fabric.

Режимы кластера с одним узлом и с пятью узлами не являются эмуляторами или имитаторами. Кластер локальной разработки выполняет тот же код платформы, что и кластеры, состоящие из нескольких компьютеров.

> [!WARNING]
> При изменении режима текущий кластер удаляется из системы и создается новый. Данные, хранящиеся в кластере, также будут удалены.
> 
> 

Чтобы изменить режим на кластер с одним узлом, выберите **Switch Cluster Mode** (Переключение режима кластера) в диспетчере локального кластера Service Fabric.

![Переключение режима кластера][switch-cluster-mode]

Режим кластера также можно изменить с помощью PowerShell.

1. Откройте новое окно PowerShell от имени администратора.
2. Запустите сценарий установки кластера из папки пакета SDK:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```
   
    Настройка кластера занимает несколько минут. После завершения установки вы увидите примерно такой результат:
   
    ![Результат установки кластера][cluster-setup-success-1-node]

## <a name="next-steps"></a>Дополнительная информация
* Теперь, когда вы развернули и обновили предварительно собранные приложения, вы можете [выполнить сборку своего приложения в Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
* Все описанные в этой статье действия, выполняемые в локальном кластере, можно также выполнять в [кластере Azure](service-fabric-cluster-creation-via-portal.md) .
* В этой статье описано простое обновление. Чтобы узнать больше о преимуществах и гибкости обновлений Service Fabric, см. [документацию по обновлению](service-fabric-application-upgrade.md).

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png
