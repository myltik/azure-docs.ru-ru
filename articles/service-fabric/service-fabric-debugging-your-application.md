<properties
   pageTitle="Отладка приложения в Visual Studio | Microsoft Azure"
   description="Повысьте надежность и производительность служб, разрабатывая их в Visual Studio и локальном кластере разработки."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="vturecek;mikhegn"/>

# Отладка приложения Service Fabric с помощью Visual Studio

## Отладка локального приложения Service Fabric

Вы можете сэкономить время и деньги, развернув приложение Azure Service Fabric и выполнив его отладку в кластере для разработки, состоящем из локальных компьютеров. С помощью Visual Studio можно развернуть приложение в локальном кластере и автоматически подключить отладчик ко всем экземплярам приложения.

1. Запустите кластер локальной разработки, выполнив действия, описанные в статье [Настройка среды разработки Service Fabric](service-fabric-get-started.md).

2. Нажмите клавишу **F5** или выберите в меню **Отладка** команду **Начать отладку**.

    ![Запуск отладки приложения][startdebugging]

3. Задайте в коде точки останова и поэтапно выполните приложение, выбирая соответствующие команды в меню **Отладка**.

    > [AZURE.NOTE] Visual Studio подключается ко всем экземплярам приложения. При пошаговом выполнении кода разные процессы могут одновременно достигать точек останова. Это приводит к возникновению параллельных сеансов. Чтобы этого не происходило, настройте отключение точек останова после их достижения, сделав их зависимыми от идентификатора потока, или используйте события диагностики.

4. Окно **События диагностики** открывается автоматически и позволяет просматривать данные о событиях диагностики в реальном времени.

    ![Просмотр событий диагностики в режиме реального времени][diagnosticevents]

5. Кроме того, вы можете открыть окно **События диагностики** вручную, используя обозреватель облака. В разделе **Service Fabric** щелкните любой узел правой кнопкой мыши и выберите пункт **Показать трассировки потоковой передачи**.

    ![Окно событий диагностики][viewdiagnosticevents]

    Чтобы отфильтровать трассировки по определенной службе или приложению, просто включите потоковую передачу трассировки по этой службе или приложению.

