---
title: "Публикация веб-службы машинного обучения в Azure Marketplace (устаревшая версия) | Документация Майкрософт"
description: "Сведения о публикации веб-службы машинного обучения Azure в Azure Marketplace (устаревшая версия)."
services: machine-learning
documentationcenter: 
author: BharathS
manager: jhubbard
editor: cgronlun
ms.assetid: 68e908be-3a99-4cd7-9517-e2b5f2f341b8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: bharaths
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 85a58e09e05fdb50984055ab25c4c2fe520dab4d
ms.openlocfilehash: 2d62966f130f6778c9561393cc7fc338f8903f1e


---
# <a name="deprecated-publish-azure-machine-learning-web-service-to-the-azure-marketplace"></a>Публикация веб-службы машинного обучения Azure в Azure Marketplace (устаревшая версия)

> [!NOTE]
> Работа DataMarket и служб данных прекращается. Начиная с 31 марта 2017 г. имеющиеся подписки выводятся из эксплуатации и будут отменены. Поэтому мы не рекомендуем использовать эту статью. 
> 
> В качестве альтернативы вы можете публиковать свои эксперименты машинного обучения в [коллекции Cortana Intelligence](https://gallery.cortanaintelligence.com/) и поделиться ими с сообществом, занимающимся анализом данных. Дополнительные сведения см. в статье [Поиск ресурсов в коллекции Cortana Intelligence и обмен ими](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-gallery-how-to-use-contribute-publish).

Azure Marketplace предоставляет возможность публикации веб-служб машинного обучения Azure как платных или бесплатных служб для внешних клиентов. В этой статье приводится обзор процесса публикации со ссылками на руководства для начала работы. С помощью этой процедуры можно разрешить другим разработчикам использовать веб-службы в их приложениях.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview-of-the-publishing-process"></a>Обзор процесса публикации
Чтобы опубликовать веб-службу Машинного обучения Azure в Azure Marketplace, сделайте следующее:

1. Создайте и опубликуйте службу машинного обучения типа «запрос-ответ» (RRS).
2. Разверните службу в рабочей среде и получите информацию о ключе API и конечной точке OData.
3. Используйте URL-адрес опубликованной веб-службы для публикации в [Azure Marketplace (Data Market)](https://publish.windowsazure.com/workspace/) 
4. После отправки это предложение рассматривается и должно быть утверждено, прежде чем клиенты смогут начать приобретать его. Процесс публикации может занять несколько рабочих дней. 

## <a name="walk-through"></a>Пошаговое руководство
### <a name="step-1-create-and-publish-a-machine-learning-request-response-service-rrs"></a>Шаг 1. Создание и публикация службы машинного обучения типа «запрос-ответ» (RRS)
 Если вы еще не создали такую службу, ознакомьтесь с этим [пошаговым руководством](machine-learning-walkthrough-5-publish-web-service.md).

### <a name="step-2-deploy-the-service-to-production-and-obtain-the-api-key-and-odata-endpoint-information"></a>Шаг 2. Развертывание службы в рабочей среде, а также получение информации о ключе API и конечной точке OData.
1. На [классическом портале Azure](http://manage.windowsazure.com)выберите элемент **МАШИННОЕ ОБУЧЕНИЕ** на панели навигации слева, а затем рабочую область. 
2. Щелкните вкладку **ВЕБ-СЛУЖБЫ** и выберите веб-службу, которую нужно опубликовать в Marketplace.
   
    ![Azure Marketplace][workspace]
3. Выберите конечную точку, которую необходимо использовать в Marketplace. Если вы не создали дополнительные конечные точки, можно выбрать конечную точку **По умолчанию** .
4. Щелкнув конечную точку, вы сможете увидеть **ключ API**. Скопируйте его. Эта информация потребуется далее на шаге 3.
   
    ![Azure Marketplace][apikey]
5. Щелкните метод **Запрос-ответ**. На этом этапе публикация служб пакетного выполнения в Marketplace не поддерживается. Вы перейдете на страницу справки API для метода «Запрос-ответ».
6. Скопируйте **адрес конечной точки OData**. Эти сведения потребуются далее на шаге 3.
   
    ![Azure Marketplace][odata]

Разверните службу в рабочей среде.

### <a name="step-3-use-the-url-of-the-published-web-service-to-publish-to-azure-marketplace-datamarket"></a>Шаг 3. Использование URL-адреса опубликованной веб-службы для публикации в Azure Marketplace (DataMarket)
1. Перейдите к [Azure Marketplace (Data Market)](http://datamarket.azure.com/home) 
2. Щелкните ссылку **Опубликовать** в верхней части страницы. Откроется страница [портала публикации Microsoft Azure](https://publish.windowsazure.com)
3. Щелкните раздел **Издатели** , чтобы зарегистрироваться как издатель.
4. При создании предложения выберите пункт **Службы данных**, а затем выберите команду **Create a New Data Service** (Создать службу данных). 
   
   ![Azure Marketplace][image1]
   
   <br />
5. На вкладке **Планы** укажите информацию о предложении, включая тарифный план. Определите, будет ли предложение платной или бесплатной службой. Для получения оплаты необходимо указать платежные реквизиты, такие как банковские и налоговые данные.
6. На вкладке **Маркетинг** укажите информацию о предложении, такую как заголовок и описание предложения.
7. На вкладке **Цены** можно установить цену для планов в конкретных странах или позволить системе автоматически назначить цену для вашего предложения.
8. На вкладке **Data Service** (Служба данных) выберите для параметра **Источник данных** значение **Веб-служба**.
   
    ![Azure Marketplace][image2]
9. Откройте информацию об URL-адресе и ключе API веб-службы, полученных на классическом портале Azure, как описано на шаге 2 выше.
10. В диалоговом окне настройки службы данных Marketplace в текстовом поле **URL-адрес службы** вставьте адрес конечной точки OData.
11. Для **проверки подлинности** выберите значение **Заголовок** для параметра **Authentication Scheme** (Схема проверки подлинности).
    
    * В поле **Имя заголовка**укажите Authorization.
    * В поле **Header Value** (Значение заголовка) введите Bearer (без кавычек), щелкните строку **Space** (Пространство), а затем вставьте ключ API.
    * Установите флажок **Это служба OData** .
    * Нажмите кнопку **Проверить подключение** , чтобы проверить подключение.
12. На вкладке **Категории** выберите **Машинное обучение**.
13. После ввода всех метаданных о вашем предложении нажмите кнопку **Опубликовать**, а затем щелкните **Push to Staging** (Переместить в промежуточную среду). На этом этапе вы получите уведомление об оставшихся нерешенных проблемах.
14. Если все проблемы решены, щелкните **Запросить утверждение для перемещения в рабочую среду**. Процесс публикации может занять несколько рабочих дней. 

[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png




<!--HONumber=Jan17_HO2-->


