---
title: "Использование окна Azure Cloud Shell (предварительная версия) | Документация Майкрософт"
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
ms.date: 09/25/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fb242abfbea79bc8c242a7a89b3d775cf74a0617
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="using-the-azure-cloud-shell-window"></a>Использование окна Azure Cloud Shell

В этом документе содержатся сведения о том, как использовать окно Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Переключение между средами Bash и PowerShell
![](media/using-the-shell-window/env-selector.png)

Для переключения между средами Bash и PowerShell используйте селектора сред на панели инструментов Cloud Shell.

## <a name="restart-cloud-shell"></a>Перезапуск Cloud Shell
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Перезапуск Cloud Shell сбросит состояние машины, и все файлы, не сохраненные в общей папке, будут утеряны.

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
* Щелкните значок "Параметры" в верхнем левом углу окна, а затем наведите указатель мыши на параметр "Размер текста" и выберите нужный размер текста.

## <a name="exit-command"></a>Команда выхода
Команда `exit` позволяет завершить активный сеанс. Это происходит по умолчанию, если взаимодействия нет более 10 минут.

## <a name="next-steps"></a>Дальнейшие действия

[Краткое руководство по Bash в Cloud Shell](quickstart.md)
[Краткое руководство по PowerShell в Cloud Shell](quickstart-powershell.md)