6. События диагностики можно просматривать в автоматически созданном файле **ServiceEventSource.cs** и вызывать из кода приложения.

    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```

7. В окне **События диагностики** поддерживаются фильтрация, приостановка и проверка событий в реальном времени. Фильтр представляет собой простой поиск по строкам сообщений о событиях, включая их содержимое.

    ![Фильтрация, приостановка, возобновление и проверка событий в реальном времени][diagnosticeventsactions]

8. Отладка служб аналогична отладке приложений. Обычно для простой отладки точки останова задаются в Visual Studio. Несмотря на то, что надежные коллекции реплицируются на нескольких узлах, они все равно реализуют интерфейс IEnumerable. Это означает, что вы можете использовать представление результатов в Visual Studio во время отладки для просмотра внутренних сведений. Просто задайте точку останова в любом месте своего кода.

    ![Запуск отладки приложения][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Отладка удаленного приложения Service Fabric

Если приложения Service Fabric выполняются в кластере Service Fabric в Azure, они доступны для удаленной отладки непосредственно из Visual Studio.

> [AZURE.NOTE] Компоненту требуется [пакет SDK 2.0 для Service Fabric](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) и [пакет SDK Azure для .NET 2.9](https://azure.microsoft.com/downloads/).

<!-- -->
> [AZURE.WARNING] Удаленная отладка предназначена для сценариев разработки и тестирования и не должна использоваться в производственной среде, поскольку влияет на запущенные приложения.

1. Перейдите к своему кластеру в **обозревателе облака**, щелкните его правой кнопкой мыши и выберите пункт **Включить отладку**

    ![Включение удаленной отладки][enableremotedebugging]

    При этом включается расширение удаленной отладки в узлах кластера, а также необходимые конфигурации сети.

2. Щелкните узел кластера в **обозревателе облака** правой кнопкой мыши и выберите пункт **Подключить отладчик**.

    ![Подключение отладчика][attachdebugger]

3. В диалоговом окне **Подключение к процессу** выберите процесс, который нужно отладить, и нажмите кнопку **Подключить**.

    ![Выбор процесса][chooseprocess]

    Имя процесса, к которому нужно подключиться, совпадает с именем сборки для проекта службы.

    Отладчик подключится ко всем узлам, на которых выполняется процесс.
    - При отладке службы без отслеживания состояния сеанс отладки затрагивает все экземпляры службы на всех узлах.
    - При отладке службы с отслеживанием состояния активна только первичная реплика каждого раздела, а значит, отладчик затрагивает только ее. Если во время сеанса отладки первичная реплика переносится, обработка этой реплики по-прежнему входит в сеанс отладки.
    - Чтобы перехватывать только релевантные секции или экземпляры определенной службы, можно использовать условные точки останова, которые будут останавливать только определенную секцию или экземпляр.

    ![Условная точка останова][conditionalbreakpoint]

    > [AZURE.NOTE] Сейчас отладка кластера Service Fabric с несколькими экземплярами исполняемого имени одной и той же службы не поддерживается.

4. После того, как отладка приложения будет завершена, расширение удаленной отладки можно отключить, щелкнув кластер в **обозревателе облака** правой кнопкой мыши и выбрав пункт **Отключить отладку**.

    ![Отключение удаленной отладки][disableremotedebugging]

## Потоковая передача трассировок из удаленного узла кластера

Трассировки можно также передавать из удаленного узла кластера в Visual Studio напрямую. Эта функция позволяет передавать события трассировки ETW, возникающие на узле кластера Service Fabric, непосредственно в Visual Studio.

> [AZURE.NOTE] Компоненту требуется [пакет SDK 2.0 для Service Fabric](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) и [пакет SDK Azure для .NET 2.9](https://azure.microsoft.com/downloads/).

<!-- -->
> [AZURE.WARNING] Потоковая передача трассировок предназначена для сценариев разработки и тестирования и не должна использоваться в производственной среде, поскольку влияет на запущенные приложения. В рабочем сценарии следует применять пересылку событий с помощью средств диагностики Azure.

1. Перейдите к своему кластеру в **обозревателе облака**, щелкните его правой кнопкой мыши и выберите пункт **Включить потоковую передачу трассировок**.

    ![Включение трассировки удаленной потоковой передачи][enablestreamingtraces]

    При этом включается расширение потоковой передачи трассировок в узлах кластера, а также необходимые конфигурации сети.

2. Разверните элемент **Узлы** в **обозревателе облака**, щелкните узел, потоковую передачу трассировок с которого вы хотите включить, правой кнопкой мыши и выберите пункт **Показать потоковую передачу трассировок**.

    ![Просмотр трассировки удаленной потоковой передачи][viewremotestreamingtraces]

    Повторите шаг 2 для всех узлов, трассировку которых вы хотите получать. Потоковая передача каждого узла будет отображаться в отдельном окне.

    Теперь вы можете также получать трассировки, которые выпускает Service Fabric и ваши собственные службы. Если вы хотите отфильтровать события по определенному приложению, просто введите название этого приложения в качестве фильтра.

    ![Просмотр трассировки потоковой передачи][viewingstreamingtraces]

4. После того, как потоковая передача трассировок будет завершена, вы можете отключить удаленную потоковую передачу трассировок, щелкнув кластер в **обозревателе облака** правой кнопкой мыши и выбрав пункт **Отключить потоковую передачу трассировок**.

    ![Отключение трассировки удаленной потоковой передачи][disablestreamingtraces]

## Дальнейшие действия

- [Проверка службы Service Fabric](service-fabric-testability-overview.md)
- [Управление приложениями Service Fabric в Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png

<!---HONumber=AcomDC_0622_2016-->