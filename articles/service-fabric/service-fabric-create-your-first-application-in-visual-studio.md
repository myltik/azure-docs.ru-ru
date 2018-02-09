---
title: "Создание надежной службы Azure Service Fabric с помощью C#"
description: "Создание, развертывание и отладка приложения надежной службы, созданного в Service Fabric с помощью Visual Studio."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 2ecb8f8068043936d00f2c9752666490137414e3
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>Создание первого приложения надежных служб Service Fabric с отслеживанием состояния на C#

Узнайте, как развертывать приложение Service Fabric для .NET в Windows за несколько минут. По завершении у вас будет локальный кластер, выполняющийся с приложением надежной службы.

## <a name="prerequisites"></a>предварительным требованиям

Перед началом работы [настройте среду разработки](service-fabric-get-started.md). Это включает установку пакета SDK для Service Fabric и Visual Studio 2017 или 2015.

## <a name="create-the-application"></a>Создание приложения

Запустите Visual Studio от имени **администратора**.

Создайте проект, нажав клавиши `CTRL`+`SHIFT`+`N`.

В диалоговом окне **Создать проект** выберите **Облако > Приложение Service Fabric**.

Укажите имя приложения **MyApplication** и нажмите кнопку **ОК**.

   
![Диалоговое окно "Новый проект" в Visual Studio][1]

Вы можете создать любой тип приложения Service Fabric из следующего диалогового окна. В рамках этого краткого руководства выберите **Служба с отслеживанием состояния**.

Присвойте службе имя **MyStatefulService** и щелкните **ОК**.

![Диалоговое окно "Новая служба" в Visual Studio][2]


Visual Studio создаст проект приложения и проект службы с отслеживанием состояния, затем отобразит их в обозревателе решений.

![Обозреватель решений после создания приложения и службы с отслеживанием состояния][3]

Проект приложения (**MyApplication**) не содержит никакой код. Он только ссылается на набор проектов служб. Также он содержит данные еще трех типов.

* **Профили публикации**  
Профили для развертывания в различных средах.

* **Сценарии**  
Сценарий PowerShell для развертывания и обновления приложения.

* **Определение приложения**  
Включает файл ApplicationManifest.xml в *ApplicationPackageRoot*, описывающей композицию приложения. Связанные файлы параметров приложения расположены в разделе *ApplicationParameters*, которые можно использовать для указания параметров среды. Visual Studio выбирает файл параметров приложения, который указан в связанном профиле публикации во время развертывания в определенную среду.
    
