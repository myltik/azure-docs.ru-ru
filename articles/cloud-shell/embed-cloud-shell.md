---
title: "Внедрение оболочки облако Azure | Документы Microsoft"
description: "Узнайте, как внедрить оболочки облако Azure."
services: cloud-shell
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
ms.date: 12/11/2017
ms.author: juluk
ms.openlocfilehash: 78b539136971aa282e5447d7882ecb02f73f346b
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2017
---
# <a name="embed-azure-cloud-shell"></a>Внедрение оболочки облако Azure

Внедрение облака оболочки позволяет разработчикам и редакторы записи непосредственно открыть консоль облака с выделенной URL-адреса [shell.azure.com](https://shell.azure.com). Откроется сразу все возможности проверки подлинности облака оболочки, для работы с проектами, и актуальные Azure CLI или Azure PowerShell средств для пользователей.

[![](https://shell.azure.com/images/launchcloudshell.png "Запустите консоль облако Azure")](https://shell.azure.com)

## <a name="how-to"></a>Инструкции

Интегрировать кнопку запуска оболочки облака в файлы markdown путем копирования следующее:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

HTML для внедрения всплывающих оболочки облака используется следующим образом:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Настройка рабочей среды

Задайте режим работы определенная оболочка путем дополнения URL-адрес.
|Взаимодействие   |URL-адрес   |
|---|---|
|Последние использовавшиеся оболочки   |Shell.Azure.com           |
|Bash                       |Shell.Azure.com/bash       |
|PowerShell                 |Shell.Azure.com/PowerShell |

## <a name="next-steps"></a>Дальнейшие действия
[Краткое руководство по использованию Bash в Azure Cloud Shell](quickstart.md)<br>
[PowerShell в облаке оболочки краткое руководство](quickstart-powershell.md)
