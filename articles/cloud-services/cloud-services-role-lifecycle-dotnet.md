<properties 
pageTitle="Обработка событий жизненного цикла облачной службы | Microsoft Azure" 
description="Узнайте об использовании методов жизненного цикла роли облачной службы в .NET" 
services="cloud-services" 
documentationCenter=".net" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="12/07/2015" 
ms.author="adegeo"/>

# Адаптируйте жизненный цикл веб-роли или рабочей роли в .NET

Создавая рабочую роль, вы расширяете класс [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx), который предоставляет вам методы переопределения, которые позволяют реагировать на события жизненного цикла. Этот класс необязателен для веб-роли и чаще всего используется для реагирования на события жизненного цикла.

## Расширение класса RoleEntryPoint

Класс [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) включает в себя методы, вызываемые Azure при **запуске**, **выполнении** и **остановке** рабочей или веб-роли. Для управления ролями инициализации, последовательности завершения работы или потока выполнения при необходимости можно переопределить эти методы.

При расширении **RoleEntryPoint** нужно знать о следующих вариантах работы этих методов:

-   Методы [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) и [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) возвращают логическое значение, и эти методы могут вернуть значение **false**.

     Если код возвращает **false**, роль процесса немедленно завершается без запуска процессов завершения имеющихся последовательностей. В большинстве случаев нужно избегать возвращения значения **false** из метода **OnStart**.
     
-   При любом неперехваченном исключении при перегрузке **RoleEntryPoint** метод рассматривается как необработанное исключение.

     Если исключение возникает в одном из методов жизненного цикла, Azure генерирует событие [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) и процесс завершается. После перевода вашей роли в автономный режим Azure перезапустит ее. При возникновении необработанного исключения не вызывается событие [Stopping](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) и метод **OnStop**.

Если ваша роль не запускается или зацикливается между состояниями инициализации, занятости и остановки, возможно ваш код вызывает необработанное исключение в одном из событий жизненного цикла при каждом перезапуске роли. В этом случае следует использовать событие [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx), которое определит причину исключения и соответствующим образом ее обработает. Кроме того, ваша роль могла возвращаться из метода [Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx), что приводит к ее перезапуску. Узнать больше о состоянии развертывания можно в разделе [Распространенные проблемы, которые вызывают повторное использование ролей](https://msdn.microsoft.com/library/azure/gg465402.aspx).

> [AZURE.NOTE]Если для разработки приложения вы используете [средства Azure для Microsoft Visual Studio](https://msdn.microsoft.com/library/azure/ee405484.aspx), шаблоны проекта роли автоматически расширят класс **RoleEntryPoint** в файлах WebRole.cs и WorkerRole.cs.

## Метод OnStart

Метод **OnStart** вызывается, когда Azure переводит экземпляр роли в сетевой режим. Во время выполнения кода OnStart, экземпляр роли помечается как **Busy** и подсистема балансировки нагрузки не будет направлять к нему внешний трафик. Можно переопределить этот метод для выполнения инициализации, например для реализации обработчиков событий и запуска [диагностики Azure](cloud-services-how-to-monitor.md).

Если **OnStart** возвращает значение **true**, то экземпляр инициализирован успешно и Azure вызывает метод **RoleEntryPoint.Run**. Если **OnStart** возвращает значение **false**, роль немедленно завершается без выполнения запланированных выключений.

В следующем примере кода показано, как переопределить метод **OnStart**. Этот метод настраивает и запускает монитор диагностики при запуске экземпляра роли и устанавливает передачу данных журналов в учетную запись хранилища:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## Метод OnStop

Метод **OnStop** вызывается после того, как Azure переведет экземпляр роли в автономный режим еще до завершения процесса. Можно переопределить этот метод с целью выполнения кода, необходимого для экземпляра роли и чистого завершения работы приложения.

> [AZURE.IMPORTANT]Код, выполняемый методом **OnStop**, имеет ограниченное время для завершения, когда вызов происходит по причинам, не связанным с завершением работы по инициативе пользователя. По истечении этого времени процесс завершается, поэтому убедитесь, что код в методе **OnStop** выполняется быстро и допускает неполное выполнение. Метод **OnStop** вызывается после генерирования события **Stopping**.


## Метод Run

Метод **Run** можно переопределить для реализации длительно выполняемого потока экземпляра роли.

Переопределение метода **Run** не обязательно. По умолчанию запускается поток, который постоянно находится в режиме ожидания. При переопределении метода **Run** код должен заблокироваться на неопределенное время. При возвращении метода **Run** роль автоматически и корректно перезапускается. Другими словами, Azure создает событие **Stopping** и вызывает метод **OnStop**, чтобы выполнить последовательность завершения работы до того, как ваша роль перейдет в автономный режим.


### Реализация методов жизненного цикла ASP.NET для веб-роли

Для управления последовательностями инициализации и завершения работы веб-роли вы можете использовать методы жизненного цикла ASP.NET в дополнение к тем, которые предоставляются классом **RoleEntryPoint**. Это может быть полезно для обеспечения совместимости при переносе существующего приложения ASP.NET в Azure. Методы жизненного цикла ASP.NET вызываются из методов **RoleEntryPoint**. Метод **Application\_Start** вызывается по завершении вызова метода **RoleEntryPoint.OnStart**. Метод **Application\_End** вызывается перед вызовом метода **RoleEntryPoint.OnStop**.

## Дальнейшие действия
Узнайте, как [создать пакет облачной службы](cloud-services-model-and-package.md).

<!---HONumber=AcomDC_1210_2015-->