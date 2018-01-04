---
title: "Отправка сертификата API управления в Azure | Документация Майкрософт"
description: "Узнайте, как можно передать сертификат API управления для портала Azure."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: adegeo
ms.openlocfilehash: ad55d71a56657e9cf33c1d33e09c58295206a2ae
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="upload-an-azure-management-api-management-certificate"></a>Отправка сертификата управления Azure для API управления
Сертификаты управления позволяют выполнять аутентификацию с помощью классической модели развертывания Azure. Многие программы и инструменты (например, Visual Studio или пакет SDK Azure) будут использовать эти сертификаты для автоматизации настройки и развертывания разных служб Azure. 

> [!WARNING]
> Будьте осторожны! Эти типы сертификатов позволяют каждому, кто прошел проверку подлинности, управлять подпиской, с которой они связаны.
>
>

Дополнительные сведения о сертификатах Azure (включая сведения о создании самозаверяющего сертификата) см. в разделе [Что такое сертификаты управления](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Для автоматизации проверки подлинности клиентского кода можно также использовать службу [Azure Active Directory](https://azure.microsoft.com/en-us/services/active-directory/).

**Примечание.** Вы должны быть соадминистратором подписки для выполнения любых операций с сертификатами управления. [Узнайте](https://go.microsoft.com/fwlink/?linkid=849300), как добавить или удалить соадминистратора на новом портале Azure. 

## <a name="upload-a-management-certificate"></a>Отправка сертификата управления
Создав сертификат управления (CER-файл только с открытым ключом), передайте его на портал. Когда сертификат доступен на портале, любой пользователь с соответствующим сертификатом (закрытым ключом) сможет подключаться через API управления и работать с ресурсами связанной подписки.

1. Войдите на [портал Azure](http://portal.azure.com).
2. Щелкните **Больше служб** внизу списка служб Azure и выберите **Подписки** в группе служб _Общие_.

    ![Меню "Подписка"](./media/azure-api-management-certs/subscriptions_menu.png)

3. Обязательно выберите именно ту подписку, с которой необходимо связать сертификат.     
4. Выбрав правильную подписку, щелкните **Сертификаты управления** в группе _Параметры_.

    ![Параметры](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Нажмите кнопку **Отправить** .

    ![Кнопка "Отправить" на странице сертификатов](./media/azure-api-management-certs/certificates_page.png)
6. Укажите сведения в диалоговом окне и нажмите кнопку **Отправить**.

    ![Параметры](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Дальнейшие действия
Связав сертификат управления с подпиской и установив соответствующий сертификат локально, вы можете программно подключаться к [REST API классической модели управления](https://msdn.microsoft.com/library/azure/mt420159.aspx) и автоматизировать различные ресурсы Azure, связанные с этой же подпиской.
