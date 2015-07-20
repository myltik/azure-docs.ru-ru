<properties
   pageTitle="Визуализация кластера с помощью обозревателя Service Fabric"
   description="Обозреватель Service Fabric — это полезное средство графического пользовательского интерфейса для изучения облачных приложений и узлов в кластере Microsoft Azure Service Fabric и управления ими."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/20/2015"
   ms.author="jesseb"/>

# Визуализация кластера с помощью обозревателя Service Fabric

Обозреватель Service Fabric — это средство визуализации для изучения облачных приложений и узлов в кластере Microsoft Azure Service Fabric и управления ими. Обозреватель Service Fabric может подключаться как к локальным кластерам, так и к кластерам Azure. Сведения о командлетах PowerShell Service Fabric см. в подразделе **Дальнейшие действия**.

> [AZURE.NOTE]Создание кластеров Service Fabric в Azure пока не доступно.

## Общие сведения об обозревателе Service Fabric

Убедитесь, что ваша локальная среда разработки настроена согласно инструкциям в статье о [настройке среды разработки Service Fabric](service-fabric-get-started.md).

Запустите обозреватель Service Fabric по локальному пути установки (%Program Files%\\Microsoft SDKs\\Service Fabric\\Tools\\ServiceFabricExplorer\\ServiceFabricExplorer.exe). Средство автоматически подключится к локальному кластеру разработки при его наличии. Оно отображает следующие сведения о кластере:

- приложения, выполняемые на кластере;
- сведения об узлах кластера;
- события работоспособности в приложениях и на узлах;
- нагрузка на приложения в кластере;
- мониторинг состояния обновления приложения.

![Визуальное представление кластера Service Fabric и развернутых приложений][servicefabricexplorer]

Один из важнейших элементов визуализации — схема кластера, отображаемая на панели мониторинга кластера (например, ее можно увидеть, щелкнув **Onebox/Local cluster**). Схема кластера отображает набор доменов обновления и доменов сбоев, а также сопоставление между узлами и доменами. Основные понятия Service Fabric см. в [техническом обзоре Service Fabric](service-fabric-technical-overview.md).

![Схема кластера принадлежность каждого узла к тем или иным доменам обновления или сбоев.][clustermap]


## Просмотр приложений и служб

Обозреватель Service Fabric позволяет просматривать приложения, выполняемые в кластере. Разверните **Представление приложения**, чтобы просмотреть подробные сведения о приложениях, службах, разделах и репликах.

На диаграмме ниже показано, что у приложения с именем **"fabric:/Stateful1Application"** есть одна служба без отслеживания состояния с именем **"fabric:/Stateful1Application/MyFrontEnd"** и одна служба с отслеживанием состояния с именем **"fabric:/Stateful1Application/Stateful1"**. Служба без отслеживания состояния содержит один раздел с одной репликой, выполняемой на узле **Node.4**. Служба с отслеживанием состояния содержит два раздела по три реплики в каждом, выполняемые на нескольких узлах.

![Представление приложений, выполняемых в кластере Service Fabric][applicationview]

Выберите приложение, службу, раздел или реплику, чтобы просмотреть подробные сведения о соответствующей сущности. На схеме ниже показана панель мониторинга работоспособности реплики службы для одной из основных реплик службы с отслеживанием состояния. На ней показаны ее роль, узел, на котором она выполняется, прослушиваемый адрес, расположение ее файлов на диске и связанные события работоспособности.

![Подробные сведения о реплике Service Fabric][replicadetails]


## Подключение к удаленному кластеру Service Fabric

Для просмотра удаленного кластера Service Fabric нажмите **Подключение**, чтобы вызвать диалоговое окно **Подключение к кластеру Service Fabric**. Укажите **конечную точку Service Fabric** для кластера и нажмите кнопку **Подключиться**. Конечная точка Service Fabric, как правило, представляет собой общедоступное имя службы кластера с прослушиванием порта 19000.

![Настройка подключения к удаленному кластеру Service Fabric][connecttocluster]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

- [Обзор Testability](service-fabric-testability-overview.md).
- [Управление приложениями Service Fabric в Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Развертывание приложений Service Fabric с помощью PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[applicationview]: ./media/service-fabric-visualizing-your-cluster/applicationview.png
[clustermap]: ./media/service-fabric-visualizing-your-cluster/clustermap.png
[connecttocluster]: ./media/service-fabric-visualizing-your-cluster/connecttocluster.png
[replicadetails]: ./media/service-fabric-visualizing-your-cluster/replicadetails.png
[servicefabricexplorer]: ./media/service-fabric-visualizing-your-cluster/servicefabricexplorer.png
 

<!---HONumber=July15_HO2-->