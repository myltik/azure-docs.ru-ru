---
title: "Включение Application Insights Profiler для приложений службы вычислений Azure | Документация Майкрософт"
description: "Узнайте, как настроить Profiler в приложении, запущенном в службе вычислений Azure."
services: application-insights
documentationcenter: 
author: ramach
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: b39b3b234112647ebeb531262d3b3876aee0b63b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2017
---
# <a name="enable-application-insights-profiler-on-azure-virtual-machines-service-fabric-and-cloud-services"></a>Включение Application Insights Profiler на виртуальных машинах Azure, в Service Fabric и облачных службах

В этом пошаговом руководстве показано, как включить Azure Application Insights Profiler в приложении ASP.NET, размещенном в ресурсе службы вычислений Azure.  
Примеры включают поддержку виртуальных машин Azure, масштабируемых наборов виртуальных машин Azure, Azure Service Fabric и облачных служб Azure.  
Для всех примеров используются шаблоны, которые поддерживают модель развертывания [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).  


## <a name="overview"></a>Обзор

На следующей схеме показано, как Profiler работает для ресурсов Azure. Виртуальная машина Azure используется в качестве примера.

![Обзор](./media/enable-profiler-compute/overview.png)


Чтобы полностью включить Profiler, необходимо выполнить настройку в трех местах:

1. колонка портала экземпляра Application Insights;
2. исходный код приложения (например, веб-приложение ASP.NET);
3. исходный код определения развертывания среды (например, JSON-файл шаблона развертывания виртуальной машины).


## <a name="configure-the-application-insights-instance"></a>Настройка экземпляра Application Insights

Создайте или посетите колонку портала Azure, чтобы найти экземпляр Application Insights, который необходимо использовать, и запишите его ключ инструментирования. Он пригодится при выполнении других шагов.

  ![Расположение ключа](./media/enable-profiler-compute/CopyAIKey.png)

Этот экземпляр должен совпадать с экземпляром, на который ваше приложение настроено отправлять данные телеметрии при каждом запросе.
Кроме того, результаты Profiler будут доступны в этом экземпляре.  

На портале Azure выполните действия, приведенные в разделе [Включение профилировщика](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler), чтобы завершить настройку экземпляра AI для Profiler. Вам не нужно связывать веб-приложения для целей этого пошагового руководства. Достаточно включить Profiler на портале.


## <a name="configure-the-application-source-code"></a>Настройка исходного кода приложения

Приложение должно быть настроено для отправки данных телеметрии в экземпляр Application Insights в каждой операции `Request`.  

