---
title: "Включение Azure Application Insights Profiler для вычислительных ресурсов | Документация Майкрософт"
description: "Узнайте, как настраивать профилировщик"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: cfreeman
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: cde0b0bce2e332243e555a72088b8e9eeb680bdb
ms.contentlocale: ru-ru
ms.lasthandoff: 07/28/2017

---

# <a name="how-to-enable-application-insights-profiler-on-azure-compute-resources"></a>Как включить Application Insights Profiler в ресурсах вычислений Azure

В этом пошаговом руководстве показано, как включить Application Insights Profiler в приложении ASP.NET, размещенном в вычислительных ресурсах Azure. Примеры включают поддержку виртуальных машин, масштабируемых набор виртуальных машин и Service Fabric. Все примеры используют шаблоны, которые поддерживают модели развертывания Azure Resource Management. Дополнительные сведения о модели развертывания см. в статье [Развертывание с помощью Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](/azure-resource-manager/resource-manager-deployment-model).

## <a name="overview"></a>Обзор

В следующей схеме показано, как профилировщик работает для вычислительных ресурсов Azure. Виртуальная машина Azure используется в качестве примера.

![Общие сведения](./media/enable-profiler-compute/overview.png) Чтобы собирать сведения для обработки и отображения на портале Azure, необходимо установить компонент агента диагностики для вычислительных ресурсов Azure. Остальная часть пошагового руководства предоставляет сведения о том, как установить и настроить агент диагностики для включения Application Insights Profiler.

## <a name="prerequisites-for-the-walkthrough"></a>Предварительные требования для данного пошагового руководства

* Скачать шаблоны развертывания Resource Manager, которые устанавливают агент профилировщика на виртуальные машины или масштабируемые наборы.

    [WindowsVirtualMachine.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachine.json) | [WindowsVirtualMachineScaleSet.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachineScaleSet.json)
* Экземпляр Application Insights, включенный для профилирования. Инструкции см. по адресу https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler.
* Платформа .NET версии 4.6.1 или более поздней, установленная в целевом вычислительном ресурсе Azure.

## <a name="create-a-resource-group-in-your-azure-subscription"></a>Создание группы ресурсов в подписке Azure
В следующем примере показано, как создать группу ресурсов с помощью скрипта PowerShell:

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>Создание ресурса Application Insights в группе ресурсов

![Создание Application Insights](./media/enable-profiler-compute/createai.png)

## <a name="apply-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>Применение ключа инструментирования Application Insights в шаблоне Azure Resource Manager
Если вы еще не скачали шаблон, скачайте шаблон, который приведен ниже. [WindowsVirtualMachine.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachine.json)

![Поиск ключа AI](./media/enable-profiler-compute/copyaikey.png)

