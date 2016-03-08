<properties
   pageTitle="Начало развертывания и обновления приложений в локальном кластере | Microsoft Azure"
   description="Установка локального кластера Service Fabric, развертывание в нем существующего приложения и обновление этого приложения."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/26/2016"
   ms.author="seanmck"/>

# Начало развертывания и обновления приложений в локальном кластере
Пакет SDK Service Fabric включает полную локальную среду разработки, которая позволяет быстро приступить к развертыванию приложений в локальном кластере и управлению ими. Следуя инструкциям в этой статье и используя Windows PowerShell, вы создадите локальный кластер, развернете в нем существующее приложение, а затем обновите приложение до новой версии.

> [AZURE.NOTE] В этой статье предполагается, что вы уже [настроили среду разработки](service-fabric-get-started.md).

## Создание локального кластера
Кластер Service Fabric представляет собой набор аппаратных ресурсов, на которых можно развернуть приложение. Как правило, в состав кластера входит от пяти до нескольких тысяч компьютеров. Однако пакет SDK Service Fabric включает конфигурацию кластера, которая может выполняться на одном компьютере.

Важно понимать, что локальный кластер Service Fabric не является ни эмулятором, ни имитатором. Он выполняет тот же код платформы, что и кластеры, состоящие из нескольких компьютеров. Он отличается только тем, что выполняет на одном компьютере процессы платформы, которые обычно распределены между пятью компьютерами.

Пакет SDK можно установить двумя способами: с помощью сценария Windows PowerShell и с помощью приложения области уведомлений в диспетчере локального кластера. В этом руководстве используется сценарий PowerShell.

> [AZURE.NOTE] Если вы уже создали локальный кластер путем развертывания приложения из Visual Studio, можете пропустить этот раздел.


1. Откройте новое окно PowerShell от имени администратора.

2. Запустите сценарий установки кластера из папки пакета SDK:

	```powershell
	& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
	```

    Настройка кластера занимает несколько минут. После завершения установки вы увидите такой результат:

    ![Результат установки кластера][cluster-setup-success]

    Теперь вы можете попробовать развернуть приложение в кластере.

