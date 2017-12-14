---
title: "Использование окна Azure Cloud Shell | Документация Майкрософт"
description: "Общие сведения о том, как использовать окно Azure Cloud Shell."
services: azure
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
ms.date: 10/16/2017
ms.author: juluk
ms.openlocfilehash: 4eb5680c618d78e0722e1eb4a0f551f26b4dc902
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
---
# <a name="using-the-azure-cloud-shell-window"></a>Использование окна Azure Cloud Shell

В этом документе содержатся сведения о том, как использовать окно Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Переключение между средами Bash и PowerShell
![](media/using-the-shell-window/env-selector.png)

Для переключения между средами Bash и PowerShell используйте селектора сред на панели инструментов Cloud Shell.

## <a name="restart-cloud-shell"></a>Перезапуск Cloud Shell
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Перезапуск Cloud Shell сбросит состояние машины, и все файлы, не сохраненные в общей папке Azure, будут утеряны.

* Щелкните значок перезапуска на панели инструментов Cloud Shell, чтобы сбросить состояние компьютера.

## <a name="minimize--maximize-cloud-shell-window"></a>Свертывание и развертывание окна Cloud Shell
![](media/using-the-shell-window/minmax.png)
* Щелкните значок "Свернуть" в верхнем правом углу окна, чтобы скрыть окно. Для его отображения повторно щелкните значок Cloud Shell.
* Щелкните значок "Развернуть", чтобы установить максимальную высоту окна. Чтобы восстановить окно до предыдущего размера, щелкните "Восстановить".

## <a name="concurrent-sessions"></a>Параллельные сеансы
Cloud Shell допускает выполнение нескольких параллельных сеансов на вкладках браузера. Таким образом каждый сеанс может существовать как отдельный процесс Bash.
При выходе из сеанса необходимо выйти из всех окон, так как каждый процесс выполняется независимо, несмотря на то что они выполняются на одной машине.

## <a name="copy-and-paste"></a>Копирование и вставка
[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Изменение размера окна Cloud Shell
* Щелкните верхний край панели и растяните или сожмите окно Cloud Shell, чтобы изменить его размер.

## <a name="scrolling-text-display"></a>Прокрутка текста
* Прокручивайте текст с помощью мыши или сенсорной панели.

## <a name="changing-the-text-size"></a>Изменение размера текста
![](media/using-the-shell-window/text-size.png)
* Щелкните значок "Параметры" в верхнем левом углу окна, а затем наведите указатель мыши на параметр "Размер текста" и выберите нужный размер текста. Выбранные параметры будут сохранены во всех сеансах.

## <a name="exit-command"></a>Команда выхода
Команда `exit` позволяет завершить активный сеанс. Это происходит по умолчанию после 20 минут простоя.

## <a name="next-steps"></a>Дальнейшие действия

[Краткое руководство по Bash в Cloud Shell](quickstart.md)
[Краткое руководство по PowerShell в Cloud Shell](quickstart-powershell.md)