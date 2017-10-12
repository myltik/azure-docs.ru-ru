---
title: "Обзор Azure Cloud Shell (предварительная версия) | Документация Майкрософт"
description: "Обзор Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 44d2ac2fd35621ab8cd8d7584744139ee3bab5c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-cloud-shell-preview"></a>Обзор Azure Cloud Shell (предварительная версия)
Azure Cloud Shell — это интерактивная доступная браузеру оболочка для управления ресурсами Azure.
Она предоставляет гибкие возможности при выборе оболочки, соответствующей вашим методам работы.
Пользователи Linux могут использовать Bash, а пользователи Windows — PowerShell.

![Bash в Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell в Cloud Shell](media/overview/overview-ps-pic.png)

## <a name="features"></a>Функции
### <a name="browser-based-shell-experience"></a>Оболочка на основе браузера
Cloud Shell предоставляет доступ к браузерному интерфейсу командной строки, созданному с учетом задач управления Azure.
Используйте Cloud Shell для работы из локального компьютера с неограниченными возможностями, которые может обеспечить только облако.

### <a name="choice-of-preferred-shell-experience"></a>Выбор предпочтительной оболочки
Azure Cloud Shell предоставляет гибкие возможности при выборе оболочки, соответствующей вашим методам работы.
Пользователи Linux могут использовать Bash, а пользователи Windows — PowerShell.

### <a name="pre-configured-azure-workstation"></a>Предварительно настроенная рабочая станция Azure
Cloud Shell поставляется с предварительно установленными популярными инструментами командной строки и поддержкой языков, чтобы вы могли работать быстрее.

Просмотрите полный список инструментов для работы с [Bash](features.md#tools) и [PowerShell](features-powershell.md#tools).

### <a name="automatic-authentication"></a>Автоматическая проверка подлинности
Cloud Shell автоматически безопасно проходит проверку подлинности при каждом сеансе, чтобы обеспечить мгновенный доступ к ресурсам через Azure CLI 2.0.

### <a name="connect-your-azure-file-storage"></a>Подключение хранилища файлов Azure
Машины Cloud Shell являются временными, поэтому требуют подключения общей папки Azure как `clouddrive` для хранения домашнего каталога.
При первом запуске Cloud Shell предлагает создать группу ресурсов, учетную запись хранения и общую папку от вашего имени. Это одноразовое действие, которое автоматически применяется для всех сеансов. Можно сопоставить отдельный файловый ресурс, который будет использоваться Bash и PowerShell в Cloud Shell.

#### <a name="create-new-storage"></a>Создание хранилища
![](media/overview/basic-storage.png)

Учетная запись локально избыточного хранилища (LRS) и файловый ресурс Azure могут быть созданы от вашего имени. Файловый ресурс Azure будет использоваться для сред Bash и PowerShell, если вы решите использовать обе эти среды. Применяются расходы на обычное хранение.

От вашего имени будет создано три ресурса:
1. Группа ресурсов с именем: `cloud-shell-storage-<region>`
2. Учетная запись хранения с именем: `cs<uniqueGuid>`
3. Общая папка с именем: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Bash в Cloud Shell также создает образ диска по умолчанию размером в 5 ГБ для хранения `$Home`. Все файлы в каталоге $Home, такие как ключи SSH, сохраняются в образе диска пользователя, который хранится в подключенной общей папке. Соблюдайте рекомендации при сохранении файлов в каталоге $Home и подключенной общей папке.

#### <a name="use-existing-resources"></a>Использование существующих ресурсов
![](media/overview/advanced-storage.png)

В Cloud Shell доступна дополнительная возможность, которая позволяет связывать существующие ресурсы с Cloud Shell.
При появлении запроса на настройку хранилища щелкните "Показать дополнительные настройки", чтобы отобразить дополнительные параметры.
Раскрывающиеся меню фильтруются с учетом назначенного региона Cloud Shell и учетных записей локально или глобально избыточных хранилищ.

[Дополнительные сведения о хранилище Cloud Shell, обновлении файловых ресурсов, а также о передаче и скачивании файлов](persisting-shell-storage.md).

## <a name="concepts"></a>Основные понятия
* Cloud Shell работает на временной машине, предоставляемой для каждого сеанса и для каждого пользователя отдельно.
* Время ожидания Cloud Shell истекает через 20 минут при отсутствии интерактивных действий.
* Доступ к Cloud Shell можно получить только при наличии присоединенной общей папки.
* Cloud Shell использует один и тот же файловый ресурс для Bash и PowerShell.
* Cloud Shell назначается один компьютер на учетную запись пользователя.
* Разрешения задаются как для обычного пользователя Linux (Bash).

Узнайте больше о возможностях [Bash в Cloud Shell](features.md) и [PowerShell в Cloud Shell](features-powershell.md).

## <a name="examples"></a>Примеры
* Создание сценариев для автоматизации задач управления Azure
* Одновременное управление ресурсами с помощью портала Azure и программ командной строки Azure.
* Испытание Azure CLI 2.0 или командлетов Azure PowerShell.

Опробуйте приведенные примеры в кратких руководствах для [Bash в Cloud Shell](quickstart.md) и [PowerShell в Cloud Shell](quickstart-powershell.md).

## <a name="pricing"></a>Цены
За использование компьютера, на котором размещена среда Cloud Shell, плата не взимается. На этом компьютере должен быть подключен файловый ресурс Azure. Применяются расходы на обычное хранение.

## <a name="supported-browsers"></a>Поддерживаемые браузеры
Cloud Shell рекомендуется использовать с Chrome, Edge и Safari.
Хотя служба Cloud Shell и поддерживается Chrome, Firefox, Safari, IE и Edge, она все еще зависит от определенных параметров браузера.