---
title: Функции PowerShell в Azure Cloud Shell (предварительная версия) | Документация Майкрософт
description: Обзор функций PowerShell в Azure Cloud Shell.
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: damaerte
ms.openlocfilehash: 5c4cb6230d8509830d25caaf497e4e63ac7e5806
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell-preview"></a>Функции и инструменты для PowerShell в Azure Cloud Shell (предварительная версия)

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> Также доступны функции и инструменты для [Bash](features.md).

PowerShell в Cloud Shell (предварительная версия) выполняется в `Windows Server 2016`.

## <a name="features"></a>Функции

### <a name="secure-automatic-authentication"></a>Безопасная автоматическая аутентификация

PowerShell в Cloud Shell (предварительная версия) безопасно и автоматически выполняет аутентификацию доступа к учетным записям для Azure PowerShell.

### <a name="files-persistence-across-sessions"></a>Сохранение файлов между сеансами

Чтобы сохранять файлы между сеансами, при первом запуске Cloud Shell предлагается подключить файловый ресурс Microsoft Azure.
По завершении настройки Cloud Shell будет автоматически подключать хранилище (подключенное как `$home\clouddrive`) для всех будущих сеансов.
Так как каждому запросу к Cloud Shell выделяется временный компьютер, файлы за пределами каталога `$home\clouddrive` и сведения о состоянии компьютера не сохраняются между сеансами.

Дополнительные сведения см. в статье [Сохранение файлов в Azure Cloud Shell](persisting-shell-storage-powershell.md).

### <a name="azure-drive-azure"></a>Диск Azure (Azure:)

PowerShell в Cloud Shell (предварительная версия) запускается на диске Azure (`Azure:`).
Диск Azure облегчает обнаружение ресурсов Azure и перемещение по ним, включая вычислительные ресурсы, сетевые ресурсы, ресурсы хранилища и т. д., предоставляя возможности навигации как у файловой системы.
Для управления этими ресурсами можно использовать привычные [командлеты Azure PowerShell](https://docs.microsoft.com/powershell/azure).
Любые изменения, внесенные в ресурсы Azure либо непосредственно на портале Azure, либо с помощью командлетов Azure PowerShell, сразу же отражаются на диске Azure.

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>Отслеживание контекста
- **Определение области группы ресурсов**. В контексте пути группы ресурсов на диске Azure (`Azure:`) имя группы ресурсов автоматически передается в командлеты Azure PowerShell.

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get-AzureRmCommand**. Этот командлет возвращает список команд, применимых в контексте расположения на диске Azure (`Azure:`). Например, он показывает только команды, связанные с хранилищем, если пользователь находится в каталоге `Azure:\<subscription name>\StorageAccounts`.

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>Расширенные возможности редактирования сценариев PowerShell

При использовании VIM для редактирования файлов PowerShell (т. е. файлов с расширением `.ps1,.psm1,.psd1`) применяется автоматическое выделение синтаксических конструкций и поддерживается технология IntelliSense.
Поддержка IntelliSense реализуется с помощью подключаемого модуля VIM, который взаимодействует с локальным экземпляром [служб редактора PowerShell](https://github.com/PowerShell/PowerShellEditorServices).

> [!TIP]
> Нажимайте клавишу `TAB` для автозавершения (с помощью IntelliSense) имен команд, имен параметров и значений параметров (где применимо).

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>Расширяемая модель

С помощью [PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget) можно легко устанавливать (и обновлять) настраиваемые модули и сценарии из [коллекции PowerShell](https://www.powershellgallery.com).
После установки модули автоматически сохраняются между сеансами Cloud Shell.

> [!TIP]
> Модули, устанавливаемые пользователями, сохраняются в папку `$Home\CloudDrive\.pscloudshell\WindowsPowerShell`. Символьная ссылка на эту папку создается в папке документов пользователя (`$home\Documents\WindowsPowerShell`).

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>Управление гостевыми виртуальными машинами

С помощью двух встроенных команд, `Enter-AzureRmVM` и `Invoke-AzureRmVMCommand`, можно удаленно управлять виртуальными машинами Azure.
Эти команды основаны на средствах удаленного взаимодействия PowerShell и требуют подключения PowerShell к виртуальным машинам Azure.

## <a name="tools"></a>Средства

|**Категория**    |**Имя**                                 |
|----------------|-----------------------------------------|
|Инструменты Azure     |[Azure PowerShell (5.6.0)](https://docs.microsoft.com/powershell/azure/overview)<br> [Azure CLI (2.0.30)](https://docs.microsoft.com/cli/azure)|
|Текстовые редакторы    |vim<br> nano                             |
|Диспетчер пакетов |PowerShellGet<br> PackageManagement<br> npm<br> pip |
|Система управления версиями  |git                                      |
|Базы данных       |[Модуль SqlServer](https://www.powershellgallery.com/packages/SqlServer)<br> [Служебная программа sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Инструменты тестирования      |Pester                                   |

## <a name="language-support"></a>Поддержка языков

|**Язык**|**Версия**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |6.10       |
|PowerShell  |5.1 и [6.0.2](https://github.com/PowerShell/powershell/releases)       |
|Python      |2.7        |

## <a name="next-steps"></a>Дополнительная информация

[Краткое руководство по использованию PowerShell в Cloud Shell (предварительная версия)](quickstart-powershell.md)

[Дополнительные сведения об Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
