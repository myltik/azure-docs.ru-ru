---
title: "Создание первого приложения Service Fabric в Visual Studio | Документация Майкрософт"
description: "Создание, развертывание и отладка приложения Service Fabric с помощью Visual Studio"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: e8b2379c750047bf2a4c7342815b5c3aab3883c6
ms.openlocfilehash: a101ad134e15a0da5e6d3fd5cbf4ca051da34e86


---
# <a name="create-your-first-azure-service-fabric-application"></a>Создание первого приложения Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# для Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java для Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# для Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

Пакет SDK Service Fabric содержит надстройку для Visual Studio, в которой есть шаблоны и средства для создания, развертывания и отладки приложений Service Fabric. Эта статья описывает пошаговый процесс создания первого приложения в Visual Studio.

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы [настройте среду разработки](service-fabric-get-started.md).

## <a name="video-walkthrough"></a>Видеоруководство
Действия, описанные в этом руководстве, показаны в следующем видеоролике.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio/player]
> 
> 

## <a name="create-the-application"></a>Создание приложения
Приложение Service Fabric может содержать одну или несколько служб, каждая из которых выполняет определенную роль в работе приложения. С помощью мастера создания проекта создайте проект приложения и первый проект службы. Позже вы можете добавить дополнительные службы.

1. Запустите Visual Studio от имени администратора.
2. Выберите **Файл > Новый проект > Облако > Приложение Service Fabric**.
3. Присвойте приложению имя и нажмите кнопку **ОК**.
   
    ![Диалоговое окно "Новый проект" в Visual Studio][1]
