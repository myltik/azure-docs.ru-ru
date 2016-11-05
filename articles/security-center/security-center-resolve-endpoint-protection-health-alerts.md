---
title: Разрешение оповещений о работоспособности Endpoint Protection в центре безопасности Azure | Microsoft Docs
description: В этом документе объясняется, как выполнить рекомендацию центра безопасности Azure **Разрешить оповещения работоспособности Endpoint Protection**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2016
ms.author: terrylan

---
# Разрешение оповещений о работоспособности Endpoint Protection в центре безопасности Azure
Центр безопасности Azure будет рекомендовать разрешить выявленные оповещения о работоспособности Endpoint Protection. Центр безопасности позволяет просмотреть, на каких виртуальных машинах произошли сбои Endpoint Protection и сколько сбоев произошло.

> [!NOTE]
> В документе приводится обзор службы с помощью примера развертывания. Он не является пошаговым руководством.
> 
> 

## Выполнение рекомендаций
1. В колонке **Рекомендации** выберите **Разрешить оповещения работоспособности Endpoint Protection**. ![Разрешение оповещений работоспособности Endpoint Protection][1]
2. Откроется колонка **Сбой Endpoint Protection**, в которой перечислены виртуальные машины со сбоями, а также количество сбоев для каждой из них. Выберите виртуальную машину из списка. ![Сбой Endpoint Protection][2]
3. Откроется колонка **Список сбоев** для выбранной виртуальной машины, содержащая список сбоев. Выберите сбой из списка, чтобы получить дополнительные сведения. Откроется колонка с информацией о выбранном сбое. ![Список сбоев][3] ![События сбоя][4]

## См. также
Дополнительные сведения о Центре безопасности см. в следующих статьях:

* [Настройка политик безопасности в Центре безопасности Azure](security-center-policies.md) — узнайте, как настроить политики безопасности для подписок и групп ресурсов Azure.
* [Управление рекомендациями по безопасности в Центре безопасности Azure](security-center-recommendations.md) — узнайте, как рекомендации могут помочь вам защитить ресурсы Azure.
* [Наблюдение за работоспособностью системы безопасности в центре безопасности Azure](security-center-monitoring.md) — узнайте, как наблюдать за работоспособностью ресурсов Azure.
* [Управление оповещениями безопасности в Центре безопасности Azure и реагирование на них](security-center-managing-and-responding-alerts.md) — узнайте, как управлять оповещениями системы безопасности и реагировать на них.
* [Мониторинг решений партнеров с помощью центра безопасности Azure](security-center-partner-solutions.md) — узнайте, как отслеживать состояние работоспособности решений партнеров.
* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md) — часто задаваемые вопросы об использовании этой службы.
* [Блог по безопасности Azure](http://blogs.msdn.com/b/azuresecurity/) — последние новости и информация об обеспечении безопасности в Azure.

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png

<!---HONumber=AcomDC_0803_2016-->