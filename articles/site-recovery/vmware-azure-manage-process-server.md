---
title: Управление сервером обработки в Azure Site Recovery | Документация Майкрософт
description: Из этой статьи вы узнаете, как управлять сервером обработки, настроенным для репликации виртуальных машин VMware и физических серверов, в Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
editor: ''
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 096b2890d41402448809ae87759fcd6b67bee2fe
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
ms.locfileid: "29811201"
---
# <a name="manage-process-servers"></a>Управление серверами обработки

По умолчанию сервер обработки, используемый при репликации виртуальных машин VMware или физических серверов в Azure, установлен на локальном компьютере сервера конфигурации. На некоторых экземплярах нужно настроить отдельный сервер обработки:

- Для больших развертываний могут понадобиться дополнительные серверы обработки в локальной среде, чтобы изменять масштаб емкости.
- Для восстановления размещения потребуется настроить временный сервер обработки в Azure. Восстановив размещение, вы можете удалить эту виртуальную машину. 

В этой статье собраны стандартные задачи управления для этих дополнительных серверов обработки.

## <a name="upgrade-a-process-server"></a>Обновление сервера обработки

Обновите сервер обработки, запущенный локально или в Azure (для восстановления размещения), следующим образом:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  Как правило, при использовании образа из коллекции Azure, чтобы создать сервер обработки в Azure для восстановления размещения, применяется последняя доступная версия. Команды разработчиков Site Recovery регулярно выпускают исправления и улучшения, и мы рекомендуем вам систематически обновлять свои серверы обработки.



## <a name="reregister-a-process-server"></a>Повторная регистрация сервера обработки

Если нужно повторно зарегистрировать сервер обработки, запущенный локально или в Azure с сервером конфигурации, сделайте следующее:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Сохранив параметры, сделайте следующее:

1. Откройте командную строку администратора на сервере обработки.
2. Перейдите к папке **%PROGRAMDATA%\ASR\Agent** и выполните эту команду:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Изменение параметров прокси-сервера для сервера обработки в локальной среде

Если в Azure сервер обработки подключается к Site Recovery с помощью прокси-сервера, используйте описанную ниже процедуру, чтобы изменить существующие параметры прокси-сервера.

1. Войдите на компьютер сервера обработки. 
2. Откройте командную строку PowerShell с правами администратора и выполните следующую команду.
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
2. Перейдите к папке **%PROGRAMDATA%\ASR\Agent** и выполните следующую команду:
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```


## <a name="remove-a-process-server"></a>Удаление сервера обработки

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]


