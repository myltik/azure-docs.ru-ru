---
title: "С помощью профилей версии API стека Azure | Документы Microsoft"
description: "Сведения о профилях версии API в Azure стека."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: sngun
ms.openlocfilehash: b70f8a392fdddade31383fc5cc9496cb39d73fd4
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2017
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Управление профилями версии API в стек Azure

Профили версии API-Интерфейс предоставляют способ управления версии различия между Azure и Azure стека. Профиль версии API — это набор модулей AzureRM PowerShell для определенной версии API. Каждая облачная платформа имеет набор профилей версии поддерживаемых API. Например, стека Azure поддерживает определенный профиль устаревшей версии, например **2017 г. 03-09-профиля**, и Azure поддерживает **последние** профиля версии API. При установке профиля установлены модули AzureRM PowerShell, которые соответствуют выбранному профилю.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Установка модуля PowerShell, необходимые для использования профилей версия API

**AzureRM.Bootstrapper** модуля, доступных в коллекции PowerShell предоставляет командлеты PowerShell, необходимых для работы с профилями версии API. Чтобы установить модуль AzureRM.Bootstrapper, используйте следующий командлет:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
Модуль AzureRM.Bootstrapper — в режиме предварительного просмотра; Подробные сведения и функциональные возможности могут быть изменены. Чтобы загрузить и установить последнюю версию этого модуля из коллекции PowerShell, выполните следующий командлет:

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>Установка профиля

Используйте **установки AzureRmProfile** командлет с **2017 г. 03-09-профиля** профиля версии API для установки AzureRM модулями, необходимыми стеком Azure. Обратите внимание, что с этим профилем версия API не установлены модули администратора облака Azure стека, и они должны устанавливаться отдельно, как указано в шаге 3 [Установка PowerShell для Azure стека](azure-stack-powershell-install.md) статьи.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Установить и импортировать модули в профиле

Используйте **AzureRmProfile используйте** командлет, чтобы установить и импортировать модули, связанные с профилем версии API. В сеансе PowerShell можно импортировать только один профиль версии API. Чтобы импортировать другой профиль версии API, необходимо открыть новый сеанс PowerShell. Используйте AzureRMProfile выполняет следующие задачи:  
1. Проверяет наличие модули PowerShell, связанные с конкретным профилем версии API в текущей области.  
2. Загрузка и Установка модулей, если они еще не установлены.   
3. Импортирует модули в текущий сеанс PowerShell. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

Для установки и импорта выбранных модулей AzureRM из профиля версии API-Интерфейс, запустить командлет используйте AzureRMProfile с **модуль** параметр:

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Получение установленных профилей

Используйте **Get AzureRmProfile** для получения списка доступных профилей версии API: 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Обновить профили

Используйте **AzureRmProfile обновление** командлет для обновления до последней версии модулей, доступных в PSGallery модули в профиле версии API. Рекомендуется всегда запускать **AzureRmProfile обновление** командлет в сеансе PowerShell во избежание конфликтов при импорте модулей. Командлет Update-AzureRmProfile выполняет следующие задачи:

1. Проверяет, если установлены последние версии модулей в заданного профиля версии API для текущей области.  
2. Предлагает установить, если они еще не установлены.  
3. Устанавливает и импортирует обновленные модули в текущий сеанс PowerShell.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

Чтобы удалить ранее установленные модули перед обновлением до версии, с помощью командлета Update AzureRmProfile вместе с **- RemovePreviousVersions** параметр:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

Этот командлет выполняет следующие задачи:  

1. Проверяет, если установлены последние версии модулей в заданного профиля версии API для текущей области.  
2. Удаляет старые версии модули из текущего профиля версии API и в текущем сеансе PowerShell.  
4. выводит приглашение установить последнюю версию.  
5. Устанавливает и импортирует обновленные модули в текущий сеанс PowerShell.  
 
## <a name="uninstall-profiles"></a>Удалять профили

Используйте **AzureRmProfile удаления** командлет для удаления указанного профиля версии API.

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>Дальнейшие действия
* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Установка PowerShell для Azure Stack)
* [Настройка среды PowerShell пользователя стек Azure](azure-stack-powershell-configure-user.md)  
