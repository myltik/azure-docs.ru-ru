---
title: "Выставление счетов клиентам и взимание средств за использование в Azure Stack | Документация Майкрософт"
description: "Узнайте, как получить сведения о потреблении ресурсов в Azure Stack."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: a9afc7b6-43da-437b-a853-aab73ff14113
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: alfredop
ms.openlocfilehash: ea7510c239ee07a9a27f3e682e61a6b08eb5694d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="usage-and-billing-in-azure-stack"></a>Потребление ресурсов и выставление счетов в Azure Stack

Потребление описывает объем ресурсов, которые использует пользователь. Azure Stack собирает сведения о потреблении для каждого пользователя и использует эти сведения для выставления счетов. Эта статья описывает алгоритмы выставления счетов пользователям за потребление ресурсов Azure Stack, а также методы доступа к этой информации для аналитики, взимания средств за использование и т. д.

Azure Stack включает инфраструктуру для сбора и статистической обработки данных об использовании всех ресурсов и для отправки этих данных в Azure Commerce. Вы можете получить доступ к этим данным или экспортировать их в систему выставления счетов, используя специальный адаптер. Также их можно экспортировать в средство бизнес-аналитики, например Microsoft Power BI. Экспортированные сведения передаются в систему взимания средств за использование или используются в аналитике.

![Концептуальная модель адаптера выставления счетов, который соединяет Azure Stack с приложением для выставления счетов.](media/azure-stack-billing-and-chargeback/image1.png)

## <a name="usage-pipeline"></a>Конвейер потребления

Каждый поставщик ресурсов в Azure Stack передает данные о потреблении ресурсов. Служба потребления периодически (ежечасно или ежедневно) выполняет статистическую обработку этих данных и сохраняет их в базе данных. Операторы и пользователи Azure Stack могут обращаться к сохраненным сведениям о потреблении с помощью API-интерфейсов использования. 

Если вы [зарегистрируете экземпляр Azure Stack в Azure](azure-stack-register.md), настраивается "мост потребления" для отправки данных о потреблении в Azure Commerce. Когда эти данные попадают в Azure, вы можете просмотреть их на портале выставления счетов или с помощью API использования Azure. Статья [Отчеты по данным об использовании](azure-stack-usage-reporting.md) содержит сведения о том, какие данные о потреблении передаются в Azure. 

На следующем рисунке представлены ключевые компоненты конвейера потребления.

![Конвейер потребления](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Какие сведения о потреблении мне доступны, и как их получить?

Поставщики Azure Stack, такие как службы вычислений, хранилища и сети, ежечасно создают данные о потреблении для каждой подписки. Данные о потреблении содержат сведения о ресурсах, включая имя ресурса, для какой подписки он использовался, использованный объем и т. д. Информацию о ресурсах идентификаторов измерения вы найдете в разделе [часто задаваемых вопросов об API использования](azure-stack-usage-related-faq.md). 

Собранные данные о потреблении [передаются в Azure](azure-stack-usage-reporting.md) для выставления счетов, которые можно просмотреть на портале выставления счетов Azure. 

> [!NOTE]
> Предоставление данных о потреблении не является обязательным для Пакета средств разработки Azure Stack и пользователей интегрированных систем Azure Stack, использующих модель производительности. Дополнительные сведения о лицензировании в Azure Stack см. на странице с информацией о [пакетах и ценах](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

Портал выставления счетов Azure демонстрирует данные о потреблении только для платных ресурсов. Кроме сведений о платных ресурсах, Azure Stack собирает данные о потреблении для более широкого набора ресурсов. Вы можете просмотреть их в среде Azure Stack, используя REST API или PowerShell. Операторы Azure Stack могут получать данные о потреблении для всех пользовательских подписок, а отдельный пользователь — только о потребленных им ресурсах.

## <a name="retrieve-usage-information"></a>Получение сведений о потреблении

Чтобы данные о потреблении создавались, должны существовать активно работающие ресурсы, например, действующая виртуальная машина или учетная запись хранения, содержащая некоторые данные. Если вы не знаете, есть ли у вас активные ресурсы в Azure Stack Marketplace, разверните виртуальную машину, откройте для нее колонку мониторинга и проверьте, выполняется ли виртуальная машина. Следующие командлеты PowerShell позволяют просмотреть данные о потреблении:

1. [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Установка PowerShell для Azure Stack);
2. [Настройка пользователя](user/azure-stack-powershell-configure-user.md) или [оператора Azure Stack](azure-stack-powershell-configure-admin.md) в среде PowerShell; 

3. Для получения данных о потреблении используйте командлет PowerShell [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates).

   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

## <a name="next-steps"></a>Дополнительная информация

[Report Azure Stack usage data to Azure](azure-stack-usage-reporting.md) (Передача в Azure данных о потреблении из Azure Stack)

[Provider Resource Usage API](azure-stack-provider-resource-api.md) (API использования ресурсов для поставщиков);

[Tenant Resource Usage API](azure-stack-tenant-resource-usage-api.md) (API использования ресурсов для клиентов);

[Часто задаваемые вопросы об использовании](azure-stack-usage-related-faq.md)

