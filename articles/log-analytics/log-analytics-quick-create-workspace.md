---
title: Создание рабочей области в Azure Log Analytics | Документация Майкрософт
description: Узнайте, как создать рабочую область Log Analytics для включения решений по управлению и сбора данных из облачной и локальной сред.
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: magoedte
ms.openlocfilehash: e05d620a457edf427c946366a9caca471ac8531e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30833138"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Создание рабочей области Log Analytics на портале Azure
На портале Azure вы можете настроить рабочую область Log Analytics — уникальную среду с собственным репозиторием данных, источниками данных и решениями.  Действия, описанные в этой статье, помогут вам настроить сбор данные из следующих источников:

* ресурсы Azure в подписке;
* локальные компьютеры, которые отслеживает System Center Operations Manager;
* коллекции устройств из System Center Configuration Manager; 
* Данные диагностики и журнала из службы хранилища Azure

Сведения о других источниках, таких как виртуальные машины Azure и компьютеры Windows или Linux в вашей среде, см. в статьях ниже.

*  [Сбор данных о виртуальных машинах Azure](log-analytics-quick-collect-azurevm.md) 
*  [Сбор данных с компьютеров Linux, размещенных в вашей среде](log-analytics-quick-collect-linux-computer.md)
*  [Сбор данных с компьютеров Windows, размещенных в вашей среде](log-analytics-quick-collect-windows-computer.md)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="log-in-to-azure-portal"></a>Вход на портал Azure
Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Создание рабочей области
1. На портале Azure щелкните **Все службы**. В списке ресурсов введите **Log Analytics**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Log Analytics**.<br><br> ![портал Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Щелкните **Создать** и задайте следующие параметры:

  * Введите имя для новой **рабочей области OMS**, например *DefaultLAWorkspace*. 
  * Выберите в раскрывающемся списке **подписку**, с которой нужно связать рабочую область, если выбранная по умолчанию не подходит.
  * Для **группы ресурсов** выберите существующую и уже настроенную группу ресурсов или создайте новую.  
  * Выберите доступное **расположение**.  Дополнительные сведения о доступности службы Log Analytics в регионах см. в [этой статье](https://azure.microsoft.com/regions/services/).
  * При создании рабочей области в новой подписке, созданной после 2 апреля 2018 г., будет автоматически использоваться тарифный план *За ГБ*, и выбор ценовой категории будет недоступен.  При создании рабочей области в существующей подписке, созданной до 2 апреля, или в подписке, которая была привязана к существующей регистрации EA, выберите нужную ценовую категорию.  Дополнительные сведения о конкретной ценовой категории см. в статье [Цены на Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-02.png)<br>  

3. После ввода необходимых сведений в области **Рабочая область OMS** щелкните **Создать**.  

Пока проверяются данные, ход создания рабочей области можно проверить в разделе **Уведомления** в меню. 

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда рабочая область доступна, вы можете настроить сбор данных телеметрии для мониторинга, выполнять поиск по журналам для анализа этих данных, а также добавить решение по управлению для предоставления дополнительных данных и аналитических сведений. 

* Сведения о том, как включить сбор данных из ресурсов Azure с помощью системы диагностики Azure или службы хранилища Azure, см. в статье [Сбор журналов и метрик для служб Azure для использования в Log Analytics](log-analytics-azure-storage.md).  
* [Добавьте System Center Operations Manager в качестве источника данных](log-analytics-om-agents.md) для сбора данных из агентов, которые предоставляют отчеты группе управления Operations Manager, и хранения этих данных в репозитории рабочей области Log Analytics. 
* Подключите [Configuration Manager](log-analytics-sccm.md) для импорта данных с компьютеров, которые являются элементами коллекций в иерархии.  
* Просмотрите список доступных [решений по управлению](/log-analytics-add-solutions.md) и узнайте, как добавить решение в рабочую область или удалить его из нее.

