---
title: "Ограничения Azure Cloud Shell (предварительная версия) | Документация Майкрософт"
description: "Обзор ограничений Azure Cloud Shell."
services: azure
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
ms.openlocfilehash: fe325cf5fa5e3d4b0a188599de7308cf2008b458
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="limitations-of-azure-cloud-shell"></a>Ограничения Azure Cloud Shell
Azure Cloud Shell имеет следующие известные ограничения:

## <a name="general-limitations"></a>Общие ограничения

### <a name="system-state-and-persistence"></a>Состояние системы и сохраняемость
Компьютер, предоставляющий сеанс Cloud Shell, является временным и перезапускается, если сеанс не используется в течение 20 минут. Для подключения Cloud Shell требуется общая папка. Поэтому ваша подписка должна иметь возможность настраивать ресурсы хранилища для доступа к Cloud Shell. Дополнительные рекомендации:
* Если подключено хранилище, то сохраняются только изменения в каталоге `clouddrive`. Кроме того, в Bash сохраняется ваш каталог `$Home`.
* Общие папки можно подключить только в пределах [назначенного региона](persisting-shell-storage.md#mount-a-new-clouddrive).
  * В Bash выполните команду `env`, чтобы найти регион, заданный для `ACC_LOCATION`.
* Файлы Azure поддерживают только локально избыточное хранилище в геоизбыточные учетные записи.

### <a name="browser-support"></a>Поддержка браузеров
Cloud Shell поддерживает последние версии Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox и Apple Safari. Использование Safari в режиме защищенного просмотра не поддерживается.

### <a name="copy-and-paste"></a>Копирование и вставка

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Для заданного пользователя может быть активна только одна оболочка.
Пользователи могут запускать только один тип оболочки одновременно, **Bash** или **PowerShell**. Однако можно запустить несколько экземпляров Bash или PowerShell одновременно. Переключение между Bash и PowerShell приводит к перезапуску Cloud Shell и завершение существующих сеансов.

### <a name="usage-limits"></a>Ограничения использования
Cloud Shell предназначен для интерактивного использования. В результате любые длительные неинтерактивные сеансы завершаются без предупреждения.

## <a name="bash-limitations"></a>Ограничения Bash

### <a name="user-permissions"></a>Разрешения пользователя
Разрешения задаются как для обычных пользователей без доступа к sudo. Все, что устанавливается за пределами каталога `$Home`, не сохранится.
Хотя некоторые команды в каталоге `clouddrive`, такие как `git clone`, не имеют необходимых разрешений, каталог `$Home` их имеет.

### <a name="editing-bashrc"></a>Изменение файла .bashrc
Будьте внимательны при изменении файла .bashrc, так как некоторые изменения могут привести к непредвиденным ошибкам в Cloud Shell. 

### <a name="bashhistory"></a>.bash_history
Журнал команд bash может быть несогласованным из-за перебоев в сеансе Cloud Shell или из-за одновременных сеансов.

## <a name="powershell-limitations"></a>Ограничения PowerShell

### <a name="slow-startup-time"></a>Медленный запуск
Инициализация PowerShell в Cloud Shell на этапе предварительной версии может занимать до 60 секунд.

### <a name="no-home-directory-persistence"></a>Каталог $Home не сохраняется
Данные, записанные в каталог $Home каким-либо приложением (например, git, vim и пр.), не сохраняются между сеансами PowerShell.  Решение этой проблемы приведено [здесь](troubleshooting.md#powershell-resolutions).

### <a name="error-if-azurerm-module-is-updated-from-powershellgallery"></a>Ошибка при обновлении модуля AzureRM из PowerShellGallery
При обновлении модулей AzureRM до последней версии (4.4.0) может появиться следующая ошибка при запуске CloudShell.
``` powershell
VERBOSE: Authenticating to Azure ...
Import-Module : Method 'RemoveUser' in type 'Microsoft.Azure.PSCloudConsole.ADAuth.ADAuthFactory' from assembly 'Microsoft.Azure.PSCloudConsole.ADAuth, Version=0.0.0.0,
Culture=neutral, PublicKeyToken=null' does not have an implementation.
At C:\Program Files\WindowsPowerShell\Modules\PSCloudShellADAuth\PSCloudShellADAuth.psm1:12 char:1
+ Import-Module -Name $AdAuthModulePath -PassThru -Force
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Import-Module], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.PowerShell.Commands.ImportModuleCommand

Unable to find type [Microsoft.Azure.PSCloudConsole.ADAuth.ADAuthFactory].
At C:\Users\ContainerAdministrator\PSCloudShellStartup.ps1:94 char:9
+         [Microsoft.Azure.PSCloudConsole.ADAuth.ADAuthFactory]::Update ...
+         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (Microsoft.Azure...h.ADAuthFactory:TypeName) [], RuntimeException
    + FullyQualifiedErrorId : TypeNotFound
```

## <a name="next-steps"></a>Дальнейшие действия
[Устранение неполадок Cloud Shell](troubleshooting.md) <br>
[Краткое руководство по Bash](quickstart.md) <br>
[Краткое руководство по PowerShell](quickstart-powershell.md)
