---
title: "Использование прав корневой учетной записи на виртуальных машинах Linux | Документация Майкрософт"
description: "Узнайте, как использовать права корневой учетной записи на виртуальных машинах Linux в Azure."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: a2c106a2-dceb-43a3-9dd1-50ed77685952
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: dc39db1f5fecffb60499a5420bfe72850e2fffd9
ms.lasthandoff: 04/03/2017


---
# <a name="using-root-privileges-on-linux-virtual-machines-in-azure"></a>Использование прав корневой учетной записи на виртуальных машинах Linux в Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

По умолчанию пользователь `root` отключен в виртуальных машинах Linux в Azure. Пользователи могут выполнять команды с повышенными привилегиями, используя команду `sudo` . При этом в зависимости от способа подготовки системы процедура может отличаться.

1. **Ключ SSH и пароль ИЛИ только пароль** — виртуальная машина была подготовлена либо с использованием сертификата (файла с расширением `.CER`) или ключа SSH и пароля, либо с использованием только имени пользователя и пароля. В этом случае `sudo` запросит пароль пользователя перед выполнением команды.
2. **Только ключ SSH** — виртуальная машина была подготовлена с использованием сертификата (файла с расширением `.cer`, `.pem` или `.pub`) или ключа SSH, но без пароля.  В этом случае `sudo` **не будет** запрашивать пароль пользователя перед выполнением команды.

## <a name="ssh-key-and-password-or-password-only"></a>Ключ и пароль или только пароль SSH
Войдите в виртуальную машину Linux с использованием ключа SSH или пароля, а затем выполните команды с использованием `sudo`, например:

    # sudo <command>
    [sudo] password for azureuser:

В этом случае пользователю будет предложено ввести пароль. После ввода пароля `sudo` выполнит команду с привилегиями `root`.

Вы также можете включить работу sudo без пароля, изменив файл `/etc/sudoers.d/waagent` , например:

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

Это изменение позволит пользователю azureuser использовать sudo без пароля.

## <a name="ssh-key-only"></a>Только ключ SSH
Войдите в виртуальную машину Linux с использованием ключа SSH, а затем выполните команды с использованием `sudo`, например:

    # sudo <command>

В этом случае пользователю **не будет** предложено ввести пароль. После нажатия клавиши `<enter>` `sudo` выполнит команду с привилегиями `root`.


