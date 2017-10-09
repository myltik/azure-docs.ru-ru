---
title: "Устранение неполадок Azure Cloud Shell (предварительная версия) | Документация Майкрософт"
description: "Устранение неполадок Azure Cloud Shell"
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
ms.date: 09/25/2017
ms.author: damaerte
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 32d4d4d10e5d8986e2dfe94430f52db8f038e245
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="troubleshooting-azure-cloud-shell"></a>Устранение неполадок Azure Cloud Shell.
Ниже приведены решения известных проблем в Azure Cloud Shell.

## <a name="error-400-disallowedoperation"></a>Ошибка: 400 DisallowedOperation
 - **Сведения.** Не удается создать хранилище, используя подписку Azure Active Directory.
 - **Устранение.** Используйте подписку Azure, которая позволяет создавать ресурсы хранилища. Подписки Azure AD не поддерживают создание ресурсов Azure.

## <a name="powershell-resolutions"></a>Устранение неполадок PowerShell

### <a name="no-home-directory-persistence"></a>Каталог $Home не сохраняется
  - **Сведения.** Данные, записываемые любым приложением (например, git, vim и пр.) в каталог $Home, не сохраняются между сеансами PowerShell.
  - **Устранение.** В профиле PowerShell создайте символьную ссылку на папку конкретного приложения в `clouddrive` для $Home.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Нажатие клавиш CTRL+C не закрывает командную строку командлетов
 - **Сведения**. При попытке выйти из командной строки командлетов нажатие клавиш `Ctrl+C` не приводит к желаемому результату.
 - **Устранение**. Чтобы выйти из командной строки, нажмите клавиши `Ctrl+C`, затем нажмите клавишу `Enter`.

### <a name="gui-applications-are-not-supported"></a>Приложения с графическим пользовательским интерфейсом не поддерживаются
  - **Сведения**. Если пользователь запускает приложение с графическим пользовательским интерфейсом, командная строка не отображается.  Например, когда пользователь использует `git`, чтобы закрыть частный репозиторий с поддержкой двухфакторной проверки подлинности, отображается диалоговое окно с кодом двухфакторной проверки подлинности.
  - **Устранение**. Нажмите клавиши `Ctrl+C` для выхода из командной строки.

### <a name="get-help--online-does-not-open-the-help-page"></a>Команда Get-Help -online не открывает страницу справки
 - **Сведения**. Если пользователь вводит `Get-Help Find-Module -online`, отображается сообщение об ошибке `Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`.
 - **Устранение**. Скопируйте URL-адрес и откройте его в браузере вручную.
 
### <a name="troubleshooting-remote-management-of-azure-vms"></a>Устранение неполадок удаленного управления виртуальными машинами Azure
 - **Сведения**. Из-за параметров брандмауэра Windows по умолчанию для WinRM может появиться следующее сообщение об ошибке: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`.
 - **Устранение**. Убедитесь, что виртуальная машина работает. Можно выполнить командлет `Get-AzureRmVM -Status`, чтобы определить состояние виртуальной машины.  Затем добавьте новое правило брандмауэра на удаленной виртуальной машине, чтобы разрешить подключения WinRM, например, из любой подсети.

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 Можно использовать [расширение пользовательских сценариев Azure][customex], чтобы избежать входа на удаленную виртуальную машину для добавления нового правила брандмауэра.
 Можно сохранить приведенный выше сценарий в файл, например `addfirerule.ps1`, и передать его в контейнер службы хранилища Azure.
 Затем введите приведенную ниже команду.

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```
