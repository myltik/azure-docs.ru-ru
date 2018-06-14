---
title: Использование окна Azure Cloud Shell | Документация Майкрософт
description: Общие сведения о том, как использовать окно Azure Cloud Shell.
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: juluk
ms.openlocfilehash: 43da2bf5b66ff7db03a6fb5c2e1ceaebe322bcbb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
ms.locfileid: "28920000"
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
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Изменение размера окна Cloud Shell
* Щелкните верхний край панели и растяните или сожмите окно Cloud Shell, чтобы изменить его размер.

## <a name="scrolling-text-display"></a>Прокрутка текста
* Прокручивайте текст с помощью мыши или сенсорной панели.

## <a name="changing-the-text-size"></a>Изменение размера текста
![](media/using-the-shell-window/text-size.png)
* Щелкните значок "Параметры" в верхнем левом углу окна, а затем наведите указатель мыши на параметр "Размер текста" и выберите нужный размер текста. Выбранные параметры будут сохранены во всех сеансах.

## <a name="exit-command"></a>Команда выхода
Команда `exit` позволяет завершить активный сеанс. Это происходит по умолчанию после 20 минут простоя.

## <a name="next-steps"></a>Дополнительная информация

[Краткое руководство по Bash в Cloud Shell](quickstart.md)
[Краткое руководство по PowerShell в Cloud Shell](quickstart-powershell.md)