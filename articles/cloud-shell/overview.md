---
title: "Обзор Azure Cloud Shell | Документация Майкрософт"
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
ms.date: 11/16/2017
ms.author: juluk
ms.openlocfilehash: 08ab3b38e4c1fbeb1fac67c5d1b6f6749f7a0a3e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2017
---
# <a name="overview-of-azure-cloud-shell"></a>Обзор Azure Cloud Shell
Azure Cloud Shell — это интерактивная доступная браузеру оболочка для управления ресурсами Azure.
Она предоставляет гибкие возможности при выборе оболочки, соответствующей вашим методам работы.
Пользователи Linux могут использовать Bash, а пользователи Windows — PowerShell.

Выполните запуск через портал Azure с помощью значка Cloud Shell.

![Запуск с помощью портала](media/overview/portal-launch-icon.png)

В раскрывающемся списке селектора оболочки выберите "Bash" или "PowerShell".

![Bash в Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell в Cloud Shell (предварительная версия)](media/overview/overview-ps-pic.png)

## <a name="features"></a>Функции
### <a name="browser-based-shell-experience"></a>Оболочка на основе браузера
Cloud Shell предоставляет доступ к браузерному интерфейсу командной строки, созданному с учетом задач управления Azure.
Используйте Cloud Shell для работы из локального компьютера с неограниченными возможностями, которые может обеспечить только облако.

### <a name="choice-of-preferred-shell-experience"></a>Выбор предпочтительной оболочки
Azure Cloud Shell предоставляет гибкие возможности при выборе оболочки, соответствующей вашим методам работы.
Пользователи Linux могут использовать Bash в Cloud Shell, тогда как пользователи Windows могут предпочесть PowerShell в Cloud Shell (предварительная версия).

### <a name="authenticated-and-configured-azure-workstation"></a>Настроенная и аутентифицированная рабочая станция Azure
Службой Cloud Shell управляет Майкрософт, поэтому в ней изначально присутствуют популярные инструменты командной строки и поддержка различных языков, что позволяет работать быстрее. Кроме того, Cloud Shell автоматически и безопасно проходит аутентификацию, тем самым обеспечивая мгновенный доступ к ресурсам с помощью Azure CLI 2.0 или командлетов Azure PowerShell.

Просмотрите полный список инструментов для работы с [Bash](features.md#tools) и [PowerShell (предварительная версия)](features-powershell.md#tools).

### <a name="multiple-access-points"></a>Доступность в нескольких расположениях
Помимо портала Azure, служба Cloud Shell доступна также в таких местах:
* [ознакомительная документация по Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest);
* [мобильные приложения Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/);
* [расширение Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

### <a name="connect-your-azure-files-storage"></a>Подключение хранилища файлов Azure
Компьютеры Cloud Shell являются временными, поэтому требуют подключения файлового ресурса Azure как `clouddrive` для хранения каталога $Home.
При первом запуске Cloud Shell предлагает создать группу ресурсов, учетную запись хранения и общую папку от вашего имени. Это одноразовое действие, которое автоматически применяется для всех сеансов. Вы можете подключить один файловый ресурс, который будет использоваться как при работе с Bash, так и при работе с PowerShell в Cloud Shell (предварительная версия).

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
* Cloud Shell работает на временном узле, предоставляемом для каждого сеанса и для каждого пользователя отдельно.
* Время ожидания Cloud Shell истекает через 20 минут при отсутствии интерактивных действий.
* Для Cloud Shell требуется подключение файлового ресурса.
* Cloud Shell использует один и тот же файловый ресурс для Bash и PowerShell.
* Cloud Shell назначается один компьютер на учетную запись пользователя.
* Разрешения задаются как для обычного пользователя Linux в Bash.

Узнайте больше о возможностях [Bash в Cloud Shell](features.md) и [PowerShell в Cloud Shell (предварительная версия)](features-powershell.md).

## <a name="examples"></a>Примеры
* Создание сценариев для автоматизации задач управления Azure
* Одновременное управление ресурсами с помощью портала Azure и программ командной строки Azure.
* Испытание Azure CLI 2.0 или командлетов Azure PowerShell.

Эти примеры можно протестировать в кратких руководствах для [Bash в Cloud Shell](quickstart.md) и [PowerShell в Cloud Shell (предварительная версия)](quickstart-powershell.md).

## <a name="pricing"></a>Цены
За использование компьютера, на котором размещена среда Cloud Shell, плата не взимается. На этом компьютере должен быть подключен файловый ресурс Azure. Применяются расходы на обычное хранение.

## <a name="next-steps"></a>Дальнейшие действия
[Краткое руководство по использованию Bash в Azure Cloud Shell](quickstart.md) <br>
[Краткое руководство по использованию PowerShell в Cloud Shell (предварительная версия)](quickstart-powershell.md)