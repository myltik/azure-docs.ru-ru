---
title: "Использование окна Azure Cloud Shell (предварительная версия) | Документация Майкрософт"
description: "Пошаговое руководство по окну Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4e78dadb7fa036dcb4c5706fe774cbd85d57a746
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---

# <a name="using-the-shell-window"></a>Использование окна Azure Cloud Shell
В этом документе содержатся сведения о том, как использовать окно Azure Cloud Shell.

## <a name="concurrent-sessions"></a>Параллельные сеансы
Cloud Shell допускает выполнение нескольких параллельных сеансов на вкладках браузера. Таким образом каждый сеанс может существовать как отдельный процесс Bash.
При выходе из сеанса необходимо выйти из всех окон, так как каждый процесс выполняется независимо, несмотря на то что они выполняются на одной машине.

## <a name="restart-cloud-shell"></a>Перезапуск Cloud Shell
![](media/recycle.png)
* Щелкните значок перезапуска на панели инструментов, чтобы перезапустить машину Cloud Shell.

> Внимание! Перезапуск Cloud Shell сбросит состояние машины, и все файлы, не сохраненные в clouddrive, будут утеряны.

## <a name="minimize--maximize-cloud-shell-window"></a>Свертывание и развертывание окна Cloud Shell
![](media/minmax.png)
* Щелкните значок "Свернуть" в верхнем правом углу окна, чтобы скрыть окно. Для его отображения повторно щелкните значок Cloud Shell.
* Щелкните значок "Развернуть", чтобы установить максимальную высоту окна. Чтобы восстановить окно до предыдущего размера, щелкните "Восстановить".

## <a name="copy-and-paste"></a>Копирование и вставка
* Windows: нажмите клавиши `Ctrl-insert` и `Shift-insert` или щелкните правой кнопкой мыши и выберите в раскрывающемся списке команду для копирования и вставки.
  * FireFox и IE могут не поддерживать разрешения буфера обмена корректно.
* Mac OS: нажмите клавиши `Cmd-c` и `Cmd-v` или щелкните правой кнопкой мыши и выберите в раскрывающемся списке команду для копирования и вставки.

## <a name="resize-cloud-shell-window"></a>Изменение размера окна Cloud Shell
* Щелкните верхний край панели и растяните или сожмите окно Cloud Shell, чтобы изменить его размер.

## <a name="scrolling-text-display"></a>Прокрутка текста
* Прокручивайте текст с помощью мыши или сенсорной панели.

## <a name="exit-command"></a>Команда выхода
Команда `exit` позволяет завершить активный сеанс. Это происходит по умолчанию, если взаимодействия нет более 10 минут.

## <a name="next-steps"></a>Дальнейшие действия
[Краткое руководство по Cloud Shell](quickstart.md)  
