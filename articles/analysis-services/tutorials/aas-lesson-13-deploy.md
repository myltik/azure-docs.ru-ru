---
title: "Учебник по службам Azure Analysis Services: занятие 13 &quot;Развертывание&quot; | Документы Майкрософт"
description: "Описывает развертывание учебного проекта в службах Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 8e3e1be572aa66ab46f894a2e5f395d1e6f2ea23
ms.contentlocale: ru-ru
ms.lasthandoff: 06/03/2017

---
# <a name="lesson-13-deploy"></a>Занятие 13. Развертывание

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

В этом занятии вы настроите свойства развертывания, указав сервер Analysis Services в Azure или сервер SQL Server vNext Analysis Services в локальной среде, а также имя модели. Затем вы развернете модель на этом экземпляре. После развертывания модели пользователи смогут подключаться к ней с помощью клиентского приложения по созданию отчетов. Дополнительные сведения см. в статье [Deploy to Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy) (Развертывание в службы Azure Analysis Services).  
  
Предполагаемое время выполнения этого занятия: **5 минут**  
  
## <a name="prerequisites"></a>Предварительные требования  
Этот раздел входит в учебник по табличному моделированию, который следует изучать в предложенном порядке. Прежде чем выполнять задачи в этом разделе, нужно завершить предыдущее занятие: [Занятие 12. Анализ в Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).  

**Важно!** Если вы установили пример базы данных AdventureWorksDW2014 на SQL Server и развертываете модель на сервере служб Azure Analysis Services, требуется [локальный шлюз данных](../analysis-services-gateway.md).
  
## <a name="deploy-the-model"></a>Развертывание модели  
  
#### <a name="to-configure-deployment-properties"></a>Настройка свойств развертывания  

  
1.  В **обозревателе решений** щелкните правой кнопкой мыши проект **AW Internet Sales** и выберите пункт **Свойства**.  
  
2.  В свойстве **Сервер** из области **Сервер развертывания** диалогового окна **Страницы свойств AW Internet Sales** введите имя сервера служб Analysis Services в Azure или локальной среде.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
 
    > [!IMPORTANT]  
    > Чтобы развернуть модель на удаленном экземпляре Analysis Services, для него нужно обладать правами администратора.  
  
3.  В свойстве **База данных** введите **Adventure Works Internet Sales**.  
  
4.  В свойстве **Имя модели** введите **Модель Adventure Works Internet Sales**.  
  
5.  Проверьте выбранные параметры и нажмите кнопку **ОК**.  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Развертывание Adventure Works Internet Sales
  
1.  В **обозревателе решений** щелкните правой кнопкой мыши проект **AW Internet Sales** и выберите пункт **Сборка**.  

2.  Щелкните правой кнопкой мыши проект **AW Internet Sales** и выберите пункт **Развернуть**.

    При развертывании в службах Azure Analysis Services может потребоваться ввести вашу учетную запись. Укажите свою учетную запись в организации и пароль, например nancy@adventureworks.com. Это учетная запись должна обладать правами администратора на экземпляре сервера.
  
    Появляется диалоговое окно "Развертывание", отображающее состояние развертывания метаданных и все таблицы, включенные в модель.  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. После успешного завершения развертывания нажмите кнопку **Закрыть**.  
  
## <a name="conclusion"></a>Заключение  
Поздравляем! Вы создали и развернули свою первую табличную модель служб Analysis Services. Этот учебник научил вас выполнять типичные задачи по созданию табличной модели. Теперь, когда модель Adventure Works Internet Sales развернута, можно воспользоваться SQL Server Management Studio для управления ею, а также для создания скриптов обработки и плана резервного копирования. Теперь пользователи могут подключиться к модели с помощью клиентского приложения по ведению отчетов, такого как Microsoft Excel или Power BI.  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>Что дальше?
*  [Дополнительное занятие. Динамическая безопасность](../tutorials/aas-supplemental-lesson-dynamic-security.md)

*  [Дополнительное занятие. Строки детализации](../tutorials/aas-supplemental-lesson-detail-rows.md)

*  [Дополнительное занятие. Неоднородные иерархии](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)

