---
title: "Включение Application Insights Profiler для приложений службы вычислений Azure | Документация Майкрософт"
description: "Узнайте, как настроить Application Insights Profiler в приложении, запущенном в службе вычислений Azure."
services: application-insights
documentationcenter: 
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: 57a4cb560825e0c05ac49df26ac12ee52da52c3c
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-cloud-services"></a>Включение Application Insights Profiler на виртуальных машинах Azure, в Service Fabric и облачных службах

В этой статье показано, как включить Azure Application Insights Profiler в приложении ASP.NET, размещенном в ресурсе службы вычислений Azure. 

Примеры в этой статье предусматривают поддержку виртуальных машин Azure, масштабируемых наборов виртуальных машин Azure, Azure Service Fabric и облачных служб Azure. Для всех примеров используются шаблоны, которые поддерживают модель развертывания [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  


## <a name="overview"></a>Обзор

На рисунке ниже показано, как профилировщик Application Insights работает с ресурсами Azure. На этом рисунке в качестве примера используется виртуальная машина Azure.

  ![Обзор](./media/enable-profiler-compute/overview.png)

Чтобы включить профилировщик, необходимо изменить конфигурацию в трех местах:

* Область экземпляра Application Insights на портале Azure.
* Исходный код приложения (например, веб-приложения ASP.NET).
* Исходный код определения развертывания среды (например, JSON-файл шаблона развертывания виртуальной машины).


## <a name="set-up-the-application-insights-instance"></a>Настройка экземпляра Application Insights

На портале Azure создайте экземпляр Application Insights или перейдите к экземпляру, который вы хотите использовать. Запишите ключ инструментирования экземпляра. Ключ инструментирования будет использоваться в других шагах настройки.

  ![Расположение ключа инструментирования](./media/enable-profiler-compute/CopyAIKey.png)

Этот экземпляр должен быть таким же, как приложение. Он настраивается для отправки данных телеметрии после каждого запроса.
Результаты профилировщика также будут доступны в этом экземпляре.  

На портале Azure выполните действия, описанные в разделе [Включение профилировщика](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler), чтобы завершить настройку экземпляра Application Insights для профилировщика. Для работы с примером в этой статье не нужно связывать веб-приложения. Просто включите профилировщик на портале.


## <a name="set-up-the-application-source-code"></a>Настройка исходного кода приложения

Настройте приложение для отправки данных телеметрии в экземпляр Application Insights после каждой операции `Request`.  

1. Добавьте в проект приложения [пакет SDK для Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started). Убедитесь, что версии пакета NuGet совпадают с приведенными ниже:  
  - Для приложений ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) версии 2.3.0 и выше.
  - Для приложений ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) версии 2.1.0 и выше.
  - Для других приложений .NET и .NET Core (например, служба без отслеживания состояния Service Fabric, рабочая роль облачной службы): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) или [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) версии 2.3.0 и выше.  

