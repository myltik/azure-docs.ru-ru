---
title: "Интеграция партнеров в центре безопасности Azure | Документация Майкрософт"
description: "В этом документе объясняется, как интегрировать центр безопасности Azure с партнерами для повышения общей безопасности ресурсов Azure."
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
ms.date: 04/28/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8dddfc8929ab1a0c44522ed2a2596e2c82e3987d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/01/2017


---
# <a name="partner-integration-in-azure-security-center"></a>Интеграция партнеров в центре безопасности Azure
В этом документе объясняется, как можно интегрировать центр безопасности Azure с партнерами для повышения общей безопасности и расширения возможностей Azure, применяя Azure Marketplace для сертификации и выставления счетов партнеров.

## <a name="why-deploy-partners-solutions-from-security-center"></a>Зачем нужно развертывать партнерские решения через центр безопасности?

Ниже приведены четыре основных причины для использования интеграции партнеров в центре безопасности.

- **Простота развертывания**. Рекомендации центра безопасности существенно упрощают развертывание партнерских решений. Процесс развертывания можно полностью автоматизировать с помощью конфигурации по умолчанию и топологии сети. Также можно использовать вариант частичной автоматизации, который дает дополнительную гибкость в настройке конфигурации.
- **Интегрированные обнаружения**. События безопасности автоматически собираются из партнерских решений, объединяются и отображаются в списках оповещений и инцидентов центра безопасности. Эти события используются совместно с результатами сканирования других источников, что дает дополнительные возможности для обнаружения угроз.
- **Единый мониторинг работоспособности и единое управление**. Интеграция событий работоспособности позволяет отслеживать все партнерские решения в одном расположении. Предоставляются базовые возможности для управления и быстрый доступ к партнерским решениям для расширенной настройки.
- **Экспорт в SIEM**. Теперь пользователи могут экспортировать все оповещения центра безопасности и партнеров в CEF-формат, передавая их в локальную систему SIEM с использованием интеграции журналов Microsoft Azure (предварительная версия)


## <a name="what-partners-are-integrated-with-security-center"></a>Для каких партнеров возможна интеграция с центром безопасности?
Центр безопасности сейчас поддерживает интеграцию со следующими партнерами.

- Endpoint Protection ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html)) 
- Брандмауэр веб-приложения ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets), [WAF шлюза приложений](https://azure.microsoft.com/en-us/blog/azure-web-application-firewall-waf-generally-available/)) 
- Брандмауэр следующего поколения ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) и [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- Оценка уязвимостей ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) — предварительная версия)  

Постепенно для центра безопасности будет увеличено число партнеров по этим категориям, а также появятся новые категории. 

## <a name="how-to-deploy-a-partner-solution"></a>Как развернуть решение партнера?

В зависимости от конфигурации среды Azure и определенной политики безопасности центр безопасности может рекомендовать развернуть партнерское решение. В этой рекомендации описан процесс выбора и установки партнерского решения. С этого момента развертывание будет выполняться по-разному (в зависимости от типа решения и партнера). Дополнительные сведения см. по ссылкам ниже:

- [Добавление брандмауэра веб-приложения](security-center-add-web-application-firewall.md)
- [Добавить брандмауэр следующего поколения](security-center-add-next-generation-firewall.md)
- [Установка Endpoint Protection](security-center-install-endpoint-protection.md)
- [Оценка уязвимостей не установлена](security-center-vulnerability-assessment-recommendations.md)

## <a name="how-to-manage-partner-solutions"></a>Управление партнерскими решениями

Развернув партнерское решение, можно просмотреть сведения о его работоспособности и выполнить основные задачи управления на плитке "Решения партнеров" на основной панели мониторинга центра безопасности. Дополнительные сведения об управлении партнерскими решениями с помощью центра безопасности Azure см. [в этой статье](security-center-partner-solutions.md).

![Интеграция партнеров](./media/security-center-partner-integration/security-center-partner-integration-fig1-new.png)


## <a name="see-also"></a>Дополнительные материалы
В этом документе описывается, как интегрировать партнерские решения с помощью центра безопасности Azure. Дополнительные сведения о Центре безопасности см. в следующих статьях:

* [Руководство по планированию использования центра безопасности Azure и работе в нем](security-center-planning-and-operations-guide.md)
* [Управление оповещениями безопасности в Центре безопасности Azure и реагирование на них](security-center-managing-and-responding-alerts.md)
* [Типы оповещений системы безопасности в центре безопасности Azure](security-center-alerts-type.md)
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md) — узнайте, как отслеживать работоспособность ресурсов Azure.
* [Мониторинг решений партнеров с помощью центра безопасности Azure](security-center-partner-solutions.md) — узнайте, как отслеживать состояние работоспособности решений партнеров.
* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md) — часто задаваемые вопросы об использовании этой службы.
* [Блог по безопасности Azure](http://blogs.msdn.com/b/azuresecurity/) — публикации блога, посвященные безопасности и соответствию требованиям в Azure.

