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
ms.date: 06/23/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c69630688e4bcd68ab3b4ee6d9fdb0e0c46d04b
ms.openlocfilehash: 85460ea37d251b53074de9d832610d253700f2c1
ms.contentlocale: ru-ru
ms.lasthandoff: 06/24/2017


---
<a id="partner-integration-in-azure-security-center" class="xliff"></a>

# Интеграция партнеров в центре безопасности Azure
В этом документе объясняется, как можно интегрировать центр безопасности Azure с партнерами для повышения общей безопасности и расширения возможностей Azure, применяя Azure Marketplace для сертификации и выставления счетов партнеров.

>[!NOTE] 
>С начала июня 2017 г. в центре безопасности будет использоваться Microsoft Monitoring Agent для сбора и хранения данных. См. дополнительные сведения о [миграции платформы центра безопасности Azure](security-center-platform-migration.md). В этой статье описывается, как будет работать центр безопасности после перехода к использованию Microsoft Monitoring Agent.
>

<a id="why-deploy-partners-solutions-from-security-center" class="xliff"></a>

## Зачем нужно развертывать партнерские решения через центр безопасности?

Ниже приведены четыре основных причины для использования интеграции партнеров в центре безопасности.

- **Простота развертывания**. Рекомендации центра безопасности существенно упрощают развертывание партнерских решений. Процесс развертывания можно полностью автоматизировать с помощью конфигурации по умолчанию и топологии сети. Также можно использовать вариант частичной автоматизации, который дает дополнительную гибкость в настройке конфигурации.
- **Интегрированные обнаружения**. События безопасности автоматически собираются из партнерских решений, объединяются и отображаются в списках оповещений и инцидентов центра безопасности. Эти события используются совместно с результатами сканирования других источников, что дает дополнительные возможности для обнаружения угроз.
- **Единый мониторинг работоспособности и единое управление**. Интеграция событий работоспособности позволяет отслеживать все партнерские решения в одном расположении. Предоставляются базовые возможности для управления и быстрый доступ к партнерским решениям для расширенной настройки.
- **Экспорт в SIEM**. Теперь пользователи могут экспортировать все оповещения центра безопасности и партнеров в CEF-формат, передавая их в локальную систему SIEM с помощью функции интеграции журналов Microsoft Azure (предварительная версия)


<a id="what-partners-are-integrated-with-security-center" class="xliff"></a>

## Для каких партнеров возможна интеграция с центром безопасности?
Сейчас центр безопасности поддерживает интеграцию со следующими решениями:

- Endpoint Protection ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec, [антивредоносная программа Майкрософт для Azure](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- Брандмауэр веб-приложения ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets), [WAF шлюза приложений](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- Брандмауэр следующего поколения ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) и [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- Оценка уязвимостей ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Постепенно для центра безопасности будет увеличено число партнеров по этим категориям, а также появятся новые категории. 

<a id="how-to-deploy-a-partner-solution" class="xliff"></a>

## Как развернуть решение партнера?

В зависимости от конфигурации среды Azure и определенной политики безопасности центр безопасности может рекомендовать развернуть партнерское решение. В этой рекомендации описан процесс выбора и установки партнерского решения. С этого момента развертывание будет выполняться по-разному (в зависимости от типа решения и партнера). Дополнительные сведения см. по ссылкам ниже:

- [Добавление брандмауэра веб-приложения](security-center-add-web-application-firewall.md)
- [Добавить брандмауэр следующего поколения](security-center-add-next-generation-firewall.md)
- [Установка Endpoint Protection](security-center-install-endpoint-protection.md)
- [Оценка уязвимостей не установлена](security-center-vulnerability-assessment-recommendations.md)

<a id="how-to-manage-partner-solutions" class="xliff"></a>

## Управление партнерскими решениями

Развернув партнерское решение, можно просмотреть сведения о его работоспособности и выполнить основные задачи управления на плитке "Решения партнеров" на основной панели мониторинга центра безопасности. Дополнительные сведения об управлении партнерскими решениями с помощью центра безопасности Azure см. [в этой статье](security-center-partner-solutions.md).

![Интеграция партнеров](./media/security-center-partner-integration/security-center-partner-integration-fig1-1-newUI.png)

> [!NOTE]
> Symantec Endpoint Protection поддерживает только обнаружение; функция оповещений о работоспособности недоступна.
>

<a id="see-also" class="xliff"></a>

## См. также
В этом документе описывается, как интегрировать партнерские решения с помощью центра безопасности Azure. Дополнительные сведения о Центре безопасности см. в следующих статьях:

* [Руководство по планированию использования центра безопасности Azure и работе в нем](security-center-planning-and-operations-guide.md)
* [Управление оповещениями безопасности в Центре безопасности Azure и реагирование на них](security-center-managing-and-responding-alerts.md)
* [Типы оповещений системы безопасности в центре безопасности Azure](security-center-alerts-type.md)
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md) — узнайте, как отслеживать работоспособность ресурсов Azure.
* [Мониторинг решений партнеров с помощью центра безопасности Azure](security-center-partner-solutions.md) — узнайте, как отслеживать состояние работоспособности решений партнеров.
* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md) — часто задаваемые вопросы об использовании этой службы.
* [Блог по безопасности Azure](http://blogs.msdn.com/b/azuresecurity/) — публикации блога, посвященные безопасности и соответствию требованиям в Azure.

