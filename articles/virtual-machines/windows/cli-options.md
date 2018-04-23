---
title: Использование Azure CLI в Windows | Документация Майкрософт
description: Использование Azure CLI в Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: cynthn
ms.openlocfilehash: ece7806dd757ee5169a25ac83590aa4a740d6dcc
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="using-the-azure-cli-on-windows"></a>Использование Azure CLI в Windows

Интерфейс командной строки Azure (CLI) представляет собой интерфейс командной строки и среду скриптов для создания ресурсов Azure и управления ими. Azure CLI доступен для операционных систем macOS, Linux и Windows. Для этих операционных систем команды CLI идентичны. Однако синтаксис конкретного скрипта операционной системы может отличаться.

В этом документе представлены сведения об установке Azure CLI и его запуске в Windows, а также сведения о синтаксисе для каждого сценария. Подробную документацию по Azure CLI см. [здесь]( https://docs.microsoft.com/cli/azure).

## <a name="windows-subsystem-for-linux"></a>Подсистема Windows для Linux

Подсистема Windows для Linux предоставляет среду Ubuntu Linux для юбилейного выпуска Windows 10 и более поздних версий. Включив подсистему Windows для Linux, вы можете воспользоваться Bash для создания и выполнения сценариев Azure CLI. Благодаря этой возможности сценарии Azure CLI можно использовать в macOS, Linux и Windows, не изменяя их.

Чтобы использовать Azure CLI в подсистеме Windows для Linux, выполните следующие задания.

|Задача | Указания |
|---|---|
| Включение подсистемы Windows для Linux | [Документация по установке подсистемы Windows для Linux](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) |
| Установка Azure CLI |[Install the CLI on WSL/Ubuntu 14.04](https://docs.microsoft.com/cli/azure/install-az-cli2#ubuntu) (Установка интерфейса командной строки в подсистеме Windows для Linux или в Ubuntu 14.04)|

## <a name="powershell"></a>PowerShell

Azure CLI можно запускать в собственном коде в Windows. В такой конфигурации пакет Azure CLI устанавливается в операционной системе Windows, а команды можно выполнять из PowerShell. В такой конфигурации команды и скрипты Azure CLI можно выполнять в любой поддерживаемой версии Windows, но для этого потребуется синтаксис скрипта для конкретной платформы. Поэтому сценарии не обязательно совместно использовать в macOS, Linux и Windows, не изменяя их.

[Установите Azure CLI в Windows](https://docs.microsoft.com/cli/azure/install-az-cli2#windows), следуя инструкциям, для использования Azure CLI в Windows.

## <a name="docker-image"></a>Образ Docker

При использовании Docker для Windows можно запустить образ Docker, содержащий Azure CLI. Этот образ создан на основе Linux и позволяет работать с Bash.  При использовании Docker для Windows и образа Azure CLI сценарии нужно совместно использовать в macOS, Linux и Windows. 

Чтобы использовать Azure CLI на основе Docker для Windows, убедитесь, что Docker для Windows работает, и выполните следующую команду.

```bash
docker run -it azuresdk/azure-cli-python:latest bash
```

После завершения начнется сеанс Bash, в котором предварительно скачаны средства Azure CLI.

## <a name="next-steps"></a>Дальнейшие действия

[Примеры сценариев интерфейса командной строки для виртуальных машин Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Примеры сценариев интерфейса командной строки для веб-приложений Azure](../../app-service/app-service-cli-samples.md)

[Примеры сценариев интерфейса командной строки для SQL Azure](../../sql-database/sql-database-cli-samples.md)
