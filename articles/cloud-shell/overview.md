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
ms.date: 12/13/2017
ms.author: juluk
ms.openlocfilehash: 129b43db85a0962005352e0f1e6ad2ad3be2c7d5
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2017
---
# <a name="overview-of-azure-cloud-shell"></a>Обзор Azure Cloud Shell
Azure Cloud Shell — это интерактивная доступная браузеру оболочка для управления ресурсами Azure.
Она предоставляет гибкие возможности при выборе оболочки, соответствующей вашим методам работы.
Пользователи Linux могут использовать Bash, а пользователи Windows — PowerShell.

Попробуйте из shell.azure.com, с помощью этой кнопки.

[![](https://shell.azure.com/images/launchcloudshell.png "Запустите консоль облако Azure")](https://shell.azure.com)

Попробуйте из портал Azure, используя значок облака оболочки.

![Запуск с помощью портала](media/overview/portal-launch-icon.png)

## <a name="features"></a>Функции
### <a name="browser-based-shell-experience"></a>Оболочка на основе браузера
Cloud Shell предоставляет доступ к браузерному интерфейсу командной строки, созданному с учетом задач управления Azure.
Используйте Cloud Shell для работы из локального компьютера с неограниченными возможностями, которые может обеспечить только облако.

### <a name="choice-of-preferred-shell-experience"></a>Выбор предпочтительной оболочки
Linux пользователи могут использовать Bash в оболочке облака, пока Windows пользователи могут использовать PowerShell в облаке оболочки (Предварительная версия) из раскрывающегося списка оболочки.

![Bash в Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell в Cloud Shell (предварительная версия)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Настроенная и аутентифицированная рабочая станция Azure
Оболочки облака управляется корпорацией Майкрософт, поэтому поставляется со популярных средств командной строки и поддержка языков. Облако оболочки безопасно автоматически используется для проверки мгновенный доступ к ресурсам через командлеты Azure CLI 2.0 или Azure PowerShell.

Просмотрите полный список инструментов для работы с [Bash](features.md#tools) и [PowerShell (предварительная версия)](features-powershell.md#tools).

### <a name="multiple-access-points"></a>Доступность в нескольких расположениях
Оболочка облака — гибкий инструмент, который можно использовать из:
* [Portal.Azure.com](https://portal.azure.com)
* [Shell.Azure.com](https://shell.azure.com)
* [ознакомительная документация по Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest);
* [мобильные приложения Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/);
* [Учетная запись Azure кода расширении VS](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Подключение хранилища файлов Microsoft Azure
Оболочки компьютерах облака являются временными и требуют общей папке файлов Azure монтируется как `clouddrive` для сохранения файлов.

При первом запуске оболочки облака предлагается создать ресурс группы, учетной записи хранилища и файлы Azure совместно использовать от вашего имени. Это одноразовое действие, которое автоматически применяется для всех сеансов. Вы можете подключить один файловый ресурс, который будет использоваться как при работе с Bash, так и при работе с PowerShell в Cloud Shell (предварительная версия).

#### <a name="create-new-storage"></a>Создание хранилища
![](media/overview/basic-storage.png)

Учетная запись локально избыточного хранилища (LRS) и файловый ресурс Azure могут быть созданы от вашего имени. Файловый ресурс Azure будет использоваться для сред Bash и PowerShell, если вы решите использовать обе эти среды. Применяются расходы на обычное хранение.

От вашего имени будет создано три ресурса:
1. Группа ресурсов с именем: `cloud-shell-storage-<region>`
2. Учетная запись хранения с именем: `cs<uniqueGuid>`
3. Общая папка с именем: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Bash в Cloud Shell также создает образ диска по умолчанию размером в 5 ГБ для хранения `$Home`. Все файлы в каталоге $Home, такие как ключи SSH, сохраняются в образе диска пользователя, который хранится в подключенном файловом ресурсе Azure. Соблюдайте рекомендации при сохранении файлов в каталоге $Home и подключенном файловом ресурсе Azure.

#### <a name="use-existing-resources"></a>Использование существующих ресурсов
![](media/overview/advanced-storage.png)

В Cloud Shell доступна дополнительная возможность, которая позволяет связывать существующие ресурсы с Cloud Shell.
При появлении запроса на настройку хранилища щелкните "Показать дополнительные настройки", чтобы отобразить дополнительные параметры.

> [!Note]
> Раскрывающиеся меню фильтруются по свой назначенный регион облачной оболочки и учетные записи хранения LRS и GRS.

[Дополнительные сведения о хранилище Cloud Shell, обновлении файловых ресурсов Azure, а также о передаче и скачивании файлов](persisting-shell-storage.md).

## <a name="concepts"></a>Основные понятия
* Cloud Shell работает на временном узле, предоставляемом для каждого сеанса и для каждого пользователя отдельно.
* Время ожидания Cloud Shell истекает через 20 минут при отсутствии интерактивных действий.
* Для Cloud Shell требуется подключение файлового ресурса Azure.
* Cloud Shell использует один и тот же файловый ресурс Azure для Bash и PowerShell.
* Cloud Shell назначается один компьютер на учетную запись пользователя.
* С помощью изображения размером 5 ГБ, которые содержатся в папке файл $Home сохраняется bash
* Разрешения задаются как для обычного пользователя Linux в Bash.

Узнайте больше о возможностях [Bash в Cloud Shell](features.md) и [PowerShell в Cloud Shell (предварительная версия)](features-powershell.md).

## <a name="pricing"></a>Цены
За использование компьютера, на котором размещена среда Cloud Shell, плата не взимается. На этом компьютере должен быть подключен файловый ресурс Azure. Применяются расходы на обычное хранение.

## <a name="next-steps"></a>Дальнейшие действия
[Краткое руководство по использованию Bash в Azure Cloud Shell](quickstart.md) <br>
[Краткое руководство по использованию PowerShell в Cloud Shell (предварительная версия)](quickstart-powershell.md)