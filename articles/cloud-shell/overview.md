---
title: "Обзор Azure Cloud Shell (предварительная версия) | Документация Майкрософт"
description: "Обзор Azure Cloud Shell."
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
ms.openlocfilehash: 47627bc6df93db1d92aa29350fe6e48039dc6f1b
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---
# <a name="overview-of-azure-cloud-shell-preview"></a>Обзор Azure Cloud Shell (предварительная версия)
Azure Cloud Shell — это интерактивная доступная браузеру оболочка для управления ресурсами Azure.

![](media/startup.gif)

## <a name="features"></a>Функции
### <a name="browser-based-shell-experience"></a>Оболочка на основе браузера
Cloud Shell предоставляет доступ к браузерному интерфейсу командной строки, созданному с учетом задач управления Azure. Используйте Cloud Shell для работы из локального компьютера с неограниченными возможностями, которые может обеспечить только облако.

### <a name="pre-configured-azure-workstation"></a>Предварительно настроенная рабочая станция Azure
Cloud Shell поставляется с предварительно установленными популярными инструментами командной строки и поддержкой языков, чтобы вы могли работать быстрее.

[Полный список инструментария Azure Cloud Shell.](features.md#tools)

### <a name="automatic-authentication"></a>Автоматическая проверка подлинности
Cloud Shell автоматически безопасно проходит проверку подлинности при каждом сеансе, чтобы обеспечить мгновенный доступ к ресурсам через Azure CLI 2.0.

### <a name="connect-your-azure-file-storage"></a>Подключение хранилища файлов Azure
Машины Cloud Shell являются временными, поэтому требуют подключения общей папки Azure для хранения домашнего каталога.
При первом запуске Cloud Shell предлагает создать группу ресурсов, учетную запись хранения и общую папку от вашего имени. Это одноразовое действие, которое автоматически применяется для всех сеансов. 

![](media/storage-prompt.png)

Учетная запись локально избыточного хранилища (LRS) создается от вашего имени с общей папкой Azure, содержащей образ диска по умолчанию объемом 5 ГБ.
Этот образ диска используется для синхронизации и сохранения вашего домашнего каталога. Применяются расходы на обычное хранение.
От вашего имени будет создано три ресурса:
1. Группа ресурсов с именем: `cloud-shell-storage-<region>`
2. Учетная запись хранения с именем: `cs-uniqueGuid`
3. Общая папка с именем: `cs-<user>-<domain>-com-uniqueGuid`

[Подробнее о том, как Cloud Shell сохраняет файлы] (persisting-shell-storage.md).

## <a name="concepts"></a>Основные понятия
* Cloud Shell работает на временной машине, предоставляемой для каждого сеанса и для каждого пользователя отдельно.
* Время ожидания Cloud Shell истекает через 10 минут при отсутствии интерактивных действий.
* Доступ к Cloud Shell можно получить только при наличии присоединенной общей папки.
* Cloud Shell назначается один компьютер на учетную запись пользователя.
* Разрешения задаются как для обычного пользователя Linux.

[Дополнительные сведения о всех функциях Cloud Shell.](features.md)

## <a name="examples"></a>Примеры
* Создание и изменение скриптов для управления ресурсами Azure из любого браузера.
* Одновременное управление ресурсами через портал Azure и Azure CLI 2.0.
* Испытание Azure CLI 2.0.

[Опробуйте все эти примеры в кратком руководстве по Cloud Shell.](quickstart.md)

## <a name="pricing"></a>Цены
Машина, на которой размещена Cloud Shell, является бесплатной. На ней должна быть подключена общая папка Azure для хранения домашнего каталога. Применяются расходы на обычное хранение.

## <a name="supported-browsers"></a>Поддерживаемые браузеры
Cloud Shell рекомендуется использовать с Chrome, Edge и Safari. Хотя служба Cloud Shell и поддерживается Chrome, Firefox, Safari, IE и Edge, она все еще зависит от определенных параметров браузера.
Сведения об известных ограничениях Cloud Shell см. в [этой статье](limitations.md).
