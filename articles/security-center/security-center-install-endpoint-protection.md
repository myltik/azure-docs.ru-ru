---
title: "Установка Endpoint Protection в центре безопасности Azure | Документация Майкрософт"
description: "В этом документе объясняется, как выполнить рекомендацию центра безопасности Azure **Установить Endpoint Protection**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: efb86a0ae362c30a6772c391a499154b7ae2a697
ms.contentlocale: ru-ru
ms.lasthandoff: 07/12/2017

---
# <a name="install-endpoint-protection-in-azure-security-center"></a>Установка Endpoint Protection в центре безопасности Azure
Центр безопасности Azure рекомендует установить решение для защиты конечных точек на виртуальных машинах Azure, если оно еще не включено. Эта рекомендация относится только к виртуальным машинам Windows.

> [!NOTE]
> В этом примере развертывания используется антивредоносное ПО Майкрософт. Список партнеров, решения которых интегрированы с центром безопасности, см. в статье [Интеграция партнеров в центре безопасности Azure](security-center-partner-integration.md#partners-that-integrate-with-security-center).  
>
>

## <a name="implement-the-recommendation"></a>Выполнение рекомендаций

> [!NOTE]
> В документе приводится обзор службы с помощью примера развертывания.  Этот документ не является пошаговым руководством.
>
>

1. В колонке **Рекомендации** выберите **Установить Endpoint Protection**.
   ![Выберите "Установить Endpoint Protection"][1]
2. Откроется колонка **Установить Endpoint Protection** со списком виртуальных машин, на которых нет решения для защиты конечных точек. Выберите в списке виртуальные машины, на которых нужно установить решение для защиты конечных точек, и щелкните **Установить на виртуальных машинах**.
   ![Выбор виртуальных машин для установки решения для защиты конечных точек][2]
3. Откроется колонка **Выбор Endpoint Protection**, в которой вы можете выбрать необходимое решение для защиты конечных точек. В этом примере мы выберем **Антивредоносное ПО Майкрософт**.
   ![Select Endpoint Protection][3]
4. Будут выведены дополнительные сведения о решении для защиты конечных точек. Нажмите кнопку **Создать**.
   ![Создание решения для защиты от вредоносных программ][4]
5. В колонке **Добавить расширение** введите необходимые параметры конфигурации и нажмите кнопку **ОК**. Дополнительные сведения о параметрах конфигурации см. в статье [Антивредоносное ПО Майкрософт для облачных служб и виртуальных машин Azure](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

Теперь на выбранных виртуальных машинах включено [антивредоносное ПО Майкрософт](../security/azure-security-antimalware.md).

## <a name="see-also"></a>Дополнительные материалы
В этой статье объясняется, как выполнить рекомендацию центра безопасности по установке Endpoint Protection. Дополнительные сведения о включении антивредоносного ПО Майкрософт в Azure см. в следующей статье:

* [Антивредоносное ПО Майкрософт для облачных служб и виртуальных машин Azure](../security/azure-security-antimalware.md) — узнайте, как развернуть антивредоносное ПО Майкрософт.

Дополнительные сведения о центре безопасности см. в следующих статьях:

* [Настройка политик безопасности в Центре безопасности Azure](security-center-policies.md) — узнайте, как настраивать политики безопасности.
* [Управление рекомендациями по безопасности в Центре безопасности Azure](security-center-recommendations.md) — узнайте, как рекомендации могут помочь вам защитить ресурсы Azure.
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md) — узнайте, как наблюдать за работоспособностью ресурсов Azure.
* [Управление оповещениями безопасности в Центре безопасности Azure и реагирование на них](security-center-managing-and-responding-alerts.md) — узнайте, как управлять оповещениями системы безопасности и реагировать на них.
* [Мониторинг решений партнеров с помощью центра безопасности Azure](security-center-partner-solutions.md) — узнайте, как отслеживать состояние работоспособности решений партнеров.
* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md) — часто задаваемые вопросы об использовании этой службы.
* [Блог по безопасности Azure](http://blogs.msdn.com/b/azuresecurity/) — публикации блога, посвященные безопасности и соответствию требованиям в Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png

