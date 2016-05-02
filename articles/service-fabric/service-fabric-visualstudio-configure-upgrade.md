<properties
   pageTitle="Настройка обновления приложения Service Fabric | Microsoft Azure"
   description="Узнайте, как настроить параметры обновления приложения Service Fabric с помощью Microsoft Visual Studio."
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />
<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="04/14/2016"
   ms.author="cawaMS" />

# Настройка обновления приложения Service Fabric в Visual Studio

Средства Visual Studio для Azure Service Fabric обеспечивают поддержку обновления для публикации на локальных и удаленных кластерах. У обновления приложения до более новой версии есть два преимущества по сравнению с заменой приложения во время тестирования и отладки:

- данные приложения не будут потеряны во время обновления;
- достигается высокий уровень доступности, так как не будет перерывов в доступности службы по время обновления, если по доменам обновления распределено достаточное количество экземпляров службы.

Тесты для приложения могут запускаться во время его обновления.

## Параметры, необходимые для обновления

Существует два типа развертывания: обычное или обновление. При обычном развертывании стираются все предыдущие сведения о развертывании и данные в кластере, а при обновлении они сохраняются. При обновлении приложения Service Fabric в Visual Studio необходимо указать параметры обновления приложения и политики проверки работоспособности. Параметры обновления приложения помогают управлять обновлением, а политики проверки работоспособности определяют, было обновление успешным или нет. Дополнительные сведения см. в разделе [Обновление приложений Service Fabric: параметры обновления](service-fabric-application-upgrade-parameters.md).

Существует три режима обновления: *отслеживаемое*, *неотслеживаемое автоматическое* и *ручное*.

  - При отслеживаемом обновлении процесс обновления и проверка работоспособности приложения автоматизируются.

  - При неотслеживаемом автоматическом обновлении процесс обновления автоматизируется, но проверка работоспособности приложения пропускается.

  - При выполнении неотслеживаемого ручного обновления необходимо вручную обновить каждый домен обновления.

Для каждого режима обновления необходим различный набор параметров. Для получения дополнительных сведений о доступных параметрах обновления см. раздел [Параметры обновления приложений](service-fabric-application-upgrade-parameters.md).

## Обновление приложения Service Fabric в Visual Studio

При использовании средств Service Fabric Visual Studio для обновления приложения Service Fabric можно указать, что процесс публикации будет подразумевать обновление, а не обычное развертывание, установив флажок **Обновить приложение**.

### Настройка параметров обновления

1. Нажмите кнопку **Параметры** рядом с флажком. Откроется диалоговое окно **Изменение параметров обновления**. Диалоговое окно **Изменение параметров обновления** поддерживает отслеживаемое, неотслеживаемое автоматическое и ручное обновления.

2. Выберите режим обновления, который хотите использовать, и заполните сетку параметров.

    Каждый параметр имеет значения по умолчанию. Необязательный параметр *DefaultServiceTypeHealthPolicy* принимает входную хэш-таблицу. Ниже приведен пример формата входной хэш-таблицы для *DefaultServiceTypeHealthPolicy*:

	```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
	```

    *ServiceTypeHealthPolicyMap* — еще один необязательный параметр, который принимает входную хэш-таблицу в следующем формате:

	```    
	@ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
	```

    Ниже приведен пример из реальной жизни:

    ```
	@{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
	```

3. При выборе неотслеживаемого автоматического обновления необходимо будет вручную запустить консоль PowerShell для продолжения и завершения процесса обновления. Чтобы узнать, как работает ручное обновление, обратитесь к разделу [Обновление приложения Service Fabric: дополнительные разделы](service-fabric-application-upgrade-advanced.md).

## Обновление приложения с помощью PowerShell

Обновить приложение Service Fabric можно с помощью командлетов PowerShell. Подробные сведения см. в разделах [Учебник по обновлению приложений Service Fabric](service-fabric-application-upgrade-tutorial.md) и [Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx).

## Задание политики проверки работоспособности в файле манифеста приложения

Каждая служба в приложении Service Fabric может иметь собственные параметры политики работоспособности, которые переопределяют значения по умолчанию. Эти значения параметров можно указать в файле манифеста приложения.

В следующем примере показано, как применить уникальную политику проверки работоспособности для каждой службы в манифесте приложения.

```
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## Дальнейшие действия
Дополнительные сведения о развертывании приложений см. в разделе [Развертывание существующего приложения в Azure Service Fabric](service-fabric-deploy-existing-app.md).

<!---HONumber=AcomDC_0420_2016-->