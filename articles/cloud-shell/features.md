---
title: "Функции Azure Cloud Shell (предварительная версия) | Документация Майкрософт"
description: "Обзор функций Azure Cloud Shell."
services: 
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
ms.date: 07/13/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 3414957a67d67603fdb597b3715a902e8cc3c5bd
ms.contentlocale: ru-ru
ms.lasthandoff: 07/18/2017

---

# <a name="features-and-tools-for-azure-cloud-shell"></a>Функции и средства Azure Cloud Shell
Azure Cloud Shell — это браузерная оболочка, предназначенная для разработки ресурсов Azure и управления ими.

Cloud Shell предлагает предварительно настроенную и доступную из браузера оболочку для управления ресурсами Azure, которая избавит вас от необходимости самостоятельно устанавливать ПО, управлять версиями и обслуживать компьютер.

Cloud Shell подготавливает компьютеры по запросу, в результате чего состояние компьютера не сохраняется между сеансами. Поскольку Cloud Shell создана для интерактивных сеансов, оболочки автоматически завершают работу после 20 минут бездействия.

## <a name="bash-in-cloud-shell"></a>Bash в Cloud Shell
### <a name="tools"></a>Средства
|Категория   |Имя   |
|---|---|
|Интерпретатор оболочки Linux|Bash<br> sh               |
|Инструменты Azure            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) и [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)     |
|Текстовые редакторы           |vim<br> nano<br> emacs       |
|Система управления версиями         |git                    |
|Инструменты сборки            |make<br> maven<br> npm<br> pip         |
|Контейнеры             |[Docker CLI](https://github.com/docker/cli)/[Компьютер Docker](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Draft](https://github.com/Azure/draft)<br> [Интерфейс командной строки DC/OS](https://github.com/dcos/dcos-cli)         |
|Базы данных              |Клиент MySQL<br> Клиент PostgreSQL<br> [Служебная программа sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Другие                  |Клиент iPython<br> [Интерфейс командной строки Cloud Foundry](https://github.com/cloudfoundry/cli)<br> |

### <a name="language-support"></a>Поддержка языков
|язык   |Version (версия)   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|Python     |2.7 и 3.5 (по умолчанию)|

## <a name="secure-automatic-authentication"></a>Безопасная автоматическая аутентификация
Cloud Shell безопасно и автоматически выполняет аутентификацию доступа к учетной записи для Azure CLI 2.0.

## <a name="azure-files-persistence"></a>Сохраняемость файлов Azure
Поскольку Cloud Shell выделяется по запросу с использованием временного компьютера, файлы за пределами каталога $Home и сведения о состоянии компьютера не сохраняются между сеансами.
Чтобы сохранять файлы между сеансами, при первом запуске Cloud Shell предлагается присоединить общую папку Azure.
По завершении настройки Cloud Shell будет автоматически присоединять хранилище для всех будущих сеансов.

Дополнительные сведения см. в статье [Сохранение файлов в Azure Cloud Shell](persisting-shell-storage.md).

## <a name="next-steps"></a>Дальнейшие действия
[Краткое руководство по Cloud Shell](quickstart.md) <br>
[Подробное описание Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
