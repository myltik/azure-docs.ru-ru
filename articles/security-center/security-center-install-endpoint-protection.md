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
ms.date: 11/23/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: b8a69d89f335c00c5ddc3c201e33a66e1dea1da5
ms.openlocfilehash: 6ccb5367b1c7c6cae7b9d35b6a5471c8edfe6f85


---
# <a name="install-endpoint-protection-in-azure-security-center"></a>Установка Endpoint Protection в центре безопасности Azure
Если на вашем компьютере еще не включена защита от вредоносных программ, центр безопасности Azure порекомендует подготовить программу защиты от вредоносных программ для виртуальных машин (ВМ) Azure. Эта рекомендация относится только к виртуальным машинам Windows. В настоящее время эта рекомендация проверяет наличие защитника Windows или TrendMicro Deep Security. В будущем планируется добавление дополнительных решений для защиты конечных точек.

> [!NOTE]
> В документе приводится обзор службы с помощью примера развертывания.  Он не является пошаговым руководством.
>
>

## <a name="implement-the-recommendation"></a>Выполнение рекомендаций
1. В колонке **Рекомендации** выберите **Установить Endpoint Protection**.
   ![Выберите "Установить Endpoint Protection"][1]
2. Откроется колонка **Установить Endpoint Protection** со списком виртуальных машин, на которых не включена защита от вредоносных программ. Выберите в списке виртуальные машины, на которых вы хотите установить защиту от вредоносных программ, и щелкните **Install on VMs**(Установить на виртуальных машинах).
   ![Выберите виртуальные машины, на которых необходимо установить защиту от вредоносных программ][2]
3. Откроется колонка **Select Endpoint Protection** (Выбрать Endpoint Protection), в которой вы можете выбрать необходимое решение для защиты от вредоносных программ. В этом примере мы выберем **Антивредоносное ПО Майкрософт**.
   ![Select Endpoint Protection][3]
4. На экран будут выведены дополнительные сведения о решении защиты от вредоносных программ. Нажмите кнопку **Создать**.
   ![Создание решения для защиты от вредоносных программ][4]
5. В колонке **Добавить расширение** введите необходимые параметры конфигурации и нажмите кнопку **ОК**. Дополнительные сведения о параметрах конфигурации см. в статье [Антивредоносное ПО Майкрософт для облачных служб и виртуальных машин Azure](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

Теперь на выбранных виртуальных машинах включено [антивредоносное ПО Майкрософт](../security/azure-security-antimalware.md).

## <a name="see-also"></a>Дополнительные материалы
В этой статье объясняется, как выполнить рекомендацию центра безопасности по установке Endpoint Protection. Чтобы больше узнать о включении программы защиты от вредоносных программ в Azure, см. следующие статьи:

* [Антивредоносное ПО Майкрософт для облачных служб и виртуальных машин Azure](../security/azure-security-antimalware.md) — узнайте, как развернуть антивредоносное ПО Майкрософт.

Дополнительные сведения о Центре безопасности см. в следующих статьях:

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



<!--HONumber=Nov16_HO4-->


