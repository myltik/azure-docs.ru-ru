---
title: "Запрос увеличения квот учетной записи DocumentDB | Документация Майкрософт"
description: "Узнайте, как запросить изменение квот базы данных DocumentDB, например объема хранилища документов или пропускной способности для коллекции."
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 68f7dc8d-534f-4301-a42c-bcd1bb1b77fe
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: d5af6e3d9ba35558e0d7850820f4b9a479a259be
ms.openlocfilehash: 7c73de46500ac565680f2d16da05b4821507cd93


---
# <a name="request-increased-documentdb-account-quotas"></a>Запрос увеличения квот учетной записи DocumentDB
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) применяет ряд квот по умолчанию, которые могут быть изменены при обращении в службу поддержки Azure.  В этой статье показано, как запросить увеличение квоты.

Ознакомившись с данной статьей, вы сможете ответить на следующие вопросы.  

* Какие квоты базы данных DocumentDB могут быть изменены при обращении в службу поддержки Azure?
* Как запросить изменение квоты учетной записи DocumentDB?

## <a name="a-idquotasa-documentdb-account-quotas"></a><a id="Quotas"></a> Квоты учетной записи DocumentDB
В следующей таблице описаны квоты DocumentDB. Квоты со звездочкой (*) могут быть изменены при обращении в службу поддержки Azure.

[!INCLUDE [azure-documentdb-limits](../../includes/azure-documentdb-limits.md)]

## <a name="a-idrequestquotaincreasea-request-a-quota-adjustment"></a><a id="RequestQuotaIncrease"></a> Запрос изменения квоты
Ниже показано, как запросить изменение квоты.

1. На [портале Azure](https://portal.azure.com) нажмите кнопку **Больше служб**, а затем щелкните **Справка и поддержка**.
   
    ![Снимок экрана запуска справки и поддержки](media/documentdb-increase-limits/helpsupport.png)
2. В колонке **Справка и поддержка** щелкните **Новый запрос на поддержку**.
   
    ![Снимок экрана создания запроса в службу поддержки](media/documentdb-increase-limits/getsupport.png)
3. В колонке **Новый запрос на поддержку** щелкните **Основные**. Затем задайте для параметра **Тип проблемы** значение **Квота**, для параметра **Подписка** укажите свою подписку, которая содержит учетную запись DocumentDB. Задайте для параметра **Тип квоты** значение **DocumentDB**, а для параметра **План поддержки** — значение **Quota SUPPORT - Included** (Поддержка квоты включена). Затем щелкните **Далее**.
   
    ![Снимок экрана: тип запроса поддержки](media/documentdb-increase-limits/supportrequest1.png)
4. В колонке **Проблема** выберите степень серьезности и укажите сведения об увеличении квоты в поле **Сведения**. Нажмите кнопку **Далее**.
   
    ![Снимок экрана: средство выбора подписки запроса в службу поддержки](media/documentdb-increase-limits/supportrequest2.png)
5. Наконец, заполните колонку **Контактные данные** и нажмите кнопку **Создать**.

После создания запроса в службу поддержки вы получите номер запроса по электронной почте.  Чтобы просмотреть запрос, щелкните **Управление запросами на поддержку** в колонке **Справка и поддержка**.

![Снимок экрана: колонка запросов поддержки](media/documentdb-increase-limits/supportrequest4.png)

## <a name="a-namenextstepsa-next-steps"></a><a name="NextSteps"></a> Дальнейшие действия
* Для получения дополнительных сведений о DocumentDB щелкните [здесь](http://azure.com/docdb).




<!--HONumber=Nov16_HO3-->


