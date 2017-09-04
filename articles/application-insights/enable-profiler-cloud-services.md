---
title: "Включение Azure Application Insights Profiler в ресурсе облачных служб | Документация Майкрософт"
description: "Узнайте, как настроить профилировщик в приложении ASP.NET, размещенном в ресурсе облачных служб Azure."
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
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: c2cae6129386260f2bf35f75d44fa001f7541d40
ms.contentlocale: ru-ru
ms.lasthandoff: 08/28/2017

---

# <a name="enable-application-insights-profiler-on-an-azure-cloud-services-resource"></a>Включение Azure Application Insights Profiler в ресурсе облачных служб

В этом пошаговом руководстве описывается, как включить Azure Application Insights Profiler в приложении ASP.NET, размещенном в ресурсе облачных служб Azure. Примеры включают поддержку виртуальных машин Azure, масштабируемых наборов виртуальных машин и Azure Service Fabric. Для всех примеров используются шаблоны, которые поддерживают модели развертывания Azure Resource Manager. Дополнительные сведения о модели развертывания см. в статье [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](/azure-resource-manager/resource-manager-deployment-model) (Развертывание с помощью Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов).

## <a name="overview"></a>Обзор

В схеме ниже показано, как профилировщик работает с ресурсами облачных служб Azure. Виртуальная машина Azure используется в качестве примера.

![Общие сведения](./media/enable-profiler-compute/overview.png) Чтобы собирать сведения для обработки и отображения на портале Azure, необходимо установить компонент агента диагностики для ресурсов облачных служб Azure. В остальной части пошагового руководства содержатся сведения о том, как установить и настроить агент диагностики, чтобы включить Application Insights Profiler.

## <a name="prerequisites-for-the-walkthrough"></a>Предварительные требования для данного пошагового руководства

* Шаблон развертывания Resource Manager, который устанавливает агенты профилировщика на виртуальных машинах ([WindowsVirtualMachine.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)) или в масштабируемых наборах ([WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)).

* Экземпляр Application Insights, включенный для профилирования. Инструкции см. в разделе [Включение профилировщика](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler).

* .NET Framework 4.6.1 или более поздней версии в целевом ресурсе облачных служб Azure.

## <a name="create-a-resource-group-in-your-azure-subscription"></a>Создание группы ресурсов в подписке Azure
В следующем примере показано, как создать группу ресурсов с помощью скрипта PowerShell:

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>Создание ресурса Application Insights в группе ресурсов
В колонке **Application Insights** введите информацию для ресурса, как показано в следующем примере: 

![Колонка Application Insights](./media/enable-profiler-compute/createai.png)

## <a name="apply-an-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>Применение ключа инструментирования Application Insights в шаблоне Azure Resource Manager

1. Если вы еще не скачали шаблон, сделайте это на сайте [GitHub](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json).

2. Найдите ключ Application Insights.
   
   ![Расположение ключа](./media/enable-profiler-compute/copyaikey.png)

