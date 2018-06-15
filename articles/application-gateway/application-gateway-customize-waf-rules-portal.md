---
title: Настройка правил брандмауэра веб-приложения для шлюза приложений Azure с помощью портала Azure | Документация Майкрософт
description: Эта статья содержит сведения о том, как настроить правила брандмауэра веб-приложения в шлюзе приложений с помощью портала Azure.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: victorh
ms.openlocfilehash: ae61e3a8308e95c16ccde71de37fb10666ef0df9
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32769126"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Настройка правил брандмауэра веб-приложения с помощью портала Azure

> [!div class="op_single_selector"]
> * [портал Azure](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Брандмауэр веб-приложения (WAF) шлюза приложений Azure обеспечивает защиту веб-приложений с помощью основного набора правил (CRS) открытого проекта безопасности веб-приложений (OWASP). Некоторые правила могут приводить к ложным срабатываниям и блокировке реального трафика. Поэтому шлюз приложений предоставляет возможность настроить правила и группы правил. Дополнительные сведения о конкретных правилах и группах правил см. в статье [Список групп правил и правил CRS брандмауэра веб-приложения](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Если шлюз приложений не использует уровень WAF, на правой панели будет предоставлена возможность обновить шлюз приложений до уровня WAF. 

![Включение WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Просмотр правил и групп правил

**Чтобы просмотреть правила и группы правил, сделайте следующее:**
   1. Перейдите к шлюзу приложений, а затем выберите **Брандмауэр веб-приложения**.  
   2. Выберите **Advanced rule configuration** (Расширенная настройка правил).  
   В этом представлении появится страница со всеми группами правил и таблицей с выбранным набором правил. Для правила установлены все флажки.

![Настройка отключенных правил][1]

## <a name="search-for-rules-to-disable"></a>Поиск правил для отключения

В колонке **параметров брандмауэра веб-приложения** есть функция фильтрации правил по текстовому поиску. Отобразятся только правила и группы правил, которые содержат искомый текст.

![Поиск правил][2]

## <a name="disable-rule-groups-and-rules"></a>Отключение правил и групп правил

При отключении правил можно отключить всю группу правил или определенные правила в одной или нескольких группах. 

**Чтобы отключить группы правил или конкретные правила, сделайте следующее:**

   1. Найдите правила или группы правил, которые необходимо отключить.
   2. Снимите флажки для правил, которые необходимо отключить. 
   2. Щелкните **Сохранить**. 

![Сохранение изменений][3]

## <a name="next-steps"></a>Дополнительная информация

После настройки с отключением правил вы можете узнать, как просматривать журналы WAF. Дополнительные сведения см. в разделе [Журналы диагностики](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
