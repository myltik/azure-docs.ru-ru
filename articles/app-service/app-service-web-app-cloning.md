---
title: Клонирование веб-приложения с помощью PowerShell
description: Узнайте, как клонировать веб-приложения, создавая новые веб-приложения, с помощью PowerShell.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.openlocfilehash: 30817a1a6a8079e7a896305ab0b59e48fad4d644
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2018
ms.locfileid: "27867478"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Клонирование приложений службы приложений Azure с помощью PowerShell
С выходом Microsoft Azure PowerShell версии 1.1.0 был добавлен новый параметр `New-AzureRMWebApp`, позволяющий клонировать существующее веб-приложение во вновь созданное приложение, размещенное в том же или в другом регионе. Так пользователи смогут легко и быстро развернуть целый ряд приложений в различных регионах.

В настоящее время клонирование приложений поддерживается только в планах службы приложений уровня Premium. В новой функции действуют те же ограничения, что и в функции архивации веб-приложений (см. статью [Резервное копирование веб-приложений в службе приложений Azure](web-sites-backup.md)).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Клонирование существующего приложения
Сценарий. Существует веб-приложение в южно-центральном регионе США; его содержимое необходимо клонировать в новое веб-приложение в северо-центральном регионе США. Это можно решить с помощью командлета PowerShell на основе Azure Resource Manager для создания веб-приложения с параметром `-SourceWebApp`.

Зная имя группы ресурсов, содержащей исходное веб-приложение, можно выполнить следующую команду PowerShell для получения данных исходного веб-приложения (в данном случае оно называется `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Чтобы создать план службы приложений, можно выполнить команду `New-AzureRmAppServicePlan`, как показано в следующем примере:

```PowerShell
New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

С помощью команды `New-AzureRmWebApp` создайте веб-приложение в северо-центральном регионе США и привяжите его к имеющемуся плану службы приложений уровня "Премиум". Более того, можно использовать в качестве исходного веб-приложения ту же группу ресурсов или определить новую:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Для создания точной копии существующего веб-приложения, включая все соответствующие слоты развертывания, пользователю необходимо будет задать параметр `IncludeSourceWebAppSlots`. Следующая команда PowerShell показывает, как использовать этот параметр с командой `New-AzureRmWebApp`:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Для создания точной копии существующего веб-приложения в том же регионе вам нужно будет создать новую группу ресурсов и новый план службы приложений в том же регионе, а затем клонировать веб-приложение с помощью следующей команды PowerShell:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Клонирование существующего приложения в среду службы приложений
Сценарий. Существует веб-приложение в южно-центральном регионе США, содержимое которого необходимо клонировать в новое веб-приложение в существующую среду службы приложений (ASE).

Зная имя группы ресурсов, содержащей исходное веб-приложение, можно выполнить следующую команду PowerShell для получения данных исходного веб-приложения (в данном случае оно называется `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Зная имя ASE и имя группы ресурсов, к которой относится ASE, можно создать веб-приложение в существующей ASE, выполнив следующую команду:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

В связи с обратной совместимостью параметр `Location` является обязательным, однако при создании приложения в ASE он игнорируется. 

## <a name="cloning-an-existing-app-slot"></a>Клонирование существующего слота приложения
Сценарий. Вам необходимо клонировать существующий слот веб-приложения в новое веб-приложение или в новый слот веб-приложения. Новое веб-приложение может размещаться в том же регионе, что и исходный слот, или в другом.

Зная имя группы ресурсов, содержащей исходное веб-приложение, можно выполнить следующую команду PowerShell для получения данных исходного слота веб-приложения (в данном случае оно называется `source-webappslot`), привязанного к веб-приложению `source-webapp`:

```PowerShell
$srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot
```

Следующая команда демонстрирует создание точной копии исходного веб-приложения в новом веб-приложении:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Настройка диспетчера трафика при клонировании приложения
Создание мультирегиональных веб-приложений и настройка в диспетчере трафика Azure маршрутизации трафика ко всем этим веб-приложениям — это важный сценарий, обеспечивающий высокую доступность приложений клиентов. Клонируя существующее веб-приложение, вы можете подключить оба веб-приложения к новому или существующему профилю диспетчера трафика. Обратите внимание, что поддерживается только версия диспетчера трафика на основе Azure Resource Manager.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Создание профиля диспетчера трафика при клонировании приложения
Сценарий. Вам необходимо клонировать веб-приложение в другой регион и настроить профиль диспетчера трафика Azure Resource Manager так, чтобы он включал оба веб-приложения. Следующая команда демонстрирует создание точной копии исходного веб-приложения в новом веб-приложении с настройкой нового профиля диспетчера трафика:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Добавление клонированного веб-приложения в существующий профиль диспетчера трафика
Сценарий. У вас уже есть профиль диспетчера трафика Azure Resource Manager, вам необходимо добавить оба веб-приложения в качестве конечных точек. Для этого нужно собрать идентификатор профиля диспетчера трафика. Кроме того, потребуется идентификатор подписки, имя группы ресурсов и имя имеющегося профиля диспетчера трафика.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Следующая команда показывает, как создать точную копию исходного веб-приложения в новом веб-приложении и добавить эти веб-приложения в существующий профиль диспетчера трафика, зная идентификатор диспетчера трафика:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Существующие ограничения
Эта функция в настоящее время находится в предварительной версии, со временем появятся новые возможности. Ниже приведены известные ограничения текущей версии приложения для клонирования:

* Параметры автоматического масштабирования не клонируются
* Параметры расписания резервного копирования не клонируются
* Параметры виртуальных сетей не клонируются
* App Insights не настраивается в целевом веб-приложении автоматически
* Параметры простой авторизации не клонируются
* Расширение Kudu не клонируется
* Правила TiP не клонируются
* Содержимое базы данных не клонируется.
* При клонировании в другую единицу масштабирования исходящие IP-адреса изменяются.

### <a name="references"></a>Ссылки
* [Клонирование приложений службы приложений Azure с помощью PowerShell](app-service-web-app-cloning.md)
* [Резервное копирование веб-приложений в службе приложений Azure](web-sites-backup.md)
* [Предварительная поддержка диспетчера трафика Azure в диспетчере ресурсов Azure](../traffic-manager/traffic-manager-powershell-arm.md)
* [Введение в среду службы приложения](environment/intro.md)
* [Использование Azure PowerShell с диспетчером ресурсов Azure](../azure-resource-manager/powershell-azure-resource-manager.md)