Обзор содержимого проекта службы вы найдете в статье [Начало работы со службами Reliable Services в Service Fabric](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Развертывание и отладка приложения

Теперь, когда вы создали приложение, запустите его.

Чтобы начать отладку, разверните приложение, нажав в Visual Studio клавишу `F5`.

>[!NOTE]
>Во время первого запуска и развертывания приложения локально Visual Studio создает локальный кластер для отладки. Это может занять некоторое время. Процесс создания кластера можно наблюдать в окне вывода Visual Studio.

Когда кластер будет готов, вы получите уведомление от приложения диспетчера области уведомлений локального кластера, которое входит в состав пакета SDK.
   
![Уведомление в области уведомлений локального кластера][4]

Когда приложение будет запущено, Visual Studio автоматически откроет **средство просмотра событий диагностики**, в котором отображаются данные трассировки службы.
   
![Средство просмотра диагностических событий][5]

>[!NOTE]
>События будут автоматически отслеживаться в средстве просмотра диагностических событий. Но если вам нужно вручную настроить его, сначала откройте файл `ServiceEventSource.cs`, расположенный в проекте **MyStatefulService**. Скопируйте значение атрибута `EventSource` в верхней части класса `ServiceEventSource`. В примере ниже источник события — `"MyCompany-MyApplication-MyStatefulService"` (у вас может отличаться).
>
>![Определение имени источника события службы][service-event-source-name]
>
>Затем щелкните значок шестеренки на вкладке в средстве просмотра диагностических событий, чтобы открыть диалоговое окно **Поставщики трассировки событий Windows**. Вставьте скопированное имя источника событий в поле **Поставщики трассировки событий Windows**. Щелкните **Применить**. Трассировка событий будет запущена автоматически.
>
>![Выбор имени источника диагностических событий][setting-event-source-name]
>
>Теперь вы будете видеть события, которые отображаются в окне диагностических событий.

Используемый шаблон службы с отслеживанием состояния содержит значение счетчика, которое увеличивается в методе `RunAsync` файла **MyStatefulService.cs**.

Разверните одно из событий для получения дополнительных сведений, в том числе об узле, где выполняется код. В нашем случае это \_узел 0\_, хотя на вашем компьютере это может быть другой узел.
   
![Подробная информация в средстве просмотра диагностических событий][6]

Локальный кластер содержит пять узлов, размещенных на одном компьютере. В рабочей среде каждый узел размещается на различных физических или виртуальных машинах. Чтобы потренироваться использовать отладчик Visual Studio, имитировав потерю связи с машиной, можно отключить один из узлов локального кластера.

В окне **Обозреватель решений** откройте **MyStatefulService.cs**. 

Найдите метод `RunAsync` и установите точку остановки в первой строке метода.

![Точка останова в методе RunAsync службы с отслеживанием состояния][7]

Запустите средство **Service Fabric Explorer**, щелкнув правой кнопкой мыши область уведомлений **диспетчера локального кластера** и выберите пункт **Manage Local Cluster** (Управление локальным кластером).

![Запуск обозревателя Service Fabric из диспетчера локального кластера][systray-launch-sfx]

[**Service Fabric Explorer**](service-fabric-visualizing-your-cluster.md) обеспечивает визуальное представление кластера. Он включает в себя набор приложений, развернутых в нем, а также набор физических узлов, обеспечивающих их работу.

В области слева разверните **Кластер > Узлы** и найдите узел, в котором выполняется код приложения.

Щелкните **Действия > Отключить (перезапустить)** для имитации перезапуска компьютера.

![Остановка узла в обозревателе Service Fabric][sfx-stop-node]

Вы сразу же увидите срабатывание точки останова в Visual Studio. Это означает, что процесс вычислений с отключенного узла передан на другой узел как единое целое.


Затем вернитесь к средству просмотра диагностических событий и изучите сообщения. Показания счетчика продолжают расти, хотя события теперь поступают с другого узла.

![Информация в средстве просмотра диагностических событий после аварийного переключения][diagnostic-events-viewer-detail-post-failover]

## <a name="cleaning-up-the-local-cluster-optional"></a>Очистка локального кластера (необязательно)

Помните, что этот локальный кластер работает по-настоящему. Если остановить работу отладчика, будет удален экземпляр приложения и отменена регистрация типа приложения. Однако кластер при этом будет работать в фоновом режиме. Когда вы захотите остановить локальный кластер, имеется несколько вариантов.

### <a name="keep-application-and-trace-data"></a>Хранение приложения и трассировка данных

Завершите работу кластера, щелкнув правой кнопкой мыши область уведомлений **диспетчера локального кластера** и выберите пункт **Stop Local Cluster** (Остановить локальный кластер).

### <a name="delete-the-cluster-and-all-data"></a>Удаление кластера и всех данных

Удалите кластер, щелкнув правой кнопкой мыши область уведомлений **диспетчера локального кластера** и выберите пункт **Remove Local Cluster** (Удалить локальный кластер). 

Если этот параметр выбран, Visual Studio повторно развернет кластер во время следующего запуска приложения. Выбирайте этот вариант, только если вы не собираетесь использовать его в течение некоторого времени или вам нужно освободить ресурсы.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о [надежных службах](service-fabric-reliable-services-introduction.md)
<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
