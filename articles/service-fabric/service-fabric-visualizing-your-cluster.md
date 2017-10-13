---
title: "Визуализация кластера с помощью Service Fabric Explorer | Документация Майкрософт"
description: "Обозреватель Service Fabric — это веб-средство для изучения облачных приложений и узлов в кластере Microsoft Azure Service Fabric и управления ими."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: ryanwi
ms.openlocfilehash: 965ffc0f8cec26cccbe6e6459731afc234111f4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Визуализация кластера с помощью обозревателя Service Fabric
Обозреватель Service Fabric — это веб-средство для изучения приложений и узлов в кластере Azure Service Fabric и управления ими. Обозреватель Service Fabric размещается непосредственно в кластере, поэтому он доступен всегда, независимо от того, где запущен кластер.

## <a name="video-tutorial"></a>Видеоруководство

Посмотрите следующий видеоролик от Академии Microsoft Virtual Academy, в котором показано, как использовать Service Fabric Explorer:

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="connect-to-service-fabric-explorer"></a>Подключение к обозревателю Service Fabric
Если вы следовали указаниям по [подготовке среды разработки](service-fabric-get-started.md), вы можете запустить Service Fabric Explorer на локальном кластере, перейдя по адресу http://localhost:19080/Explorer.

## <a name="understand-the-service-fabric-explorer-layout"></a>Основные сведения о структуре обозревателя Service Fabric
Перейти к обозревателю Service Fabric можно с помощью дерева в левой части окна. В корне дерева на панели мониторинга кластера представлены общие сведения о кластере, включая общие сведения о приложении и работоспособности узла кластера.

![Панель мониторинга кластера обозревателя Service Fabric][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Просмотр макета кластера
Узлы в кластере Service Fabric размещаются в двухмерной сетке доменов сбоя и обновления, что обеспечивает доступность приложений при сбоях оборудования и обновлении приложений. План текущего кластера можно просмотреть на схеме кластера.

![Схема кластера в обозревателе Service Fabric][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Просмотр приложений и услуг
Кластер содержит два поддерева: одно для приложений и другое для узлов.

Представление приложений позволяет перемещаться по логической иерархии Service Fabric: приложениям, службам, разделам и репликам.

В примере ниже приложение **MyApp** состоит из двух служб: **MyStatefulService** и **WebSvcService**. Так как **MyStatefulService** — служба с отслеживанием состояния, она включает раздел с одной основной и двумя вторичными репликами. Напротив, WebSvcService — служба без сохранения состояния и содержит единственный экземпляр.

![Представление приложений обозревателя Service Fabric][sfx-application-tree]

На каждом уровне дерева на основной панели отображаются нужные сведения об элементе. Например, для конкретной службы вы увидите состояние работоспособности и версию.

![Панель основных сведений обозревателя Service Fabric][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Просмотр узлов кластера
В представлении "Узлы" отображается физическая структура кластера. Для каждого узла можно просмотреть, какие приложения были развернуты на этом узле и какие реплики запущены в настоящее время.

## <a name="actions"></a>Действия
Обозреватель Service Fabric позволяет быстро выполнять действия с узлами, приложениями и службами в кластере.

Например, чтобы удалить экземпляр приложения, выберите приложение из дерева в левой части и выберите **Действия** > **Удалить приложение**.

![Удаление приложения в обозревателе Service Fabric][sfx-delete-application]

> [!TIP]
> Те же действия можно выполнить, щелкнув знак многоточия (...) рядом с каждым элементом.
>
>

В следующей таблице перечислены действия, доступные для каждой сущности.

| **Сущность** | **Действие** | **Описание** |
| --- | --- | --- |
| Тип приложения |Отмена подготовки типа |Удаление пакета приложения из хранилища образов кластера. Сначала должны быть удалены все приложения этого типа. |
| Приложение |Удалить приложение |Удаление приложения, включая все его службы и их состояния (при наличии). |
| служба |Удаление службы |Удаление службы и ее состояния (при наличии). |
| Узел |Активировать |Активация узла. |
| Узел | Деактивация (пауза) | Приостановка узла в его текущем состоянии. Службы продолжают работу, но Service Fabric заранее не перемещает какие-либо объекты в узел или из него, если это не требуется для предотвращения несоответствия данных или сбоя. Обычно это действие используется для включения отладки служб на определенном узле для предотвращения их перемещения во время проверки. | |
| Узел | Деактивация (перезапуск) | Безопасное перемещение всех служб в памяти за пределы узла и закрытие постоянных служб. Обычно используется, когда процессы узла или компьютеры необходимо перезапустить. | |
| Узел | Деактивация (удаление данных) | Безопасное закрытие всех служб, запущенных на узле после создания достаточного количества запасных реплик. Обычно используется, когда узел (или хотя бы его хранилище) окончательно выводится из эксплуатации. | |
| Узел | Удаление состояния узла | Удаление сведений о репликах узла из кластера. Обычно используется, когда узел, вышедший из строя, уже нельзя восстановить. | |
| Узел | Перезагрузить | Моделирование сбоя узла путем перезапуска узла. Дополнительные сведения см. [здесь](/powershell/module/servicefabric/restart-servicefabricnode?view=azureservicefabricps). | |

Так как многие действия являются необратимыми, вам будет предложено подтвердить свое намерение перед выполнением действия.

> [!TIP]
> Каждое действие, которое может быть выполнено с помощью обозревателя Service Fabric, также можно выполнить с помощью PowerShell или API REST, дающего выполнить автоматизацию.
>
>

Также можно использовать Service Fabric Explorer для создания экземпляров приложения для определенного типа и версии приложения. В представлении в виде дерева выберите тип приложения, затем в области справа щелкните ссылку **Create app instance** (Создать экземпляр приложения) рядом с необходимой версией.

![Создание экземпляра приложения в Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> В настоящее время экземпляры приложения, созданные с помощью Service Fabric Explorer, не могут быть параметризованы. При их создании используются значения параметров по умолчанию.
>
>

## <a name="connect-to-a-remote-service-fabric-cluster"></a>Подключение к удаленному кластеру Service Fabric
Если вы знаете конечную точку кластера и имеете достаточно разрешений, вы можете получить доступ к Service Fabric Explorer из любого браузера. Так происходит потому, что Service Fabric Explorer — это просто еще одна служба, которая выполняется в кластере.

### <a name="discover-the-service-fabric-explorer-endpoint-for-a-remote-cluster"></a>Обнаружение конечной точки обозревателя Service Fabric для удаленного кластера
Чтобы подключиться к Service Fabric Explorer для заданного кластера, просто перейдите в браузере по адресу:

http://&lt;конечная_точка_кластера&gt;:19080/Explorer.

Полный URL-адрес для кластеров Azure доступен также на панели основных компонентов кластера портала Azure.

### <a name="connect-to-a-secure-cluster"></a>Безопасное подключение к кластеру
Вы можете управлять клиентским доступом к кластеру Service Fabric с помощью сертификатов или Azure Active Directory (AAD).

При попытке подключиться к Service Fabric Explorer в защищенном кластере вам понадобится либо представить сертификат клиента, либо войти с помощью AAD (в зависимости от конфигурации кластера).

## <a name="next-steps"></a>Дальнейшие действия
* [Обзор Testability](service-fabric-testability-overview.md)
* [Управление приложениями Service Fabric в Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Развертывание приложений Service Fabric с помощью PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png
