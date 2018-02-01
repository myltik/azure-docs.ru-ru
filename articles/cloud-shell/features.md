---
title: "Функции Bash в Azure Cloud Shell | Документация Майкрософт"
description: "Обзор функций Bash в Azure Cloud Shell."
services: Azure
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
ms.date: 11/15/2017
ms.author: juluk
ms.openlocfilehash: c77f31929b7fa8f98d7e30616c6645f79d135a1e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="features--tools-for-bash-in-azure-cloud-shell"></a>Функции и инструменты для Bash в Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> Также доступны функции и инструменты для [PowerShell](features-powershell.md).

Bash в Cloud Shell выполняется в `Ubuntu 16.04 LTS`.

## <a name="features"></a>Функции

### <a name="secure-automatic-authentication"></a>Безопасная автоматическая аутентификация

Bash в Cloud Shell безопасно и автоматически выполняет аутентификацию доступа учетных записей для Azure CLI 2.0.

### <a name="ssh-into-azure-linux-virtual-machines"></a>SSH-подключение к виртуальным машинам Linux в Azure

При создании виртуальной машины Linux в Azure CLI 2.0 можно создать ключ SSH по умолчанию и поместить его в свой каталог `$Home`. Размещение ключей SSH в `$Home` позволяет осуществлять подключение по протоколу SSH к виртуальным машинам Linux в Azure непосредственно из Cloud Shell. Ключи хранятся в файле acc_<user>.img в файловом ресурсе. Следуйте рекомендациям по использованию файлового ресурса или ключей и предоставлению доступа к ним.

### <a name="home-persistence-across-sessions"></a>Сохранение каталога $Home между сеансами

Чтобы сохранять файлы между сеансами, при первом запуске Cloud Shell предлагается присоединить общую папку Azure.
По завершении настройки Cloud Shell будет автоматически подключать хранилище (подключенное как `$Home\clouddrive`) для всех будущих сеансов.
Кроме того, в Bash в Cloud Shell ваш каталог `$Home` сохраняется в виде IMG-файла в файловом ресурсе Azure.
Файлы вне `$Home` и состояние компьютера не сохраняются между сеансами.

[Дополнительные сведения о сохранении файлов для Bash в Cloud Shell.](persisting-shell-storage.md)

## <a name="tools"></a>Средства

|Категория   |ИМЯ   |
|---|---|
|Средства Linux            |bash<br> sh<br> tmux<br> dig<br>               |
|Инструменты Azure            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) и [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard) <br> [Интерфейс командной строки Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) <br> [blobxfer](https://github.com/Azure/blobxfer#blobxfer) |
|Текстовые редакторы           |vim<br> nano<br> emacs       |
|Система управления версиями         |git                    |
|Инструменты сборки            |make<br> maven<br> npm<br> pip         |
|Контейнеры             |[Docker CLI](https://github.com/docker/cli)/[Компьютер Docker](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [Интерфейс командной строки DC/OS](https://github.com/dcos/dcos-cli)         |
|Базы данных              |Клиент MySQL<br> Клиент PostgreSQL<br> [Служебная программа sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Другие                  |Клиент iPython<br> [Интерфейс командной строки Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)| 

## <a name="language-support"></a>Поддержка языков

|Язык   |Version (версия)   |
|---|---|
|.NET       |2.0.0       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|PowerShell |[6.0 (бета-версия)](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 и 3.5 (по умолчанию)|

## <a name="next-steps"></a>Дополнительная информация
[Краткое руководство по Bash в Cloud Shell](quickstart.md) <br>
[Справочник команд Azure CLI 2.0](https://docs.microsoft.com/cli/azure/)
