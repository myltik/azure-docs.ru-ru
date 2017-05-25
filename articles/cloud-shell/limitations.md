---
title: "Ограничения Azure Cloud Shell (предварительная версия) | Документация Майкрософт"
description: "Обзор ограничений Azure Cloud Shell."
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
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: c007b73375c8c82248228f4e549c0ac95640d7ec
ms.contentlocale: ru-ru
ms.lasthandoff: 05/17/2017

---

# <a name="limitations-for-azure-cloud-shell"></a>Ограничения Azure Cloud Shell
Azure Cloud Shell имеет следующие известные ограничения.

## <a name="system-state-and-persistence"></a>Состояние системы и сохраняемость
Компьютер, предоставляющий сеанс Cloud Shell, является временным и перезапускается, если сеанс не используется в течение 10 минут. Для подключения Cloud Shell требуется общая папка.
* С подключенным хранилищем сохраняются только изменения в каталоге `$Home` или каталоге `clouddrive`.
  * Общие папки можно подключить только в пределах [назначенного региона](persisting-shell-storage.md#pre-requisites-for-manual-mounting).
  * Файлы Azure поддерживают только учетные записи хранения LRS и GRS.

## <a name="user-permissions"></a>Разрешения пользователя
Разрешения задаются как для обычных пользователей без доступа к sudo. Все, что устанавливается за пределами каталога $Home, не сохранится.
Некоторые команды, такие как `git clone` в каталоге `clouddrive`, не имеют необходимых разрешений, хотя каталог $Home их имеет.

## <a name="browser-support"></a>Поддержка браузеров
Cloud Shell поддерживает последние версии Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox и Apple Safari. Использование Safari в режиме защищенного просмотра не поддерживается.

## <a name="copy-and-paste"></a>Копирование и вставка
Сочетания клавиш Ctrl+V и Ctrl+C не работают в качестве команд копирования и вставки на компьютерах Windows. Используйте для этого сочетания клавиш Ctrl+Ins и Shift+Ins.
Вызов команд копирования и вставки щелчком правой кнопки мыши также доступен, однако эта функция зависит от параметров доступа к буферу обмена в каждом конкретном браузере.

## <a name="usage-limits"></a>Ограничения использования
Cloud Shell предназначен для интерактивного использования, поэтому любые длительные неинтерактивные сеансы завершаются без предупреждения.

## <a name="network-connectivity"></a>Сетевое подключение
Задержки в Cloud Shell зависят от локального подключения к Интернету. Cloud Shell будет пытаться продолжать работать, какие бы инструкции не отправлялись.

## <a name="next-steps"></a>Дальнейшие действия
[Краткое руководство по Cloud Shell](quickstart.md)