3. Замените значение шаблона.
   
   ![Значение, замененное в шаблоне](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-an-azure-vm-to-host-the-web-application"></a>Создание виртуальной машины Azure для размещения веб-приложения
1. Создайте защищенную строку для сохранения пароля.

   ```
   $password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
   ```

2. Разверните шаблон Azure Resource Manager.

   Измените каталог в консоли PowerShell на папку с шаблоном Resource Manager. Чтобы развернуть шаблон, выполните следующую команду:

   ```
   New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
   ```

После успешного выполнения скрипта нужно найти виртуальную машину с именем **MyWindowsVM** в группе ресурсов.

## <a name="configure-web-deploy-on-the-vm"></a>Настройка веб-развертывания на виртуальной машине
Убедитесь, что на виртуальной машине включено веб-развертывание, чтобы вы могли публиковать веб-приложения из Visual Studio.

Чтобы установить веб-развертывание на виртуальную машину вручную с использованием WebPI, см. статью [Installing and Configuring Web Deploy on IIS 8.0 or Later](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) (Установка и настройка веб-развертывания в IIS 8.0 или более поздней версии). Пример того, как автоматизировать установку веб-развертывания с помощью шаблона Azure Resource Manager, см. в статье [Create, configure, and deploy Web Application to an Azure VM](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/) (Создание, настройка и развертывание веб-приложения на виртуальной машине Azure).

Если вы развертываете приложение ASP.NET MVC, перейдите к диспетчеру сервера, выберите **Добавить роли и компоненты** > **Веб-сервер (IIS)** > **Веб-сервер** > **Разработка приложений** и включите на сервере ASP.NET 4.5.

![Добавление ASP.NET](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-the-azure-application-insights-sdk-for-your-project"></a>Установка пакета SDK Azure Application Insights в проект
1. Откройте веб-приложение ASP.NET в Visual Studio.

2. Щелкните проект правой кнопкой мыши и выберите **Добавить** > **Подключенные службы**.

3. Выберите **Application Insights**.

4. Следуйте инструкциям на странице. Выберите ресурс Application Insights, который вы создали ранее.

5. Нажмите кнопку **Зарегистрировать**.


## <a name="publish-the-project-to-an-azure-vm"></a>Публикация проекта на виртуальной машине Azure
Имеется несколько способов публикации приложения на виртуальную машину Azure. Один из них — использование Visual Studio 2017.

1. Щелкните проект правой кнопкой мыши и выберите **Опубликовать**.

2. Выберите **Виртуальные машины Microsoft Azure** в качестве цели публикации и следуйте инструкциям.

   ![Публикация из Visual Studio](./media/enable-profiler-compute/publishtoVM.png)

3. Запустите нагрузочный тест для вашего приложения. На веб-странице портала экземпляра Application Insights должны отобразиться результаты.


## <a name="enable-the-profiler"></a>Включение профилировщика
1. Перейдите к колонке Application Insights **Производительность** и выберите**Настройка**.
   
   ![Значок настройки](./media/enable-profiler-compute/enableprofiler1.png)
 
2. Выберите **Включить профилировщик**.
   
   ![Значок включения профилировщика](./media/enable-profiler-compute/enableprofiler2.png)

## <a name="add-a-performance-test-to-your-application"></a>Добавьте в приложение тест производительности
Выполните следующие инструкции, чтобы мы могли собирать примеры данных для отображения в Application Insights Profiler:

1. Найдите ресурс Application Insights, который вы создали ранее. 

2. Перейдите к колонке **Доступность** и добавьте тест производительности, который отправляет веб-запросы по URL-адресу приложения. 

   ![Добавление теста производительности](./media/enable-profiler-compute/AvailabilityTest.png)

## <a name="view-your-performance-data"></a>Просмотр данных о производительности

1. Подождите 10–15 минут, пока профилировщик соберет и проанализирует данные. 

2. Перейдите к колонке **Производительность** своего ресурса Application Insights и посмотрите, как приложение работает под нагрузкой.

   ![Просмотр данных производительности](./media/enable-profiler-compute/aiperformance.png)

3. Нажмите значок в разделе **Примеры** с открытой колонкой **Представление трассировки**.

   ![Открытие колонки "Представление трассировки"](./media/enable-profiler-compute/traceview.png)


## <a name="work-with-an-existing-template"></a>Работа с имеющимся шаблоном

1. Найдите объявление ресурса системы диагностики Azure в шаблоне развертывания.
   
   Если у вас нет объявления, можно создать его так же, как и объявление в примере ниже. Шаблон можно обновить на [веб-сайте обозревателя ресурсов Azure](https://resources.azure.com).

2. Измените данные издателя с `Microsoft.Azure.Diagnostics` на `AIP.Diagnostics.Test`.

3. Для `typeHandlerVersion` используйте `0.0`.

4. Убедитесь, что для `autoUpgradeMinorVersion` установлено значение `true`.

5. Добавьте новый экземпляр приемника `ApplicationInsightsProfiler` в объект параметров `WadCfg`, как показано в следующем примере:

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
Чтобы узнать, как включить профилировщик, скачайте шаблон [WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json). Примените те же изменения в шаблоне виртуальной машины к ресурсу расширения диагностики для масштабируемого набора виртуальных машин.

Убедитесь, что каждый экземпляр в наборе масштабирования имеет доступ к Интернету. Это необходимо, чтобы агент пофилировщика мог отправлять полученные примеры данных в Application Insights для отображения и анализа.

## <a name="enable-the-profiler-on-service-fabric-applications"></a>Включение профилировщика в приложении Service Fabric
1. Подготовьте кластер Service Fabric с расширением системы диагностики Azure, которое устанавливает агент профилировщика.

2. Установите в проект пакет SDK Application Insights и настройте ключ Application Insights.

3. Добавьте код приложения к телеметрии инструментирования.

### <a name="provision-the-service-fabric-cluster-to-have-the-azure-diagnostics-extension-that-installs-the-profiler-agent"></a>Подготовка кластера Service Fabric с расширением системы диагностики Azure, которое устанавливает агент профилировщика
Кластер Service Fabric может быть безопасным или небезопасным. Вы можете настроить один кластер шлюза как небезопасный, чтобы он не требовал сертификат для доступа. Кластеры, содержащие бизнес-логику и данные, должны быть безопасными. Вы можете включить профилировщик на безопасных и небезопасных кластерах Service Fabric. В этом пошаговом руководстве в качестве примера используется небезопасный кластер, чтобы объяснить, какие изменения необходимы для включения профилировщика. Таким же образом можно подготовить безопасный кластер.

1. Загрузите [ServiceFabricCluster.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json). Замените `Application_Insights_Key` своим ключом Application Insights так же, как для виртуальных машин и масштабируемых наборов виртуальных машин:

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

2. Разверните шаблон с помощью скрипта PowerShell:

   ```
   Login-AzureRmAccount
   New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
   New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

   ```

### <a name="install-the-application-insights-sdk-in-the-project-and-configure-the-application-insights-key"></a>Установка в проект пакета SDK Application Insights и настройка ключа Application Insights
Установите пакет SDK Application Insights из [пакета NuGet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Убедитесь, что устанавливается стабильная версия 2.3 или более поздняя. 

Сведения о настройке Application Insights в проектах см. в статье [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Использование Service Fabric с Application Insights).

### <a name="add-application-code-to-instrument-telemetry"></a>Добавление кода приложения к телеметрии инструментирования
1. Для любого фрагмента кода, который необходимо инструментировать, добавьте оператор using. 

   В следующем примере метод `RunAsync` выполняет некоторые действия, а класс `telemetryClient` регистрирует данные телеметрии сразу после запуска. Этому событию нужно уникальное имя по всему приложению.

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

2. Разверните приложение в кластер Service Fabric. Подождите, пока приложение будет выполняться в течение 10 минут. Для повышения эффекта в приложении можно выполнить нагрузочный тест. Перейдите к колонке **Производительность** портала Application Insights. Там должны отображаться примеры трассировки профилирования.

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

