---
title: "Ограничения Azure Cloud Shell (предварительная версия) | Документация Майкрософт"
description: "Обзор ограничений Azure Cloud Shell."
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
ms.date: 07/10/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 3ac234b27a0675f484018c357a65ab65049ceee0
ms.contentlocale: ru-ru
ms.lasthandoff: 08/09/2017

---

# <a name="limitations-of-azure-cloud-shell"></a>Ограничения Azure Cloud Shell
Azure Cloud Shell имеет следующие известные ограничения.

## <a name="system-state-and-persistence"></a>Состояние системы и сохраняемость
Компьютер, предоставляющий сеанс Cloud Shell, является временным и перезапускается, если сеанс не используется в течение 20 минут. Для подключения Cloud Shell требуется общая папка. Поэтому ваша подписка должна иметь возможность настраивать ресурсы хранилища для доступа к Cloud Shell. Дополнительные рекомендации:
* С подключенным хранилищем сохраняются только изменения в каталоге `$Home` или каталоге `clouddrive`.
* Общие папки можно подключить только в пределах [назначенного региона](persisting-shell-storage.md#mount-a-new-clouddrive).
* Файлы Azure поддерживают только локально избыточное хранилище в геоизбыточные учетные записи.

## <a name="user-permissions"></a>Разрешения пользователя
Разрешения задаются как для обычных пользователей без доступа к sudo. Все, что устанавливается за пределами каталога `$Home`, не сохранится.
Хотя некоторые команды в каталоге `clouddrive`, такие как `git clone`, не имеют необходимых разрешений, каталог `$Home` их имеет.

## <a name="browser-support"></a>Поддержка браузеров
Cloud Shell поддерживает последние версии Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox и Apple Safari. Использование Safari в режиме защищенного просмотра не поддерживается.

## <a name="copy-and-paste"></a>Копирование и вставка
Так как сочетания клавиш Ctrl+C и Ctrl+V не работают в качестве команд копирования и вставки на компьютерах Windows, используйте для этого сочетания клавиш Ctrl+Ins и Shift+Ins.

Вызов команд копирования и вставки щелчком правой кнопки мыши также доступен, но эта функция зависит от параметров доступа к буферу обмена в каждом конкретном браузере.

## <a name="editing-bashrc"></a>Изменение файла .bashrc
Будьте внимательны при изменении BASHRC-файла, поскольку некоторые изменения могут привести к непредвиденным ошибкам в Cloud Shell.

## <a name="bashhistory"></a>.bash_history
Журнал команд bash может быть несогласованным из-за перебоев в сеансе Cloud Shell или из-за одновременных сеансов.

## <a name="usage-limits"></a>Ограничения использования
Cloud Shell предназначен для интерактивного использования. В результате любые длительные неинтерактивные сеансы завершаются без предупреждения.

## <a name="network-connectivity"></a>Сетевое подключение
Задержки в Cloud Shell зависят от локального подключения к Интернету. Cloud Shell будет пытаться продолжать работать, какие бы инструкции не отправлялись.

## <a name="next-steps"></a>Дальнейшие действия
[Краткое руководство по Cloud Shell](quickstart.md)

