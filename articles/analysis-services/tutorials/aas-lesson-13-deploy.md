---
title: 'Учебник по службам Azure Analysis Services: занятие 13 "Развертывание" | Документы Майкрософт'
description: Описывает развертывание учебного проекта в службах Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9b953428525e7970fef7224e65200cf9811b6304
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596286"
---
# <a name="deploy"></a>Развертывание

На этом уроке вы настроите свойства развертывания, указав сервер Azure Analysis Services, на котором оно будет выполняться, развертывания и имя модели. Затем вы развернете модель на этом экземпляре. После развертывания модели пользователи смогут подключаться к ней с помощью клиентского приложения по созданию отчетов. Дополнительные сведения см. в статье [Deploy to Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy) (Развертывание в службы Azure Analysis Services).  
  
Предполагаемое время выполнения этого занятия: **5 минут**  
  
## <a name="prerequisites"></a>предварительным требованиям  
Эта статья является одной из частей руководства по созданию табличных моделей. Эти части следует изучать в предложенном порядке. Прежде чем выполнять задачи в этом разделе, нужно завершить предыдущее занятие: [Занятие 12. Анализ в Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).  

> [!IMPORTANT]  
> Вы должны иметь [права администратора](../analysis-services-server-admins.md) на удаленном сервере служб Analysis Services для его развертывания.  

> [!IMPORTANT]  
> Если вы установили пример базы данных AdventureWorksDW2014 на локальном сервере SQL Server и развертываете свою модель на сервере служб Azure Analysis Services, необходим [локальный шлюз данных](../analysis-services-gateway.md).
  
## <a name="deploy-the-model"></a>Развертывание модели  
  
#### <a name="to-configure-deployment-properties"></a>Настройка свойств развертывания  

  
1.  В **обозревателе решений** щелкните правой кнопкой мыши проект **AW Internet Sales** и выберите пункт **Свойства**.  
  
2.  В диалоговом окне **AW Internet Sales Property Pages** (Страницы свойств AW Internet Sales) в разделе **Сервер развертывания** в свойстве **Сервер** введите полное название сервера.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
  
3.  В свойстве **База данных** введите **Adventure Works Internet Sales**.  
  
4.  В свойстве **Имя модели** введите **Модель Adventure Works Internet Sales**.  
  
5.  Проверьте выбранные параметры и нажмите кнопку **ОК**.  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Развертывание Adventure Works Internet Sales
  
1.  В **обозревателе решений** щелкните правой кнопкой мыши проект **AW Internet Sales** и выберите пункт **Сборка**.  

2.  Щелкните правой кнопкой мыши проект **AW Internet Sales** и выберите пункт **Развернуть**.

    При развертывании в службах Azure Analysis Services может потребоваться ввести вашу учетную запись. Укажите свою учетную запись в организации и пароль, например nancy@adventureworks.com. Эта учетная запись должна быть у администраторов на сервере.
  
    Появляется диалоговое окно "Развертывание", отображающее состояние развертывания метаданных и все таблицы, включенные в модель.  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. После успешного завершения развертывания нажмите кнопку **Закрыть**.  
  

В этом занятии описывается самый популярный и простой способ развертывания табличной модели из SSDT. Более сложные способы развертывания, например развертывание с помощью мастера или автоматическое развертывание с помощью XML для аналитики и объектов AMO, обеспечивают дополнительную гибкость и согласованность, а также позволяют выполнять развертывание по расписанию. Дополнительные сведения см. в статье, посвященной [развертыванию решения табличной модели](https://docs.microsoft.com/sql/analysis-services/tabular-models/tabular-model-solution-deployment-ssas-tabular).

## <a name="conclusion"></a>Заключение  
Поздравляем! Вы создали и развернули свою первую табличную модель служб Analysis Services. Этот учебник научил вас выполнять типичные задачи по созданию табличной модели. Теперь, когда модель Adventure Works Internet Sales развернута, можно воспользоваться SQL Server Management Studio для управления ею, а также для создания скриптов обработки и плана резервного копирования. Теперь пользователи могут подключиться к модели с помощью клиентского приложения по ведению отчетов, такого как Microsoft Excel или Power BI.  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>Что дальше?
[Подключение с помощью Power BI](../analysis-services-connect-pbi.md)   
[Дополнительное занятие. Динамическая безопасность](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Дополнительное занятие. Строки детализации](../tutorials/aas-supplemental-lesson-detail-rows.md)   
[Дополнительное занятие. Неоднородные иерархии](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