## Развертывание приложения
Пакет SDK Service Fabric предлагает широкий выбор платформ и средств разработки для создания приложений. Если вы хотите научиться создавать приложения в Visual Studio, см. статью [Создание первого приложения Service Fabric в Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

В этом руководстве мы будем использовать существующий пример приложения (которое называется WordCount), что позволит нам сосредоточиться на особенностях управления платформой, включая ее развертывание, мониторинг и обновление.


1. Откройте новое окно PowerShell от имени администратора.

2. Импортируйте модуль PowerShell для пакета SDK Service Fabric.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. Создайте каталог для хранения приложения, которое будет загружено и развернуто, например С:\\ServiceFabric.

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. [Загрузите приложение WordCount](http://aka.ms/servicefabric-wordcountapp) в созданную папку.

5. Подключитесь к локальному кластеру:

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Вызовите команду развертывания SDK, чтобы создать новое приложение. Для этого укажите имя и путь к пакету приложения.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Если все работает правильно, вы увидите такой результат:

    ![Развертывание приложения в локальном кластере][deploy-app-to-local-cluster]

7. Чтобы увидеть приложение в действии, откройте браузер и перейдите по адресу [http://localhost:8081/wordcount/index](http://localhost:8081/wordcount/index). Вы увидите нечто вроде этого:

    ![Пользовательский интерфейс развернутого приложения][deployed-app-ui]

    Приложение WordCount очень простое. Оно состоит из клиентского кода JavaScript, создающего случайные пятизначные «слова», которые затем ретранслируются в приложение с помощью веб-API ASP.NET. Служба с отслеживанием состояния фиксирует количество подсчитанных слов. Все слова делятся на разделы по первому символу.

    Приложение, которое мы развернули, содержит четыре раздела: слова, начинающиеся на буквы A–G, хранятся в первом разделе, слова на H–N — во втором разделе и т. д.

## Просмотр сведений о приложении и его состояния
Когда приложение будет развернуто, некоторые сведения о нем можно просмотреть в PowerShell.

1. Запрос всех развернутых приложений в кластере:

    ```powershell
    Get-ServiceFabricApplication
    ```

    Если вы развернули только приложение WordCount, вы увидите примерно следующие результаты:

    ![Запрос всех развернутых приложений в PowerShell][ps-getsfapp]

2. Перейдите на следующий уровень, запросив набор служб, включенных в приложение WordCount.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Список служб для приложения в PowerShell][ps-getsfsvc]

    Обратите внимание, что приложение состоит из двух служб — веб-интерфейса и службы с отслеживанием состояния, которая управляет словами.

3. Наконец, просмотрите список разделов для WordCountService:

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![Просмотр разделов службы в PowerShell][ps-getsfpartitions]

    Набор использованных команд (например, все команды PowerShell для Service Fabric) доступен для любого локального или удаленного кластера, к которому вы можете подключиться.

    Чтобы визуально контролировать взаимодействие с кластером, используйте веб-обозреватель Service Fabric. Для этого откройте в браузере адрес [http://localhost:19080/Explorer](http://localhost:19080/Explorer).

    ![Просмотр сведений о приложении в обозревателе Service Fabric][sfx-service-overview]

    > [AZURE.NOTE] Дополнительные сведения об обозревателе Service Fabric см. в статье [Визуализация кластера с помощью обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md).

## Обновление приложения
Платформа Service Fabric предоставляет обновления без простоя. Для этого она отслеживает работоспособность приложения по мере его развертывания в кластере. Выполним простое обновление приложения WordCount.

Новая версия приложения теперь будет подсчитывать слова, начинающиеся с гласных. По мере того как развертывается обновление, мы можем увидеть в поведении приложения два изменения. Во-первых, скорость увеличения счетчика должна уменьшиться, так как подсчитывается меньшее количество слов. Во-вторых, так как первый раздел содержит две гласные буквы (A и E), а остальные разделы только по одной, показатели счетчика первого раздела со временем начнут опережать показатели других счетчиков.

1. [Загрузите пакет WordCount v2](http://aka.ms/servicefabric-wordcountappv2) в то же расположение, в которое вы загрузили пакет v1.

2. Вернитесь в окно PowerShell и запустите команду обновления пакета SDK, чтобы зарегистрировать новую версию в кластере. Начнется обновление приложения fabric:/WordCount.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Когда начнется обновление, в окне PowerShell вы увидите приблизительно такой результат:

    ![Ход обновления в PowerShell][ps-appupgradeprogress]

3. Если обновление будет продолжаться, вам может быть проще отслеживать его состояние из обозревателя Service Fabric. Откройте окно браузера и перейдите по адресу [http://localhost:19080/Explorer](http://localhost:19080/Explorer). В дереве слева щелкните раздел **Приложения**, затем выберите раздел **Выполняемые обновления**.

    ![Ход обновления в обозревателе Service Fabric][sfx-upgradeprogress]

    Обратите внимание, что индикатор «Обновления выполняются» отображает состояние обновления в доменах обновления кластера. При переходе процесса обновления от домена к домену выполняются проверки работоспособности и правильного поведения приложения; обновление продолжается только после этого.

4. Если вы повторно запустите предыдущий запрос для набора служб в приложении fabric:/WordCount, вы заметите, что версия WordCountService изменилась, а версия WordCountWebService — нет:

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Запрос служб приложений после обновления][ps-getsfsvc-postupgrade]

    Этот результат демонстрирует, как платформа Service Fabric управляет обновлениями: они применяются только к набору служб (либо к пакетам кода или конфигурации в этих службах), которые были изменены. Таким образом, процесс обновления становится быстрым и надежным.

5. Теперь вернитесь в браузер, чтобы наблюдать за поведением новой версии приложения. Как и ожидалось, показания счетчика увеличиваются медленнее, а объем первого раздела немного превышает объем остальных разделов.

    ![Просмотр новой версии приложения в браузере][deployed-app-ui-v2]

## Дальнейшие действия
- Теперь, когда вы развернули и обновили предварительно собранные приложения, вы можете [выполнить сборку своего приложения в Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
- Все описанные в этой статье действия, выполняемые в локальном кластере, можно также выполнять в [кластере Azure](service-fabric-cluster-creation-via-portal.md).
- В этой статье описано самое простое обновление. Чтобы узнать больше о преимуществах и гибкости обновлений Service Fabric, см. [документацию по обновлению](service-fabric-application-upgrade.md).

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

<!---HONumber=AcomDC_0302_2016-->