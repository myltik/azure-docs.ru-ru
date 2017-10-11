---
title: "Масштабирование квот и ограничений в лаборатории в Azure DevTest Labs | Документация Майкрософт"
description: "Узнайте, как масштабировать лабораторию в Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: tarcher
ms.openlocfilehash: f11ed42b474e4f208eac92689bfa33fb188d15a1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Масштабирование квот и ограничений в DevTest Labs
При работе в DevTest Labs вы, вероятно, замечали, что для некоторых ресурсов Azure существуют определенные ограничения по умолчанию, которые могут влиять на работу службы DevTest Labs. Такие ограничения называют **квотами**.

> [!NOTE]
> Служба DevTest Labs не устанавливает каких-либо квот. Любые квоты, которые вам могут встретиться, — это ограничения по умолчанию, действующие на уровне всей подписки Azure.

Каждый ресурс Azure можно использовать, пока не достигнута его квота. Каждая подписка имеет свои квоты, а процесс использования отслеживается отдельно для каждой подписки.

Например, что каждой подписки задана квота по умолчанию в 20 ядер. Таким образом, при создании в лаборатории виртуальных машин, каждая из которых имеет четыре ядра, можно создать только пять виртуальных машин. 

В статье [Подписка Azure, границы, квоты и ограничения службы](https://docs.microsoft.com/azure/azure-subscription-service-limits) перечислены некоторые наиболее распространенные квоты для ресурсов Azure. К ресурсам, которые чаще всего используются в лаборатории и для которых могут быть квоты, относятся ядра виртуальных машин, общедоступные IP-адреса, сетевой интерфейс, управляемые диски, назначение ролей RBAC и каналы ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Просмотр сведений об использовании и квотах
Следующие шаги показывают, как просмотреть сведения о текущих квотах в подписке на определенные ресурсы Azure, а также как узнать, какой процент каждой квоты уже использован.

1. Выполните вход на [портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Щелкните **Больше служб**, а затем выберите в списке **Выставление счетов**.
1. В колонке "Выставление счетов" выберите подписку.
4. Выберите **Использование и квоты**.

   ![Кнопка "Использование и квоты"](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Появится колонка "Использование и квоты" , в которой отображаются различные ресурсы, доступные в данной подписке, и процент квоты, используемый каждым ресурсом.

   ![Квоты и использование](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Запрос дополнительных ресурсов в подписке
При достижении ограничения квоты ресурса предельное значение по умолчанию в подписке можно увеличить до максимального предела, как описано в статье [Подписка Azure, границы, квоты и ограничения службы](https://docs.microsoft.com/azure/azure-subscription-service-limits).

Следующие действия помогут вам запросить увеличение квоты с помощью [портала Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Щелкните **Больше служб**, выберите **Выставление счетов**, а затем — **Использование и квоты**.
1. В колонке "Использование и квоты" нажмите кнопку **Запросить увеличение**.

   ![Кнопка "Запросить увеличение"](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Чтобы заполнить и отправить запрос, введите необходимые сведения на всех трех вкладках формы **Новый запрос в службу поддержки**.

   ![Форма запроса на увеличение](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

В записи блога [Understanding Azure Limits and Increases](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) (Основные сведения об ограничениях и увеличении квот Azure) предоставлены дополнительные сведения об обращении в службу поддержки Azure с запросом на увеличение квоты.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Дальнейшие действия
* Изучите [коллекцию шаблонов быстрого запуска Azure Resource Manager для DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