![Замена значения шаблона](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-azure-vm-to-host-the-web-application"></a>Создание виртуальной машины Azure для размещения веб-приложения
* Создайте защищенную строку для сохранения пароля.
```
$password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
```

* Разверните шаблон Azure Resource Manager.

Измените каталог в консоли PowerShell на папку, которая содержит шаблон Resource Manager. Чтобы развернуть шаблон, выполните следующую команду:

```
New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
```

После успешного выполнения скрипта нужно найти виртуальную машину с именем *MyWindowsVM* в группе ресурсов.

## <a name="configure-web-deploy-on-the-vm"></a>Настройка веб-развертывания на виртуальной машине
Убедитесь, что **веб-развертывание** включено на виртуальной машине, чтобы вы могли публиковать веб-приложения из Visual Studio.

Веб-развертывание можно установить на виртуальную машину вручную через WebPI. Дополнительные сведения см. в статье [Installing and Configuring Web Deploy on IIS 8.0 or Later](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) (Установка и настройка веб-развертывания в IIS версии 8.0 или более поздней).

Ниже приведен пример автоматизации установки веб-развертывания с помощью шаблона Azure Resource Manager. Дополнительные сведения см. в статье [Create, configure, and deploy Web Application to an Azure VM](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/) (Создание, настройка и развертывание веб-приложения на виртуальную машину Azure).

При развертывании приложения ASP.NET MVC необходимо перейти к диспетчеру сервера, выбрать **Добавить роли и компоненты | Веб-сервер (IIS) | Веб-сервер | Разработка приложений** и включить ASP.NET версии 4.5 на сервере.
![Добавление ASP.NET](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-azure-application-insights-sdk-to-your-project"></a>Установка пакета SDK Azure Application Insights в проект
* Откройте веб-приложение ASP.NET в Visual Studio.
* Щелкните правой кнопкой мыши проект и выберите **Добавить | Подключенные службы**.
* Выберите Application Insights.
* Следуйте инструкциям на странице. Выберите ресурс Application Insights, который вы создали ранее.
* Нажмите кнопку **Зарегистрировать**.


## <a name="publish-the-project-to-azure-vm"></a>Публикация проекта в виртуальной машине Azure
Имеется несколько способов публикации приложения на виртуальную машину Azure. Один из них — использовать Visual Studio 2017.
Чтобы завершить процесс публикации, щелкните правой кнопкой мыши проект и выберите "Опубликовать...". Выберите виртуальную машину Azure в качестве цели публикации и следуйте инструкциям.

![Публикация из Visual Studio](./media/enable-profiler-compute/publishtoVM.png)

Запустите несколько нагрузочных тестов для вашего приложения. Вы сможете увидеть результаты на веб-странице портала экземпляра Application Insights.


## <a name="enable-the-profiler-in-application-insights"></a>Включение профилировщика в Application Insights
Перейдите к колонке "Производительность" Application Insights. Щелкните значок настройки и выберите Enable the Profiler (Включение профилировщика).

![Включение профилировщика —шаг 1](./media/enable-profiler-compute/enableprofiler1.png)

![Включение профилировщика —шаг 2](./media/enable-profiler-compute/enableprofiler2.png)

## <a name="add-an-availability-test-to-your-application"></a>Добавление теста доступности в приложение
Найдите ресурс Application Insights, который вы создали ранее. Перейдите к колонке "Доступность" и добавьте тест производительности, который отправляет веб-запросы на URL-адрес приложения. Таким образом мы можем собирать примеры данных для отображения в Application Insights Profiler.

![Добавление теста производительности] [./media/enable-profiler-compute/add-test.png]

## <a name="view-your-performance-data"></a>Просмотр данных о производительности

Подождите 10–15 минут, пока профилировщик соберет и проанализирует данные. Затем перейдите к колонке "Производительность" своего ресурса Application Insights и просмотрите, как приложение работает, когда находится под нагрузкой.

![Просмотр производительности] [./media/enable-profiler-compute/view-aiperformance.png]

Щелкните значок в разделе примеров с открытой колонкой Trace View (Представление трассировки).

![Представление трассировки][./media/enable-profiler-compute/traceview.png]


## <a name="work-with-an-existing-template"></a>Работа с имеющимся шаблоном

1. Найдите объявление ресурса системы диагностики Azure (WAD) в шаблоне развертывания.
  * Создайте его, если у вас еще нет его (просмотрите, как это сделать в полном примере).
  * Шаблон можно обновить с веб-сайта Azure Resources (https://resources.azure.com).
2. Измените издателя с "Microsoft.Azure.Diagnostics" на "AIP.Diagnostics.Test".
3. Используйте для typeHandlerVersion значение 0.0.
4. Убедитесь, что параметру autoUpgradeMinorVersion присвоено значение true.
5. Добавьте новый экземпляр приемника ApplicationInsightsProfiler в объект параметров WadCfg, как показано в следующем примере.

```
"resources": [
        {
          "type": "extensions",
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "apiVersion": "2016-03-30",
          "properties": {
            "publisher": "AIP.Diagnostics.Test",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "WadCfg": {
                "SinksConfig": {
                  "Sink": [
                    {
                      "name": "Give a descriptive short name. E.g.: MyApplicationInsightsProfilerSink",
                      "ApplicationInsightsProfiler": "Enter the Application Insights instance instrumentation key guid here"
                    }
                  ]
                },
                "DiagnosticMonitorConfiguration": {
                    ...
                }
                ...
              }
              ...
            }
            ...
          }
          ...
]
```

## <a name="enable-the-profiler-on-virtual-machine-scale-sets"></a>Включение профилировщика в масштабируемых наборах виртуальных машин
Скачайте шаблон [WindowsVirtualMachineScaleSet.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachineScaleSet.json), чтобы увидеть, как включить профилировщик. Примените те же изменения в шаблоне виртуальной машины к ресурсу расширения диагностики масштабируемых наборов виртуальных машин.
Убедитесь, что каждый экземпляр масштабируемого набора имеет доступ к Интернету, чтобы агент профилировщика мог отправлять собранные примеры в Application Insights для анализа и отображения.

## <a name="enable-the-profiler-on-service-fabric-applications"></a>Включение профилировщика в приложении Service Fabric
В настоящее время для включения профилировщика в приложениях Service Fabric требуется следующее:
1. Подготовка кластера Service Fabric с расширением WAD, которое устанавливает агент профилировщика.
2. Установка пакета SDK в проект и настройка ключа Application Insights.
3. Добавление кода приложения к телеметрии инструментирования.

## <a name="provision-the-service-fabric-cluster-have-the-wad-extension-that-installs-the-profiler-agent"></a>Подготовка кластера Service Fabric с расширением WAD, которое устанавливает агент профилировщика
Кластер Service Fabric может быть безопасным или небезопасным. Можно настроить один кластер шлюза как небезопасный, чтобы он не требовал сертификата для доступа. Кластеры, содержащие бизнес-логику и данные, должны быть безопасными. Вы можете включить профилировщик на безопасных и небезопасных кластерах Service Fabric. В этом пошаговом руководстве в качестве примера используется небезопасный кластер в качестве примера, чтобы объяснить, какие изменения необходимы для включения профилировщика. Таким же образом можно подготовить безопасный кластер.

Скачайте [ServiceFabricCluster.json](https://github.com/CFreemanwa/samples/blob/master/ServiceFabricCluster.json). Как для виртуальных машин, так и для масштабируемых наборов виртуальных машин замените ключи Application Insights своими ключами:

```
"publisher": "AIP.Diagnostics.Test",
                 "settings": {
                   "WadCfg": {
                     "SinksConfig": {
                       "Sink": [
                         {
                           "name": "MyApplicationInsightsProfilerSinkVMSS",
                           "ApplicationInsightsProfiler": "[Application_Insights_Key]"
                         }
                       ]
                     },
```

Разверните шаблон с помощью скрипта PowerShell:
```
Login-AzureRmAccount
New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

```

## <a name="install-application-insights-sdk-in-the-project-and-configure-ai-key"></a>Установка пакета SDK в проект и настройка ключа Application Insights
Установите пакет SDK Application Insights из пакета NuGet. Убедитесь, что устанавливается стабильная версия 2.3 или более поздняя. [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Дополнительные сведения о настройке Application Insights в проектах см. в статье [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Использование Service Fabric с Application Insights).

## <a name="add-application-code-to-instrument-telemetry"></a>Добавление кода приложения к телеметрии инструментирования
Для любого фрагмента кода, который необходимо инструментировать, добавьте оператор using. В следующем примере метод RunAsync выполняет некоторые действия, а класс telemetryClient регистрирует данные телеметрии сразу после запуска. Этому событию нужно уникальное имя по всему приложению.

```
protected override async Task RunAsync(CancellationToken cancellationToken)
       {
           // TODO: Replace the following sample code with your own logic
           //       or remove this RunAsync override if it's not needed in your service.

           while (true)
           {
               using( var operation = telemetryClient.StartOperation<RequestTelemetry>("[Insert_Event_Unique_Name]"))
               {
                   cancellationToken.ThrowIfCancellationRequested();

                   ++this.iterations;

                   ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", this.iterations);

                   await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
               }

           }
       }
```

Разверните приложение в кластер Service Fabric. Подождите, пока приложение будет выполняться в течение 10 минут. Для повышения эффекта в приложении можно выполнить нагрузочный тест. Перейдите в колонку "Производительность" портала Application Insights, там вы должны увидеть появление трассировки профилирования.

<!---
Commenting out these sections for now
## Enable the Profiler on Cloud Services applications
[TODO]
## Enable the Profiler on classic Azure Virtual Machines
[TODO]
## Enable the Profiler on on-premise servers
[TODO]
--->

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения об устранении неполадок профилировщика см. в разделе [Устранение неполадок](app-insights-profiler.md#troubleshooting).

- Дополнительные сведения о профилировщике см. в статье [Профилирование динамических веб-приложений Azure с помощью Application Insights](app-insights-profiler.md).

