---
title: "Интеграция решений по обеспечению безопасности в центре безопасности Azure | Документация Майкрософт"
description: "Сведения о том, как интегрировать центр безопасности Azure с партнерами для повышения общей безопасности ресурсов Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 42cbc442d03cdca04d380d05d9e904355476099e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2017
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Интеграция решений по обеспечению безопасности в центре безопасности Azure
В этом документе описана процедура управления решениями по обеспечению безопасности, подключенными к центру безопасности Azure, и добавления новых решений.

## <a name="integrated-azure-security-solutions"></a>Интегрированные решения по обеспечению безопасности Azure
Центр безопасности упрощает включение интегрированных решений по обеспечению безопасности в Azure. Основные преимущества:

- **Упрощенное развертывание.** Центр безопасности предлагает упрощенную подготовку партнерских решений обеспечения безопасности. Для таких решений, как антивредоносные программы и решения по оценке уязвимостей, центр безопасности может подготовить необходимый агент на виртуальных машинах, а для решений брандмауэра он может выполнить большую часть необходимой конфигурации сети.
- **Интегрированные обнаружения.** События безопасности автоматически собираются из партнерских решений, объединяются и отображаются в списках оповещений и инцидентов центра безопасности. Эти события используются совместно с результатами сканирования других источников, что дает дополнительные возможности для обнаружения угроз.
- **Единый мониторинг работоспособности и единое управление.** Интеграция событий работоспособности позволяет клиентам отслеживать все партнерские решения в одном расположении. Предоставляются базовые возможности для управления и быстрый доступ к партнерским решениям для расширенной настройки.

Сейчас к интегрированным решениям безопасности относятся следующие решения:

- Защита конечных точек ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec, Защитник Windows и System Center Endpoint Protection (SCEP)).
- Брандмауэр веб-приложения ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) и [шлюз приложений Azure](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)).
- Брандмауэр следующего поколения ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) и [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)).
- Оценка уязвимостей ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/)).  

Интеграция защиты конечных точек может различаться в зависимости от решения. В следующей таблице приведены дополнительные сведения о каждом решении:

| Защита конечных точек               | Платформы                             | Установка центра безопасности | Обнаружение центра безопасности |
|-----------------------------------|---------------------------------------|------------------------------|---------------------------|
| Защитник Windows (антивредоносное ПО Майкрософт)                  | Windows Server 2016                   | Нет, встроен в ОС           | Да                       |
| System Center Endpoint Protection (антивредоносное ПО Майкрософт) | Windows Server 2012 R2, 2012, 2008 R2 | Через расширение                | Да                       |
| Trend Micro — все версии         | Семейство Windows Server                 | Через расширение                | Да                       |
| Symantec v12.1.1100+                     | Семейство Windows Server                 | Нет                           | Да                        |
| MacAfee                           | Семейство Windows Server                 | Нет                           | Нет                        |
| Kaspersky                         | Семейство Windows Server                 | Нет                           | Нет                        |
| Sophos                            | Семейство Windows Server                 | Нет                           | Нет                        |



## <a name="how-security-solutions-are-integrated"></a>Как интегрируются решения по обеспечению безопасности
Решения по обеспечению безопасности Azure, развертываемые из центра безопасности, подключены автоматически. Вы также можете подключить другие источники данных безопасности:

- защиту идентификации Azure AD
- Компьютеры, работающие локально или в других облаках.
- Решения по обеспечению безопасности, поддерживающие общий формат событий (CEF).
- Microsoft Advanced Threat Analytics.

![Интеграция решений партнеров](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Управление интегрированными решениями по обеспечению безопасности Azure и другими источниками данных

После развертывания можно просмотреть сведения о работоспособности встроенного решения по обеспечению безопасности Azure и выполнить основные задачи управления. Вы также можете подключить другие типы источников данных безопасности, такие как оповещения службы "Защита идентификации Azure Active Directory" и журналы брандмауэра в общем формате событий (CEF). На панели мониторинга центра безопасности выберите "Решения безопасности".

### <a name="connected-solutions"></a>Подключенные решения

Раздел **Подключенные решения** содержит решения безопасности, которые подключены к центру безопасности, и сведения о состоянии работоспособности для каждого из них.  

![Подключенные решения](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

### <a name="discovered-solutions"></a>Обнаруженные решения

В разделе **Обнаруженные решения** показаны все решения, которые были добавлены с помощью Azure. Он также содержит все решения, которые центр безопасности предлагает подключить к нему.

![Обнаруженные решения](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Центр безопасности автоматически обнаруживает другие решения по обеспечению безопасности в Azure. Сюда входят такие решения Azure, как [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), а также партнерские решения, запущенные в Azure. Для интеграции этих решений с центром безопасности выберите **Подключиться**.

### <a name="add-data-sources"></a>Добавление источников данных

В разделе **Добавление источников данных** содержатся сведения о других доступных источниках данных, которые можно подключить. Чтобы получить инструкции по добавлению данных из любого из этих источников, щелкните **Добавить**.

![Источники данных](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как интегрировать партнерские решения в центре безопасности. Дополнительные сведения о центре безопасности см. в следующих статьях:

* [Руководство по планированию использования центра безопасности Azure и работе в нем](security-center-planning-and-operations-guide.md).
* [Подключение Microsoft Advanced Threat Analytics к центру безопасности Azure](security-center-ata-integration.md)
* [Подключение службы "Защита идентификации Azure Active Directory" к центру безопасности Azure](security-center-aadip-integration.md)
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md). Узнайте, как отслеживать работоспособность ресурсов Azure.
* [Мониторинг решений партнеров с помощью центра безопасности Azure](security-center-partner-solutions.md). Узнайте, как отслеживать работоспособность партнерских решений.
* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md). Ознакомьтесь с ответами на часто задаваемые вопросы об использовании центра безопасности.
* [Блог по безопасности Azure](http://blogs.msdn.com/b/azuresecurity/). Записи блога, посвященные безопасности и соответствию требованиям в Azure.
