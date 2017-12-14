---
title: "Устранение неполадок Azure Cloud Shell | Документация Майкрософт"
description: "Устранение неполадок Azure Cloud Shell."
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: damaerte
ms.openlocfilehash: 233569303ea3651192aafe9681f58a9582625d29
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshooting-azure-cloud-shell"></a>Устранение неполадок Azure Cloud Shell.

Ниже приведены решения известных проблем в Azure Cloud Shell.

## <a name="general-resolutions"></a>Общие способы устранения

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Диалоговое окно службы хранилища. Ошибка: 403 RequestDisallowedByPolicy
- **Сведения.** Создание учетной записи хранилища с помощью Cloud Shell завершается ошибкой из-за политики Azure, установленной администратором. Сообщение об ошибке содержит следующие сведения: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Решение.** Обратитесь к администратору Azure с просьбой удалить или обновить политику Azure, которая отклоняет создание хранилища.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Диалоговое окно службы хранилища. Ошибка: 400 DisallowedOperation
 - **Сведения.** Не удается создать хранилище, используя подписку Azure Active Directory.
 - **Устранение.** Используйте подписку Azure, которая позволяет создавать ресурсы хранилища. Подписки Azure AD не поддерживают создание ресурсов Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Выходные данные терминала. Ошибка "Failed to connect terminal: websocket cannot be established. Press `Enter` to reconnect" (Не удалось подключиться к терминалу. Невозможно установить подключение по протоколу WebSocket. Нажмите клавишу ВВОД, чтобы подключиться еще раз)
 - **Сведения.** Для Cloud Shell требуется возможность установить подключение по протоколу WebSocket к инфраструктуре Cloud Shell.
 - **Решение.** Убедитесь, что параметры сети настроены для разрешения отправки HTTP-запросов и запросов WebSocket к доменам по адресу *.console.azure.com.

## <a name="bash-resolutions"></a>Способы устранения для Bash

### <a name="cannot-run-az-login"></a>Не удается выполнить команду az login

- **Сведения**. Команда `az login` не будет выполняться, так как вы уже прошли аутентификацию с учетной записью, которая использовалась для входа в Cloud Shell или на портал Azure.
- **Устранение**. Используйте свою учетную запись, с помощью которой вы осуществили вход или выход, и повторно пройдите аутентификацию с нужной учетной записью Azure.

### <a name="cannot-run-the-docker-daemon"></a>Не удается запустить управляющую программу Docker

- **Сведения**. Cloud Shell использует контейнер для размещения оболочки среды, поэтому выполнение управляющей программы запрещено.
- **Устранение**. Используйте компонент [docker-machine](https://docs.docker.com/machine/overview/), установленный по умолчанию, для управления контейнерами Docker с удаленного узла Docker.

## <a name="powershell-resolutions"></a>Устранение неполадок PowerShell

### <a name="no-home-directory-persistence"></a>Каталог $Home не сохраняется

- **Сведения.** Любые данные, записываемые приложением (например, Git, Vim и т. д.) в каталог `$Home`, не сохраняются между сеансами PowerShell.
- **Устранение.** В профиле PowerShell создайте символьную ссылку на папку конкретного приложения в `clouddrive` для $Home.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Нажатие клавиш CTRL+C не закрывает командную строку командлетов

- **Сведения.** При попытке выйти из командной строки командлетов нажатие клавиш `Ctrl+C` не приводит к желаемому результату.
- **Устранение**. Чтобы выйти из командной строки, нажмите клавиши `Ctrl+C`, затем нажмите клавишу `Enter`.

### <a name="gui-applications-are-not-supported"></a>Приложения с графическим пользовательским интерфейсом не поддерживаются

- **Сведения**. Если пользователь запускает приложение с графическим пользовательским интерфейсом, командная строка не отображается. Например, когда пользователь клонирует частный репозиторий GitHub с поддержкой двухфакторной проверки подлинности, отображается диалоговое окно выполнения двухфакторной проверки подлинности.  
- **Разрешение.** Закройте и снова откройте оболочку.


### <a name="get-help--online-does-not-open-the-help-page"></a>Команда Get-Help -online не открывает страницу справки

- **Сведения**. Если пользователь вводит `Get-Help Find-Module -online`, отображается сообщение об ошибке `Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`.
- **Устранение**. Скопируйте URL-адрес и откройте его в браузере вручную.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Устранение неполадок удаленного управления виртуальными машинами Azure

- **Сведения**. Из-за параметров брандмауэра Windows по умолчанию для WinRM может появиться следующее сообщение об ошибке: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`.
- **Устранение**. Убедитесь, что виртуальная машина работает. Можно выполнить командлет `Get-AzureRmVM -Status`, чтобы определить состояние виртуальной машины.  Затем добавьте новое правило брандмауэра на удаленной виртуальной машине, чтобы разрешить подключения WinRM, например, из любой подсети.

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 Чтобы избежать входа на удаленную виртуальную машину для добавления нового правила брандмауэра, можно использовать [расширение пользовательских сценариев Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript).
 Можно сохранить приведенный выше сценарий в файл, например `addfirerule.ps1`, и отправить его в контейнер службы хранилища Azure.
 Затем введите приведенную ниже команду.

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` кэширует результат на диск Azure

- **Сведения:** результат `dir` кэшируется на диск Azure.
- **Решение:** после создания или удаления ресурса в представлении диска Azure выполните команду `dir -force` для обновления.