1. Добавьте в проект приложения [пакет SDK для Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview#get-started). Убедитесь, что версии пакета NuGet совпадают с приведенными ниже:  
  - Для приложений ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) версии 2.3.0 или более поздней.
  - Для приложений ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 или более поздней версии.
  - Для других приложений .NET и .NET Core (например, служба без отслеживания состояния Service Fabric, рабочая роль облачной службы): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) или [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 или более поздней версии.  

2. Если приложение *не* является приложением ASP.NET или ASP.NET Core (например, рабочие роли облачных служб Microsoft Azure, API-интерфейсы без отслеживания состояния Service Fabric), потребуется следующая дополнительная настройка инструментирования:  

  2.1. Добавьте следующий код в раннюю точку во времени существования приложения.  

  ```csharp
  using Microsoft.ApplicationInsights.Extensibility;
  ...
  // Replace with your own Application Insights instrumentation key.
  TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
  ```
  Дополнительные сведения об этой глобальной конфигурации ключа инструментирования см. в статье [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Использование Service Fabric с Application Insights).  

  2.2. Для любого фрагмента кода, который необходимо инструментировать, добавьте оператор `StartOperation<RequestTelemetry>` `using`, как показано в следующем примере:

  ```csharp
  using Microsoft.ApplicationInsights;
  using Microsoft.ApplicationInsights.DataContracts;
  ...
  var client = new TelemetryClient();
  ...
  using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
  {
    // ... Code I want to profile.
  }
  ```

> [!NOTE]  
> Вызов `StartOperation<RequestTelemetry>` в другой области `StartOperation<RequestTelemetry>` не поддерживается. Вместо этого можно использовать `StartOperation<DependencyTelemetry>` во вложенной области. Пример:  

```csharp
using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
{
  try
  {
    ProductDetail details = new ProductDetail() { Id = productId };
    getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();

    // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
    using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
    {
        double price = await _priceDataBase.GetAsync(productId);
        if (IsTooCheap(price))
        {
            throw new PriceTooLowException(productId);
        }
        details.Price = price;
    }

    // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
    using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
    {
        details.Reviews = await _reviewDataBase.GetAsync(productId);
    }

    getDetailsOperation.Telemetry.Success = true;
    return details;
  }
  catch(Exception ex)
  {
    getDetailsOperation.Telemetry.Success = false;

    // This exception gets linked to the 'GetProductDetails' request telemetry.
    client.TrackException(ex);
    throw;
  }
}
```


## <a name="configure-the-environment-deployment-definition"></a>Настройка определения развертывания среды

Среда, где выполняется Profiler и ваше приложение, может быть виртуальной машиной, масштабируемым набором виртуальных машин, кластером Service Fabric или облачными службами Microsoft Azure.  

### <a name="virtual-machine-virtual-machine-scale-sets-or-service-fabric"></a>Виртуальная машина, масштабируемые наборы виртуальных машин или Service Fabric

Полные примеры:  
  * [Виртуальная машина](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [масштабируемый набор виртуальных машин](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json);
  * [кластер Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

1. Чтобы использовать [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) или более новой версии, развернутая ОС должна быть версии `Windows Server 2012 R2` или более поздней.

2. Найдите расширение [системы диагностики Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) в файле шаблона развертывания и добавьте следующий раздел `SinksConfig` в качестве дочернего элемента `WadCfg`, заменив значение свойства `ApplicationInsightsProfiler` на ваш собственный ключ инструментирования AI:  
```json
"SinksConfig": {
  "Sink": [
    {
      "name": "MyApplicationInsightsProfilerSink",
      "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
    }
  ]
}
```

Сведения о добавлении расширения диагностики в шаблон развертывания см. в примерах и [этом руководстве](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


### <a name="cloud-services"></a>Облачные службы

1. Чтобы использовать [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) или более новой версии, файлы `ServiceConfiguration.*.cscfg` должны содержать `osFamily` версии `"5"` или более поздней.

2. Найдите файл [системы диагностики Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) `diagnostics.wadcfgx` для роли приложения:  
![Расположение файла конфигурации диагностики](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
Если не удается найти файл, см. [это руководство](https://docs.microsoft.com/en-us/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them), чтобы узнать, как включить расширение диагностики в проекте облачных служб Azure.

3. Добавьте следующий раздел `SinksConfig` в качестве дочернего элемента `WadCfg`:  
  ```xml
  <WadCfg>
    <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
    <SinksConfig>
      <Sink name="MyApplicationInsightsProfiler">
        <!-- Replace with your own Application Insights instrumentation key. -->
        <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
      </Sink>
    </SinksConfig>
  </WadCfg>
  ```

> [!NOTE]  
> Если файл `diagnostics.wadcfgx` также содержит другой приемник типа `ApplicationInsights`, все три эти ключа инструментирования должны совпадать:  
>  * ключ инструментирования, используемый в приложении;  
>  * ключ инструментирования, используемый в приемнике `ApplicationInsights`;  
>  * ключ инструментирования, используемый в приемнике `ApplicationInsightsProfiler`.  
>
>Фактическое значение ключа инструментирования, используемого в приемнике `ApplicationInsights`, можно найти в файлах `ServiceConfiguration.*.cscfg`.  
>После выпуска пакета SDK Azure для Visual Studio 15.5 совпадать друг с другом должны только ключи инструментирования, используемые в приложении и приемнике `ApplicationInsightsProfiler`.


## <a name="environment-deployment-and-runtime-configurations"></a>Конфигурации развертывания среды и среды выполнения

1. Разверните определение развертывания измененной среды.  
Как правило, чтобы применить изменения, необходимо полное развертывание шаблона или публикация облачных служб с помощью командлетов PowerShell или Visual Studio.  
Ниже приведен альтернативный подход для имеющихся виртуальных машин, который `Virtual Machines` касается только их расширения диагностики:  
```powershell
$ConfigFilePath = [IO.Path]::GetTempFileName()
# After exporting the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
(Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
# Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
# if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
# (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
```

2. Если нужное приложение выполняется с помощью [IIS](https://www.microsoft.com/web/platform/server.aspx), необходимо включить компонент Windows `IIS Http Tracing`:  
  Установите удаленный доступ к среде, а затем используйте окно [Добавить функции Windows]( https://docs.microsoft.com/en-us/iis/configuration/system.webserver/tracing/) или выполните следующую команду в PowerShell (с правами администратора):  
  ```powershell
  Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
  ```  
  Если установить удаленный доступ не удается, можно использовать [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli), чтобы выполнить следующую команду:  
  ```powershell
  az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
  ```


## <a name="how-to-enable-profiler-on-on-premises-servers"></a>Как включить Profiler на локальных серверах

Этот процесс также называется запуском AI Profiler в автономном режиме (не привязан к изменениям расширения диагностики).  
Мы не планируем официальную поддержку Profiler для локальных серверов.
Если вы заинтересованы в экспериментах с этим сценарием, можно загрузить код поддержки [здесь](https://github.com/ramach-msft/AIProfiler-Standalone).  
Мы *не* несем ответственность за обслуживание этого кода и не предоставляем ответы на запросы по поводу проблем и функций, связанных с ним.


## <a name="next-steps"></a>Дальнейшие действия

- Создайте трафик в ваше приложение (например, запустите [тест доступности](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-monitor-web-app-availability)), а после этого подождите 10–15 минут для отправки трассировки в экземпляр Application Insights.

- См. раздел о [включении Profiler на портале Azure](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler).

- Дополнительные сведения об устранении неполадок Profiler см. в разделе [Устранение неполадок](app-insights-profiler.md#troubleshooting).

- Дополнительные сведения о Profiler см. в статье [Профилирование динамических веб-приложений Azure с помощью Application Insights](app-insights-profiler.md).
