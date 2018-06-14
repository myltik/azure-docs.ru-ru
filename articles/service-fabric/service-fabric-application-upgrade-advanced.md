---
title: Дополнительные разделы об обновлении приложений | Документация Майкрософт
description: В этой статье рассматриваются некоторые дополнительные темы, относящиеся к обновлению приложения Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 168d944f72c1409b5b69c9ab7c07f7fcfa04c7c8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212329"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Обновление приложений Service Fabric: дополнительные разделы
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Добавление или удаление типов служб во время обновления приложения
Если в приложение, которое опубликовано как обновление, добавляется новый тип службы, то он добавляется в развернутое приложение. Подобное обновление не влияет на какие-либо экземпляры служб, которые уже были частью приложения. Однако чтобы активировать новый тип службы, необходимо создать экземпляр типа службы, который был добавлен (см. статью о командлете [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Точно так же типы служб могут быть удалены из приложения. Но в этом случае перед продолжением обновления все текущие экземпляры удаляемой службы должны быть удалены (см. статью о командлете [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Ручной режим обновления
> [!NOTE]
> Для всех обновлений Service Fabric рекомендуется использовать *отслеживаемый* (Monitored) режим обновления.
> Режим *UnmonitoredManual* (неотслеживаемый, ручной) следует использовать только для сбойных или отложенных обновлений. 
>
>

В *отслеживаемом* режиме Service Fabric применяет политики работоспособности, чтобы гарантировать, что в процессе обновления приложение находится в работоспособном состоянии. Если политики работоспособности нарушены, обновление приостанавливается или автоматически откатывается. Это зависит от указанного атрибута *FailureAction*.

В режиме *UnmonitoredManual* администратор приложения полностью контролирует процесс обновления. Этот режим удобен, когда применяются настраиваемые политики оценки работоспособности или выполняется нестандартное обновление, чтобы полностью обойти проверки работоспособности (например, приложение уже потеряло некоторые данные). Обновление, выполняющееся в этом режиме, самостоятельно приостанавливается после применения обновлений к каждому домену обновления. Его необходимо возобновить с помощью командлета [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Когда обновление приостановлено и готово к возобновлению, его состояние изменится на *RollforwardPending* (Ожидание наката) (см. статью о команде [ApplicationUpgradeState Enum](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Наконец, режим *UnmonitoredAuto* (неотслеживаемый, автоматический) подходит для выполнения быстрых итераций обновления во время разработки или тестирования служб, так как пользовательский ввод не требуется, а политики работоспособности приложения не оцениваются.

## <a name="upgrade-with-a-diff-package"></a>Обновление при помощи пакета diff
Обновления могут выполняться путем подготовки пакетов разностных изменений (вместо полных пакетов приложений), содержащих только обновленные пакеты кода, конфигурации или данных, а также полные манифесты приложений и служб. Для первичной установки приложений в кластер необходимы полные пакеты приложений. При последующих обновлениях могут использоваться либо полные пакеты приложений, либо пакеты разностных изменений (diff).  

Любая ссылка в манифесте приложения или манифесте служб пакета diff, которую невозможно найти в пакете приложения, автоматически заменяется текущей подготовленной версией.

Использование пакета diff оптимально в следующих случаях:

* Когда используется объемный пакет приложения, который ссылается на несколько файлов манифеста службы или на несколько пакетов кода, пакетов конфигурации или пакетов данных.
* Когда система развертывания формирует структуру сборки напрямую в ходе создания сборки приложения. В этом случае, хотя в самом коде ничего не изменилось, новые сборки будут обладать другой контрольной суммой. Использование полного пакета приложения потребует обновления версии всех пакетов кода. При использовании пакета diff вы предоставляете только измененные файлы и файлы манифеста, версия которых изменилась.

При обновлении приложения с помощью Visual Studio пакет diff публикуется автоматически. Чтобы создать пакет diff вручную, необходимо обновить манифест приложения и манифесты служб, но только измененные пакеты должны быть включены в финальный пакет приложения.

К примеру, начнем со следующего приложения (номера версий приведены для простоты понимания):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Предположим, что необходимо обновить только код пакета service1, использующего пакет diff. Обновленное приложение включает следующие изменения версии:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

В этом случае вы обновляете манифест приложения до версии 2.0.0 и манифест службы для service1 для отражения обновления пакета кода. Структура папок для пакета приложения будет выглядеть следующим образом.

```text
app1/
  service1/
    code/
```

Другими словами, создайте полный пакет приложения, а затем удалите все папки пакета кода, конфигурации или данных, для которых версия не изменилась.

## <a name="rolling-back-application-upgrades"></a>Откат обновлений приложений

Хотя обновления можно выполнить в одном из трех режимов (*Monitored*, *UnmonitoredAuto* или *UnmonitoredManual*), откатить их можно только в режиме *UnmonitoredAuto* или *UnmonitoredManual*. Откат в режиме *UnmonitoredAuto* работает так же, как накат. Единственное исключение: другое значение по умолчанию *UpgradeReplicaSetCheckTimeout* (см. статью [Параметры обновления приложений](service-fabric-application-upgrade-parameters.md)). Откат в режиме *UnmonitoredManual* работает так же, как и накат. Он приостанавливается после завершения работы с каждым доменом обновления. Возобновить его можно с помощью командлета [ Resume-ServiceFabricApplicationUpgrade ](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps).

Откат может запускаться автоматически, если нарушены политики работоспособности обновления в режиме *Monitored* с указанным для *отката* атрибутом *FailureAction* , (см. статью [Параметры обновления приложения](service-fabric-application-upgrade-parameters.md)) или если используется командлет [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Во время отката все еще можно изменить значение *UpgradeReplicaSetCheckTimeout*. Режим также можно изменить в любое время с помощью командлета [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Дополнительная информация
[Руководство по обновлению приложений Service Fabric с помощью Visual Studio](service-fabric-application-upgrade-tutorial.md) поможет вам выполнить поэтапное обновление приложения с помощью Visual Studio.

[Обновление приложения с помощью PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) поможет вам выполнить обновление приложения с помощью PowerShell.

Управление обновлениями приложения осуществляется с помощью [параметров обновления](service-fabric-application-upgrade-parameters.md).

Узнайте, как использовать [сериализацию данных](service-fabric-application-upgrade-data-serialization.md), чтобы обеспечить совместимость обновлений приложения.

Сведения об устранении распространенных проблем при обновлении приложений см. в статье [Устранение неполадок при обновлениях приложений](service-fabric-application-upgrade-troubleshooting.md).
