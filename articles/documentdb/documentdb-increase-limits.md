---
title: "Запрос увеличения квот учетной записи Azure Cosmos DB | Документация Майкрософт"
description: "Сведения о запросе изменения квот базы данных Azure Cosmos DB, например объема хранилища документов или пропускной способности для коллекции."
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 68f7dc8d-534f-4301-a42c-bcd1bb1b77fe
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2016
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 9e92efb269f88371109c003ceed50e8e7286d36c
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="request-increased-azure-cosmos-db-account-quotas"></a>Запрос увеличения квот учетной записи Azure Cosmos DB
[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) — это масштабная база данных, пропускную способность и хранилище которой можно масштабировать в соответствии с требованиями приложения. Если у вас возникнут вопросы по масштабированию Azure Cosmos DB, отправьте электронное сообщение по адресу askdocdb@microsoft.com.

В этой статье показано, как запросить увеличение квоты в службе поддержки Azure

## <a id="RequestQuotaIncrease"></a> Запрос изменения квоты
Ниже показано, как запросить изменение квоты.

1. На [портале Azure](https://portal.azure.com) нажмите кнопку **Больше служб**, а затем щелкните **Справка и поддержка**.
   
    ![Снимок экрана запуска справки и поддержки](media/documentdb-increase-limits/helpsupport.png)
2. В колонке **Справка и поддержка** щелкните **Новый запрос на поддержку**.
   
    ![Снимок экрана создания запроса в службу поддержки](media/documentdb-increase-limits/getsupport.png)
3. В колонке **Новый запрос на поддержку** щелкните **Основные**. Затем задайте для параметра **Тип проблемы** значение **Квота**, для параметра **Подписка** укажите свою подписку, которая содержит учетную запись Azure Cosmos DB. Задайте для параметра **Тип квоты** значение **Azure Cosmos DB**, а для параметра **План поддержки** — значение **Quota SUPPORT - Included** (Поддержка квоты включена). Затем щелкните **Далее**.
   
    ![Снимок экрана: тип запроса поддержки](media/documentdb-increase-limits/supportrequest1.png)
4. В колонке **Проблема** выберите степень серьезности и укажите сведения об увеличении квоты в поле **Сведения**. Щелкните **Далее**.
   
    ![Снимок экрана: средство выбора подписки запроса в службу поддержки](media/documentdb-increase-limits/supportrequest2.png)
5. Наконец, заполните колонку **Контактные данные** и нажмите кнопку **Создать**.

После создания запроса в службу поддержки вы получите номер запроса по электронной почте.  Чтобы просмотреть запрос, щелкните **Управление запросами на поддержку** в колонке **Справка и поддержка**.

![Снимок экрана: колонка запросов поддержки](media/documentdb-increase-limits/supportrequest4.png)

## <a name="NextSteps"></a> Дальнейшие действия
* Дополнительные сведения о базе данных Azure Cosmos DB см. [здесь](http://azure.com/docdb).