2. Если приложение *не* является приложением ASP.NET или ASP.NET Core (например, рабочие роли облачных служб Microsoft Azure, API-интерфейсы без отслеживания состояния Service Fabric), потребуется следующая дополнительная настройка инструментирования:  

  1. Добавьте следующий код в раннюю точку во времени существования приложения:  

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    ...
    // Replace with your own Application Insights instrumentation key.
    TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
    ```
  Дополнительные сведения об этой глобальной конфигурации ключа инструментирования см. в статье [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Использование Service Fabric с Application Insights).  

  2. Для всех фрагментов кода, которые необходимо инструментировать, добавьте оператор `StartOperation<RequestTelemetry>` **USING**, как показано в следующем примере:

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

  Вызов `StartOperation<RequestTelemetry>` в другой области `StartOperation<RequestTelemetry>` не поддерживается. Вместо этого можно использовать `StartOperation<DependencyTelemetry>` во вложенной области. Например:   

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


## <a name="set-up-the-environment-deployment-definition"></a>Настройка определения развертывания среды

Среда, в которой выполняется профилировщик и ваше приложение, может быть виртуальной машиной, масштабируемым набором виртуальных машин, кластером Service Fabric или экземпляром облачных служб.  

### <a name="virtual-machines-virtual-machine-scale-sets-or-service-fabric"></a>Виртуальная машина, масштабируемые наборы виртуальных машин или Service Fabric

Полные примеры:  
  * [Виртуальная машина](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Масштабируемый набор виртуальных машин](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Кластер Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. Чтобы использовать [.NET Framework версии 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) и выше, разверните ОС версии `Windows Server 2012 R2` и выше.

2. Найдите расширение [системы диагностики Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) в файле шаблона развертывания и добавьте следующий раздел `SinksConfig` в качестве дочернего элемента `WadCfg`. Замените значение свойства `ApplicationInsightsProfiler` собственным ключом инструментирования Application Insights:  
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

  Сведения о добавлении расширения диагностики в шаблон развертывания см. в статье [Использование мониторинга и системы диагностики с виртуальной машиной Windows и шаблонами Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


### <a name="cloud-services"></a>Облачные службы

1. Чтобы использовать [.NET Framework версии 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) и вышеи, убедитесь, что в файлах ServiceConfiguration.\*.cscfg параметр `osFamily` имеет как минимум значение **5**.

2. Найдите файл diagnostics.wadcfgx [системы диагностики Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) для роли приложения:  
  ![Расположение файла конфигурации диагностики](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
  Если не удается найти файл, см. статью [Настройка системы диагностики для облачных служб и виртуальных машин Azure](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them), чтобы узнать, как включить расширение диагностики в проекте облачных служб.

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
> Если файл `diagnostics.wadcfgx` также содержит другой приемник типа `ApplicationInsights`, все три ключа инструментирования должны совпадать:  
>  * ключ инструментирования, используемый в приложении;  
>  * ключ инструментирования, используемый в приемнике `ApplicationInsights`;  
>  * ключ инструментирования, используемый в приемнике `ApplicationInsightsProfiler`.  
>
> Фактическое значения ключа инструментирования, используемое приемником `ApplicationInsights`, можно найти в файлах ServiceConfiguration.\*.cscfgg.  
> После выпуска пакета SDK Azure для Visual Studio 15.5 должны совпадать только ключи инструментирования, используемые в приложении и приемнике `ApplicationInsightsProfiler`.


## <a name="environment-deployment-and-runtime-configurations"></a>Конфигурации развертывания среды и среды выполнения

1. Разверните определение развертывания измененной среды.  

  Чтобы применить изменения, нужно полностью развернуть шаблон или опубликовать облачные службы с помощью командлетов PowerShell или Visual Studio.  

  Ниже приведен альтернативный подход для существующих виртуальных машин, который касается только их расширения диагностики Azure:  
  ```powershell
  $ConfigFilePath = [IO.Path]::GetTempFileName()
  # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
  (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
  # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
  # if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
  # (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
  Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
  ```

2. Если нужное приложение выполняется с помощью [IIS](https://www.microsoft.com/web/platform/server.aspx), необходимо включить компонент Windows `IIS Http Tracing`:  
  
  1. Установите удаленный доступ к среде, а затем используйте окно [Добавить функции Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) или выполните следующую команду в PowerShell (с правами администратора):  
    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
  2. Если не удается установить удаленный доступ, можно использовать [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), чтобы выполнить следующую команду:  
    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-the-profiler-on-on-premises-servers"></a>Включение профилировщика на локальных серверах

Включение профилировщика на локальном сервере приравнивается к работе Application Insights Profiler в автономном режиме (он не привязан к изменениям расширения системы диагностики Azure). 

Мы не планируем официальную поддержку профилировщика для локальных серверов. Если вы хотите поэкспериментировать с этим сценарием, [ скачайте код поддержки](https://github.com/ramach-msft/AIProfiler-Standalone). Мы *не* несем ответственность за обслуживание этого кода и не предоставляем ответы на запросы по поводу проблем и функций, связанных с ним.

## <a name="next-steps"></a>Дальнейшие действия

- Создайте трафик к приложению (например, запустите [тест доступности](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Подождите 10–15 минут, пока трассировки не начнут отправляться в экземпляр Application Insights.
- См. раздел [Включение профилировщика](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler).
- Сведения об устранении неполадок профилировщика см. в разделе [Устранение неполадок](app-insights-profiler.md#troubleshooting).
- Дополнительные сведения о профилировщике см. в статье [Профилирование динамических веб-приложений Azure с помощью Application Insights](app-insights-profiler.md).
