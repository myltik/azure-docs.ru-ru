---
title: "Разрешение оповещений о работоспособности Endpoint Protection в центре безопасности Azure | Документация Майкрософт"
description: "В этом документе объясняется, как выполнить рекомендацию центра безопасности Azure **Разрешить оповещения работоспособности Endpoint Protection**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4050f453-98fc-4314-8438-d476469757fb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5e6b136d6bd3b11fb82126d104fd0cb149255118


---
# <a name="resolve-endpoint-protection-health-alerts-in-azure-security-center"></a>Разрешение оповещений о работоспособности Endpoint Protection в центре безопасности Azure
Центр безопасности Azure будет рекомендовать разрешить выявленные оповещения о работоспособности Endpoint Protection.  Центр безопасности позволяет просмотреть, на каких виртуальных машинах произошли сбои Endpoint Protection и сколько сбоев произошло.

> [!NOTE]
> В документе приводится обзор службы с помощью примера развертывания. Он не является пошаговым руководством.
> 
> 

## <a name="implement-the-recommendation"></a>Выполнение рекомендаций
1. В колонке **Рекомендации** выберите **Разрешить оповещения работоспособности Endpoint Protection**.
   ![Разрешение оповещений о работоспособности Endpoint Protection][1]
2. Откроется колонка **Сбой Endpoint Protection** , в которой перечислены виртуальные машины со сбоями, а также количество сбоев для каждой из них. Выберите виртуальную машину из списка.
   ![Endpoint protection failure][2]
3. Откроется колонка **Список сбоев** для выбранной виртуальной машины, содержащая список сбоев. Выберите сбой из списка, чтобы получить дополнительные сведения. Откроется колонка с информацией о выбранном сбое.
   ![Список сбоев][3]
   ![Событие сбоя][4]

## <a name="see-also"></a>Дополнительные материалы
Дополнительные сведения о Центре безопасности см. в следующих статьях:

* [Настройка политик безопасности в Центре безопасности Azure](security-center-policies.md)— узнайте, как настроить политики безопасности для подписок и групп ресурсов Azure.
* [Управление рекомендациями по безопасности в Центре безопасности Azure](security-center-recommendations.md)— узнайте, как рекомендации могут помочь вам защитить ресурсы Azure.
* [Наблюдение за работоспособностью системы безопасности в центре безопасности Azure](security-center-monitoring.md)— узнайте, как наблюдать за работоспособностью ресурсов Azure.
* [Управление оповещениями безопасности в Центре безопасности Azure и реагирование на них](security-center-managing-and-responding-alerts.md)— узнайте, как управлять оповещениями системы безопасности и реагировать на них.
* [Мониторинг решений партнеров с помощью центра безопасности Azure](security-center-partner-solutions.md) — узнайте, как отслеживать состояние работоспособности решений партнеров.
* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md)— часто задаваемые вопросы об использовании этой службы.
* [Блог по безопасности Azure](http://blogs.msdn.com/b/azuresecurity/)— последние новости и информация об обеспечении безопасности в Azure.

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png



<!--HONumber=Nov16_HO3-->


