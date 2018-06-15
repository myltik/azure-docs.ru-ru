---
title: Устранение неполадок Azure Cloud Shell | Документация Майкрософт
description: Устранение неполадок Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: damaerte
ms.openlocfilehash: cffa67509690f4c594182fbe8104f0620da56bee
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608956"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Устранение неполадок и ограничения в Azure Cloud Shell

Ниже описаны решения известных проблем в Azure Cloud Shell.

## <a name="general-troubleshooting"></a>Общие действия по устранению неполадок

### <a name="early-timeouts-in-firefox"></a>Преждевременное истечение времени ожидания в FireFox
- **Сведения.** Cloud Shell использует открытый WebSocket для передачи входящих и исходящих значений в браузер. FireFox включает предварительно заданные политики, которые могут преждевременно закрыть подключение по протоколу WebSocket, что может стать причиной преждевременного истечения времени ожидания в Cloud Shell.
- **Решение.** Откройте FireFox и перейдите в область about:config в поле URL-адреса. Введите в строку поиска network.websocket.timeout.ping.request и измените значение 0 на 10.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Диалоговое окно службы хранилища. Ошибка: 403 RequestDisallowedByPolicy
- **Сведения.** Создание учетной записи хранилища с помощью Cloud Shell завершается ошибкой из-за политики Azure, установленной администратором. Сообщение об ошибке содержит следующие сведения: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Решение.** Обратитесь к администратору Azure с просьбой удалить или обновить политику Azure, которая отклоняет создание хранилища.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Диалоговое окно службы хранилища. Ошибка: 400 DisallowedOperation
 - **Сведения.** Не удается создать хранилище, используя подписку Azure Active Directory.
 - **Устранение.** Используйте подписку Azure, которая позволяет создавать ресурсы хранилища. Подписки Azure AD не поддерживают создание ресурсов Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Выходные данные терминала. Ошибка "Failed to connect terminal: websocket cannot be established. Press `Enter` to reconnect" (Не удалось подключиться к терминалу. Невозможно установить подключение по протоколу WebSocket. Нажмите клавишу ВВОД, чтобы подключиться еще раз)
 - **Сведения.** Для Cloud Shell требуется возможность установить подключение по протоколу WebSocket к инфраструктуре Cloud Shell.
 - **Решение.** Убедитесь, что параметры сети настроены для разрешения отправки HTTP-запросов и запросов WebSocket к доменам по адресу *.console.azure.com.

## <a name="bash-troubleshooting"></a>Устранение неполадок в Bash

### <a name="cannot-run-the-docker-daemon"></a>Не удается запустить управляющую программу Docker

- **Сведения**. Cloud Shell использует контейнер для размещения оболочки среды, поэтому выполнение управляющей программы запрещено.
- **Устранение**. Используйте компонент [docker-machine](https://docs.docker.com/machine/overview/), установленный по умолчанию, для управления контейнерами Docker с удаленного узла Docker.

## <a name="powershell-troubleshooting"></a>Устранение неполадок в PowerShell

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

## <a name="general-limitations"></a>Общие ограничения
Azure Cloud Shell имеет следующие известные ограничения:

### <a name="system-state-and-persistence"></a>Состояние системы и сохраняемость

Компьютер, предоставляющий сеанс Cloud Shell, является временным и перезапускается, если сеанс не используется в течение 20 минут. Для Cloud Shell требуется подключение файлового ресурса Azure. Поэтому ваша подписка должна иметь возможность настраивать ресурсы хранилища для доступа к Cloud Shell. Дополнительные рекомендации:

* Если подключено хранилище, то сохраняются только изменения в каталоге `clouddrive`. Кроме того, в Bash сохраняется ваш каталог `$Home`.
* Файловые ресурсы Azure можно подключить только в пределах [назначенного региона](persisting-shell-storage.md#mount-a-new-clouddrive).
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

Разрешения задаются как для обычных пользователей без доступа к sudo. Все, что устанавливается за пределами каталога `$Home`, не сохраняется.

### <a name="editing-bashrc"></a>Изменение файла .bashrc

Будьте внимательны при изменении файла .bashrc, так как некоторые изменения могут привести к непредвиденным ошибкам в Cloud Shell.

## <a name="powershell-limitations"></a>Ограничения PowerShell

### <a name="slow-startup-time"></a>Медленный запуск

Инициализация PowerShell в Azure Cloud Shell (предварительная версия) может занимать до 60 секунд на этапе предварительной версии.

### <a name="default-file-location-when-created-from-azure-drive"></a>Расположение файла по умолчанию при создании с помощью диска Azure:

С помощью командлетов PowerShell пользователи не могут создавать файлы на диске Azure. Когда пользователи создают новые файлы с помощью других инструментов, таких как vim или nano, по умолчанию файлы сохраняются в папке C:\Users. 

### <a name="gui-applications-are-not-supported"></a>Приложения с графическим пользовательским интерфейсом не поддерживаются

Если пользователь выполняет команду для создания диалогового окна Windows, например `Connect-AzureAD` или `Connect-AzureRmAccount`, отобразится следующее сообщение об ошибке: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

## <a name="gdpr-compliance-for-cloud-shell"></a>Соответствие GDPR для Cloud Shell

Azure Cloud Shell серьезно относится к личным данным. Данные, собранные и хранимые службой Azure Cloud Shell, используются для предоставления значений по умолчанию при работе пользователя. Это могут быть последние использованные оболочки, предпочтительный размер шрифта, предпочтительный тип шрифта и сведения о файловом ресурсе для clouddrive. На случай, если вам понадобится экспортировать или удалить эти данные, мы добавили следующие инструкции.

### <a name="export"></a>экспорт.
Чтобы **экспортировать** пользовательские настройки, сохраненные Cloud Shell, такие как предпочитаемая оболочка, размер и тип шрифта, выполните следующие команды.

1. Запуск Bash в Cloud Shell.
2. Выполните следующие команды:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
```

### <a name="delete"></a>Delete
Чтобы **удалить** пользовательские настройки, сохраненные Cloud Shell, такие как предпочитаемая оболочка, размер и тип шрифта, выполните следующие команды. При следующем запуске Cloud Shell вам будет предложено еще раз выставить файловый ресурс. 

При удалении настроек пользователя сам файловый ресурс Azure не будет удален. Перейдите к службе файлов Azure для выполнения этого действия.

1. Запуск Bash в Cloud Shell.
2. Выполните следующие команды:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
```
