---
title: Включение Application Insights Profiler для приложений, размещенных в ресурсах облачных служб Azure | Документация Майкрософт
description: Сведения о настройке Application Insights Profiler в приложении, запущенном в облачных службах Azure.
services: application-insights
documentationcenter: ''
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: ef6d8cb47971b1cf261edceb13485b940eff3d13
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-azure-cloud-services"></a>Включение Application Insights Profiler на виртуальных машинах Azure, в Service Fabric и облачных службах Azure

В этой статье показано, как включить Azure Application Insights Profiler в приложении ASP.NET, размещенном в ресурсе облачных служб Azure.

Примеры в этой статье предусматривают поддержку виртуальных машин Azure, масштабируемых наборов виртуальных машин Azure, Azure Service Fabric и облачных служб Azure. Для всех примеров используются шаблоны, которые поддерживают модель развертывания [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  


## <a name="overview"></a>Обзор

На схеме ниже показано, как Application Insights Profiler работает с приложениями, размещенными в ресурсах облачных служб Azure. К ресурсам облачных служб Azure относятся виртуальные машины, масштабируемые наборы, облачные службы и кластеры Service Fabric. На этом рисунке в качестве примера используется виртуальная машина Azure.  

  ![Схема, показывающая, как Application Insights Profiler работает с ресурсами облачных служб Azure](./media/enable-profiler-compute/overview.png)

Чтобы включить профилировщик, необходимо изменить конфигурацию в трех местах:

* Область экземпляра Application Insights на портале Azure.
* Исходный код приложения (например, веб-приложения ASP.NET).
* Исходный код определения развертывания среды (например, шаблон Azure Resource Manager в JSON-файле).


## <a name="set-up-the-application-insights-instance"></a>Настройка экземпляра Application Insights

1. [Создайте новый ресурс Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-create-new-resource) или выберите имеющийся. 

2. Перейдите к ресурсу Application Insights и скопируйте ключ инструментирования.

   ![Расположение ключа инструментирования](./media/enable-profiler-compute/CopyAIKey.png)

3. Чтобы завершить настройку экземпляра Application Insights для профилировщика, [включите профилировщик. Не нужно связывать веб-приложения, так как соответствующие инструкции предназначены для ресурса службы приложений. Включите профилировщик в области **настройки профилировщика**.


## <a name="set-up-the-application-source-code"></a>Настройка исходного кода приложения

### <a name="aspnet-web-applications-azure-cloud-services-web-roles-or-the-service-fabric-aspnet-web-front-end"></a>Веб-приложения ASP.NET, веб-роли облачных служб Azure или внешние веб-приложения ASP.NET в Service Fabric
Настройте приложение для отправки данных телеметрии в экземпляр Application Insights после каждой операции `Request`.  

Добавьте в проект приложения [пакет SDK для Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started). Убедитесь, что версии пакета NuGet совпадают с приведенными ниже:  
  - Для приложений ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) версии 2.3.0 и выше.
  - Для приложений ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) версии 2.1.0 и выше.
  - Для других приложений .NET и .NET Core (например, служба без отслеживания состояния Service Fabric, рабочая роль облачной службы): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) или [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) версии 2.3.0 и выше.  

### <a name="azure-cloud-services-worker-roles-or-the-service-fabric-stateless-back-end"></a>Рабочие роли облачных служб Azure или внутренние службы без отслеживания состояния Service Fabric
В дополнение к завершению предыдущего шага, если приложение *не* является приложением ASP.NET или ASP.NET Core (например, рабочие роли облачных служб Azure или API-интерфейсы без отслеживания состояния Service Fabric), сделайте следующее.  

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

### <a name="virtual-machines-scale-sets-or-service-fabric"></a>Виртуальные машины, масштабируемые наборы или Service Fabric

Полные примеры см. по следующим ссылкам.  
  * [Виртуальная машина](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Масштабируемый набор виртуальных машин](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Кластер Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Чтобы настроить среду, сделайте следующее:
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

> [!TIP]
> Альтернативой описанным выше действий с JSON для виртуальных машин является переход на портал Azure. Откройте **Виртуальные машины** > **Параметр диагностики** > **Приемники**. Затем **включите** отправку данных диагностики в Application Insights и выберите учетную запись Application Insights или определенный ключ.

### <a name="azure-cloud-services"></a>Облачные службы Azure

1. Чтобы использовать [.NET Framework версии 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) и выше, убедитесь, что в файлах *ServiceConfiguration.\*.cscfg* для параметра `osFamily` задано по меньшей мере значение 5.

2. Найдите файл *diagnostics.wadcfgx* [системы диагностики Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) для роли приложения, как показано ниже.  

   ![Расположение файла конфигурации диагностики](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

   Если не удается найти файл, см. статью [Настройка системы диагностики для облачных служб и виртуальных машин Azure](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them), чтобы узнать, как включить расширение диагностики в проекте облачных служб Azure.

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
> Если файл диагностики *diagnostics.wadcfgx* также содержит другой приемник типа `ApplicationInsights`, все три ключа инструментирования должны совпадать:  
>  * Ключ, используемый в приложении.  
>  * Ключ, используемый в приемнике `ApplicationInsights`.  
>  * Ключ, используемый в приемнике `ApplicationInsightsProfiler`.  
>
> Фактическое значения ключа инструментирования, используемое приемником `ApplicationInsights`, можно найти в файлах *ServiceConfiguration.\*.cscfg*.  
> После выпуска пакета SDK Azure для Visual Studio 15.5 должны совпадать только ключи инструментирования, используемые в приложении и приемнике `ApplicationInsightsProfiler`.


## <a name="configure-environment-deployment-and-runtime"></a>Настройка развертывания среды и среды выполнения

1. Разверните определение развертывания измененной среды.  

   Чтобы применить изменения, как правило, нужно полностью развернуть шаблон или опубликовать облачные службы с помощью командлетов PowerShell или Visual Studio.  

   Ниже приведен альтернативный подход для существующих виртуальных машин, который касается только расширения диагностики Azure.  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

2. Если нужное приложение выполняется с помощью [IIS](https://www.microsoft.com/web/platform/server.aspx), необходимо включить компонент Windows `IIS Http Tracing`, выполнив следующие действия:  

   a. Установите удаленный доступ к среде, а затем используйте окно [Добавить функции Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) или выполните следующую команду в PowerShell (с правами администратора):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   Б. Если не удается установить удаленный доступ, можно использовать [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), чтобы выполнить следующую команду:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-profiler-on-on-premises-servers"></a>Включение профилировщика на локальных серверах

Включение профилировщика на локальном сервере приравнивается к работе Application Insights Profiler в автономном режиме. Он не привязан к изменениям расширения системы диагностики Azure.

Мы не планируем официальную поддержку профилировщика для локальных серверов. Если вы хотите поэкспериментировать с этим сценарием, [ скачайте код поддержки](https://github.com/ramach-msft/AIProfiler-Standalone). Мы *не* несем ответственности за обслуживание этого кода и не предоставляем ответы на запросы по поводу проблем и функций, связанных с ним.

## <a name="next-steps"></a>Дополнительная информация

- Создайте трафик к приложению (например, запустите [тест доступности](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Подождите 10–15 минут, пока трассировки не начнут отправляться в экземпляр Application Insights.
- См. раздел [Включение профилировщика](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler).
- Сведения об устранении неполадок профилировщика см. в разделе [Устранение неполадок](app-insights-profiler.md#troubleshooting).
- Дополнительные сведения о профилировщике см. в статье [Профилирование динамических веб-приложений Azure с помощью Application Insights](app-insights-profiler.md).
