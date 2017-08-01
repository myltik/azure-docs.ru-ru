---
title: "Настройка правил брандмауэра веб-приложения для шлюза приложений Azure с помощью портала | Документация Майкрософт"
description: "Эта статья содержит сведения о том, как настроить правила брандмауэра веб-приложения в шлюзе приложений с помощью портала."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: 094ae187ec8ba5ff38f174ee4cf139d30db7e057
ms.contentlocale: ru-ru
ms.lasthandoff: 06/14/2017


---

# <a name="customize-web-application-firewall-rules-through-the-portal"></a>Настройка правил брандмауэра веб-приложения на портале

Брандмауэр веб-приложения шлюза приложений обеспечивает защиту веб-приложений с помощью наборов правил OWASP CRS. Некоторые правила могут приводить к ложным срабатываниям и блокировке реального трафика.  Поэтому в шлюзе приложений можно настраивать правила и группы правил в шлюзе приложений с включенным брандмауэром веб-приложений. Дополнительные сведения о конкретных правилах и группах правил см. в статье [List of web application firewall CRS rule groups and rules offered](application-gateway-crs-rulegroups-rules.md) (Список правил и групп правил CRS для брандмауэра веб-приложения).

>[!NOTE]
> Если шлюз приложений не использует уровень WAF, вам будет предоставлена возможность обновить шлюз приложений до уровня WAF, как показано на следующем рисунке.

![Включение WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Просмотр правил и групп правил

Перейдите в шлюз приложений и выберите **Брандмауэр веб-приложения**.  Щелкните **Advanced rule configuration** (Расширенная настройка правил).  На странице со всеми группами правил отобразится таблица с выбранным набором правил.

![Настройка с отключением правил][1]

## <a name="search-for-rules-to-disable"></a>Поиск правил для отключения

В колонке параметров брандмауэра веб-приложения есть функция фильтрации правил по текстовому поиску. В результате отобразятся только правила и группы правил, которые содержат искомый текст.

![Поиск правил][2]

## <a name="disable-rule-groups-and-rules"></a>Отключение правил и групп правил

При отключении правил можно отключить всю группу правил или определенные правила в одной или нескольких группах.  Сняв флажки напротив правил, которые нужно отключить, нажмите кнопку **Сохранить**.  Изменения в шлюзе приложений будут сохранены.

![Сохранение изменений][3]

## <a name="next-steps"></a>Дальнейшие действия

После настройки с отключением правил узнайте, как просматривать журналы WAF, ознакомившись с разделом [Ведение журнала диагностики](application-gateway-diagnostics.md#diagnostic-logs).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