4. На следующей странице выберите службу **с отслеживанием состояния**. Она будет включена в приложение первой. Присвойте ей имя и нажмите кнопку **ОК**.
   
    ![Диалоговое окно "Новая служба" в Visual Studio][2]
   
   > [!NOTE]
   > Дополнительные сведения о доступных возможностях см. в статье [Общие сведения о модели программирования Service Fabric](service-fabric-choose-framework.md).
   > 
   > 
   
    Visual Studio создаст проект приложения и проект службы с отслеживанием состояния, затем отобразит их в обозревателе решений.
   
    ![Обозреватель решений после создания приложения и службы с отслеживанием состояния][3]
   
    Проект приложения не содержит код. Он только ссылается на набор проектов служб. Также он содержит данные еще трех типов.
   
   * **Профили публикации.** Управляют настройками инструментов для различных сред.
   * **Скрипты.** Включают в себя скрипт PowerShell для развертывания и обновления приложения. Visual Studio выполняет этот сценарий в фоновом режиме. Вы также можете запустить его из командной строки.
   * **Определение приложения.** Содержит манифест приложения в разделе *ApplicationPackageRoot*. Связанные файлы параметров приложения расположены в разделе *ApplicationParameters*. Они определяют характеристики этого приложения, и с помощью них можно настроить приложение для конкретной среды.
     
     Обзор содержимого проекта службы вы найдете в статье [Начало работы со службами Reliable Services в Service Fabric](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Развертывание и отладка приложения
Теперь, когда вы создали приложение, попробуйте запустить его.

1. Чтобы начать отладку, разверните приложение, нажав в Visual Studio клавишу F5.
   
   > [!NOTE]
   > Запаситесь терпением — при первом запуске Visual Studio создает локальный кластер для разработки. Локальный кластер выполняет на одном локальном компьютере тот же код платформы, который будет затем развернут на кластере с несколькими компьютерами. Процесс создания кластера можно наблюдать в окне вывода Visual Studio.
   > 
   > 
   
    Когда кластер будет готов, вы получите уведомление от приложения диспетчера области уведомлений локального кластера, которое входит в состав пакета SDK.
   
    ![Уведомление в области уведомлений локального кластера][4]
2. Когда приложение будет запущено, Visual Studio автоматически откроет средство просмотра событий диагностики, в котором отображаются данные трассировки службы.
   
    ![Средство просмотра диагностических событий][5]
   
    Для шаблона службы с отслеживанием состояния эти сообщения содержат только значение счетчика, которое увеличивается в методе `RunAsync` файла MyStatefulService.cs.
3. Разверните одно из событий для получения дополнительных сведений, в том числе об узле, где выполняется код. В данном случае это узел 2, хотя на вашем компьютере это может быть другой узел.
   
    ![Подробная информация в средстве просмотра диагностических событий][6]
   
    Локальный кластер содержит пять узлов, размещенных на одном компьютере. Он имитирует кластер из пяти узлов, находящихся на разных компьютерах. Отключим один из узлов локального кластера, имитируя потерю связи с машиной. Заодно мы потренируемся использовать отладчик Visual Studio.
   
   > [!NOTE]
   > Шаблон проекта создает события диагностики приложений, которые используют встроенный класс `ServiceEventSource`. Дополнительные сведения см. в статье [Мониторинг и диагностика состояния служб в локальной среде разработки](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
   > 
   > 
4. Найдите в проекте службы класс, унаследованный от класса StatefulService (например, MyStatefulService), и установите в нем точку останова на первой строке метода `RunAsync`.
   
    ![Точка останова в методе RunAsync службы с отслеживанием состояния][7]
5. Щелкните правой кнопкой мыши область уведомлений диспетчера локального кластера и выберите пункт **Управление локальным кластером**, чтобы запустить обозреватель Service Fabric.
   
    ![Запуск обозревателя Service Fabric из диспетчера локального кластера][systray-launch-sfx]
   
    Service Fabric Explorer отображает кластер, в том числе составляющие его физические узлы и развернутые на нем приложения. Дополнительные сведения о Service Fabric Explorer см. в статье о [визуализации кластера](service-fabric-visualizing-your-cluster.md).
6. В области слева разверните **Кластер > Узлы** и найдите узел, в котором выполняется код приложения.
7. Щелкните **Действия > Отключить (перезапустить)** для имитации перезапуска компьютера. (Обратите внимание, что выбрать деактивацию также можно в контекстном меню в представлении списка узлов в левой области).
   
    ![Остановка узла в обозревателе Service Fabric][sfx-stop-node]
   
    Вы сразу же увидите срабатывание точки останова в Visual Studio. Это означает, что процесс вычислений с отключенного узла передан на другой узел как единое целое.
8. Вернитесь к средству просмотра диагностических событий и изучите сообщения. Показания счетчика продолжают расти, хотя события теперь поступают с другого узла.
   
    ![Информация в средстве просмотра диагностических событий после аварийного переключения][diagnostic-events-viewer-detail-post-failover]

## <a name="switch-cluster-mode"></a>Переключение режима кластера
По умолчанию кластер локальной разработки запускается как кластер из 5 узлов, что подходит для отладки служб, развернутых на нескольких узлах. Тем не менее развертывание приложения в таком кластере разработки может занять некоторое время. Чтобы быстро вносить изменения кода, не запуская приложение на 5 узлах, можно переключиться в режим кластера разработки с 1 узлом. Для выполнения кода в кластере с одним узлом щелкните правой кнопкой мыши диспетчер локального кластера на панели задач и выберите **Switch Cluster Mode -> 1 Node** (Переключить режим кластера -> 1 узел).  

![Переключение режима кластера][switch-cluster-mode]

После изменения режима кластер разработки перезапустится, и будут удалены все приложения, подготовленные или работающие на кластере.

## <a name="cleaning-up"></a>Очистка.
  Прежде чем завершить работу, важно помнить, что локальный кластер работает по-настоящему. Если остановить работу отладчика, будет удален экземпляр приложения и отменена регистрация типа приложения. Но кластер при этом будет работать в фоновом режиме. Доступно несколько вариантов управления кластером.

1. Чтобы завершить работу кластера, но сохранить данные приложения и трассировки, в приложении области уведомлений выберите команду **Stop Local Cluster** (Остановить локальный кластер).
2. Чтобы полностью удалить кластер, в приложении области уведомлений щелкните **Remove Local Cluster** (Удалить локальный кластер). Выбор этого параметра приведет к медленному развертыванию при последующем нажатии клавиши F5 в Visual Studio. Удаляйте кластер, только если вы не собираетесь использовать его в течение некоторого времени или вам нужно освободить ресурсы.

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как создать [кластер в Azure](service-fabric-cluster-creation-via-portal.md) или [автономный кластер в Windows](service-fabric-cluster-creation-for-windows-server.md).
* Попробуйте создать службу с помощью модели программирования [Reliable Services](service-fabric-reliable-services-quick-start.md) или [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Узнайте, как предоставлять доступ к службам из Интернета с помощью [внешнего интерфейса веб-служб](service-fabric-add-a-web-frontend.md).
* Ознакомьтесь с [практическим примером](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) , а также создайте службу без отслеживания состояния, настройте мониторинг и отчеты о работоспособности и обновите приложение.

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



<!--HONumber=Dec16_HO1-->


