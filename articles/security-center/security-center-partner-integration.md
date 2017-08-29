---
title: "Интеграция партнеров в центре безопасности Azure | Документация Майкрософт"
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
ms.date: 08/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 44beafeff5cbe58ac8ca37632879f6ffc2b67e53
ms.contentlocale: ru-ru
ms.lasthandoff: 08/19/2017

---
# <a name="partner-integration-in-azure-security-center"></a>Интеграция партнеров в центре безопасности Azure

В этой статье описывается, как можно интегрировать центр безопасности Azure с партнерами для повышения общей безопасности. Центр безопасности предоставляет расширенные возможности интеграции Azure, используя преимущества Azure Marketplace для сертификации и выставления счетов партнеров.

> [!NOTE] 
> С начала июня 2017 г. в центре безопасности используется Microsoft Monitoring Agent для сбора и хранения данных. Дополнительные сведения см. в статье [Миграция платформы центра безопасности Azure](security-center-platform-migration.md). В этой статье описывается, как будет работать центр безопасности после перехода к использованию Microsoft Monitoring Agent.
>

## <a name="why-deploy-partner-solutions-from-security-center"></a>Зачем нужно развертывать партнерские решения через центр безопасности

Ниже приведены четыре основные причины для использования интеграции партнеров в центре безопасности.

- **Простота развертывания.** Рекомендации центра безопасности существенно упрощают развертывание партнерских решений. Процесс развертывания можно полностью автоматизировать с помощью настроек по умолчанию и топологии сети. Пользователи также могут использовать вариант частичной автоматизации, который дает дополнительную гибкость и предоставляет расширенные настройки.
- **Интегрированные обнаружения.** События безопасности автоматически собираются из партнерских решений, объединяются и отображаются в списках оповещений и инцидентов центра безопасности. Эти события используются совместно с результатами сканирования других источников, что дает дополнительные возможности для обнаружения угроз.
- **Единый мониторинг работоспособности и единое управление.** Пользователи могут использовать интегрированные события работоспособности, чтобы отслеживать все партнерские решения в одном расположении. Предоставляются базовые возможности для управления и быстрый доступ к партнерским решениям для расширенной настройки.
- **Экспорт в SIEM.** Пользователи могут экспортировать все оповещения центра безопасности и партнеров в CEF-формат, передавая их в локальные системы управления сведениями о безопасности и событиями (SIEM) с использованием интеграции журналов Azure (предварительная версия).


## <a name="partners-that-integrate-with-security-center"></a>Партнеры, которые интегрируются с центром безопасности

Сейчас центр безопасности поддерживает интеграцию со следующими решениями.

- Endpoint Protection ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec и [антивредоносное ПО Майкрософт для облачных служб и службы "Виртуальные машины" Azure](https://docs.microsoft.com/azure/security/azure-security-antimalware)). 
- Брандмауэр веб-приложения ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) и [шлюз приложений Azure](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)). 
- Брандмауэр следующего поколения ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) и [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)). 
- Оценка уязвимостей ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/)).  

Постепенно для центра безопасности будет увеличено число партнеров по этим категориям, а также появятся новые категории. 

## <a name="deploy-a-partner-solution"></a>Развертывание решения партнера

В зависимости от настроек среды Azure и определенной политики безопасности центр безопасности может рекомендовать развернуть партнерское решение. В рекомендации центра безопасности описан процесс выбора и установки партнерского решения. В зависимости от типа решения и партнера развертывание может выполняться по-разному. Дополнительные сведения см. в следующих статьях:

- [Установка Endpoint Protection](security-center-install-endpoint-protection.md)
- [Добавление брандмауэра веб-приложения](security-center-add-web-application-firewall.md)
- [Добавление брандмауэра следующего поколения в центре безопасности Azure](security-center-add-next-generation-firewall.md)
- [Оценка уязвимостей не установлена](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>Управление партнерскими решениями

После развертывания выберите параметр **Решения партнеров** в колонке **Центр безопасности**, чтобы просмотреть сведения о работоспособности решения и выполнить основные задачи управления. Дополнительные сведения об управлении партнерскими решениями с помощью центра безопасности Azure см. [в этой статье](security-center-partner-solutions.md).

![Интеграция партнеров](./media/security-center-partner-integration/security-center-partner-integration-fig1-new2.png)

> [!NOTE]
> Symantec Endpoint Protection поддерживается только для обнаружения. Оповещения о работоспособности недоступны.
>

## <a name="see-also"></a>См. также

Из этой статьи вы узнали, как интегрировать партнерские решения в центре безопасности Azure. Дополнительные сведения о центре безопасности см. в следующих статьях:

* [Руководство по планированию использования центра безопасности Azure и работе в нем](security-center-planning-and-operations-guide.md).
* [Управление оповещениями системы безопасности и управление ими в центре безопасности](security-center-managing-and-responding-alerts.md)
* [Основные сведения об оповещениях системы безопасности в центре безопасности Azure](security-center-alerts-type.md).
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md). Узнайте, как отслеживать работоспособность ресурсов Azure.
* [Мониторинг решений партнеров с помощью центра безопасности Azure](security-center-partner-solutions.md). Узнайте, как отслеживать работоспособность партнерских решений.
* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md). Ознакомьтесь с ответами на часто задаваемые вопросы об использовании этой службы.
* [Блог по безопасности Azure](http://blogs.msdn.com/b/azuresecurity/). Записи блога, посвященные безопасности и соответствию требованиям в Azure.

