---
title: Управление версиями пакетов SDK клиента и сервера в мобильных приложениях и мобильных службах | Документация Майкрософт
description: Список пакетов SDK клиента и сведения о совместимости с версиями пакетов SDK сервера для мобильных служб и мобильных приложений Azure
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 37bf36af535eb9b5c8b0ba38434b71f1a6686811
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "27593380"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Управление версиями клиента и сервера в мобильных приложениях и мобильных службах
Последняя версия мобильных служб Azure — компонент **Мобильные приложения** службы приложений Azure.

Пакеты SDK для клиента и сервера мобильных приложений основаны на аналогичных пакетах мобильных служб, но *не совместимы* с ними.
Другими словами, пакет SDK клиента *мобильных приложений* необходимо использовать с пакетом SDK сервера *мобильных приложений* (точно так же и для *мобильных служб*). Этот контракт реализуется посредством специального значения заголовка, используемого пакетами SDK для клиента и сервера, `ZUMO-API-VERSION`.

Примечание. Когда в этом документе упоминается внутренний сервер *мобильных служб*, он не обязательно должен размещаться в мобильных службах. Теперь можно выполнить миграцию мобильной службы для работы в службе приложений без внесения изменений в код, однако служба по-прежнему будет использовать версии пакета SDK для *мобильных служб*.

Дополнительные сведения о переносе в службу приложений, не изменяя код, см. в статье [Перенос существующей мобильной службы Azure в службу приложений Azure].

## <a name="header-specification"></a>Спецификация заголовка
Ключ `ZUMO-API-VERSION` можно указать в заголовке HTTP или в строке запроса. Его значение представляет строку версии в формате **x.y.z**.

Например: 

GET https://service.azurewebsites.net/tables/TodoItem

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Отказ от проверки версий
От проверки версий можно отказаться, задав для параметра приложения **MS_SkipVersionCheck** значение **true**. Укажите это значение в файле web.config или в разделе параметров приложения портала Azure.

> [!NOTE]
> Работа мобильных приложений отличается от мобильных служб в нескольких аспектах, в частности, в сфере автономной синхронизации, проверки подлинности и push-уведомлений. Вы можете отказаться от проверки версий только после выполнения тщательного тестирования, чтобы изменения в работе не нарушили функциональность приложения.
>
>

## <a name="summary-of-compatibility-for-all-versions"></a>Сводные сведения о совместимости для всех версий
Ниже приведены сведения о совместимости между всеми типами клиентов и серверов. Серверная часть называется мобильными **службами** или мобильными **приложениями** в зависимости от используемого пакета SDK сервера.

|  | **мобильных служб**  | **Мобильные приложения**  |
| --- | --- | --- |
| [Клиенты мобильных служб] |кнопку "ОК" |Ошибка\* |
| [Клиенты мобильных приложений] |Ошибка\* |кнопку "ОК" |

\*Это поведение можно изменить, указав параметр **MS_SkipVersionCheck**.

<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Клиент и сервер мобильных служб
Пакеты SDK клиента в таблице ниже совместимы с **мобильными службами**.

Примечание. Пакеты SDK для клиента мобильных служб *не* отправляют значение заголовка для `ZUMO-API-VERSION`. Если служба получает этот заголовок или значение строки запроса, будет возвращена ошибка, если только вы явно не отказались от проверки версий (см. выше).

### <a name="MobileServicesClients"></a> Пакеты SDK для клиента мобильных *служб*
| Платформа клиента | Version (версия) | Значение заголовка версии |
| --- | --- | --- |
| Управляемый клиент (Windows, Xamarin) |[1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) |Недоступно |
| iOS |[2.2.2](http://aka.ms/gc6fex) |Недоступно |
| Android |[2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) |Недоступно |
| HTML |[1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) |Недоступно |

### <a name="mobile-services-server-sdks"></a>Пакеты SDK для сервера мобильных *служб*
| Платформа сервера | Version (версия) | Принятый заголовок версии |
| --- | --- | --- |
| .NET |[WindowsAzure.MobileServices.Backend.* версия 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) |**Отсутствует заголовок версии** |
| Node.js |(ожидается в ближайшее время) |**Отсутствует заголовок версии** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Поведение внутренних серверов мобильных служб
| ZUMO-API-VERSION | Значение параметра MS_SkipVersionCheck | Ответ |
| --- | --- | --- |
| Не указан |Любой |200 – OK |
| Любое значение |Истина |200 – OK |
| Любое значение |False/не указан |400 – неверный запрос |

## <a name="2.0.0"></a>Клиент и сервер мобильных приложений Azure
### <a name="MobileAppsClients"></a> Пакеты SDK для клиента мобильных *приложений*
Проверка версии была добавлена, начиная со следующих версий пакета SDK для клиента **мобильных приложений Azure**:

| Платформа клиента | Version (версия) | Значение заголовка версии |
| --- | --- | --- |
| Управляемый клиент (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Пакеты SDK для сервера мобильных *приложений*
Проверка версий входит в состав следующих версий пакета SDK сервера:

| Платформа сервера | SDK | Принятый заголовок версии |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Поведение внутренних серверов мобильных приложений
| ZUMO-API-VERSION | Значение параметра MS_SkipVersionCheck | Ответ |
| --- | --- | --- |
| x.y.z или значение NULL |Истина |200 – OK |
| Null |False/не указан |400 – неверный запрос |
| 1.x.y |False/не указан |400 – неверный запрос |
| 2.0.0-2.x.y |False/не указан |200 – OK |
| 3.0.0-3.x.y |False/не указан |400 – неверный запрос |

## <a name="next-steps"></a>Дальнейшие действия
* [Перенос существующей мобильной службы Azure в службу приложений Azure]

[Клиенты мобильных служб]: #MobileServicesClients
[Клиенты мобильных приложений]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Перенос существующей мобильной службы Azure в службу приложений Azure]: app-service-mobile-migrating-from-mobile-services.md
