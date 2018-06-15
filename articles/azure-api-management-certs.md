---
title: Передача сертификата управления службами Azure | Документация Майкрософт
description: Узнайте, как передать сертификат управления службами на портал Azure.
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: adegeo
ms.openlocfilehash: be548a8e823d4c7109951183886764738a66ecea
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32152017"
---
# <a name="upload-an-azure-service-management-certificate"></a>Передача сертификата управления службами Azure
Сертификаты управления позволяют выполнять аутентификацию с помощью классической модели развертывания Azure. Многие программы и инструменты (например, Visual Studio или пакет SDK Azure) будут использовать эти сертификаты для автоматизации настройки и развертывания разных служб Azure. 

> [!WARNING]
> Будьте осторожны! Эти типы сертификатов позволяют каждому, кто прошел проверку подлинности, управлять подпиской, с которой они связаны.
>
>

Дополнительные сведения о сертификатах Azure (включая сведения о создании самозаверяющего сертификата) см. в разделе [Что такое сертификаты управления](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Для автоматизации проверки подлинности клиентского кода можно также использовать службу [Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

**Примечание.** Вы должны быть соадминистратором подписки для выполнения любых операций с сертификатами управления. [Узнайте](https://go.microsoft.com/fwlink/?linkid=849300), как добавить или удалить соадминистратора на новом портале Azure. 

## <a name="upload-a-management-certificate"></a>Отправка сертификата управления
Создав сертификат управления (CER-файл только с открытым ключом), передайте его на портал. Когда сертификат доступен на портале, любой пользователь с соответствующим сертификатом (закрытым ключом) сможет подключаться через API управления и работать с ресурсами связанной подписки.

1. Войдите на [портал Azure](http://portal.azure.com).
2. Щелкните **Все службы** внизу списка служб Azure и выберите **Подписки** в группе служб _Общие_.

    ![Меню "Подписка"](./media/azure-api-management-certs/subscriptions_menu.png)

3. Обязательно выберите именно ту подписку, с которой необходимо связать сертификат.     
4. Выбрав правильную подписку, щелкните **Сертификаты управления** в группе _Параметры_.

    ![Параметры](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Нажмите кнопку **Отправить** .

    ![Кнопка "Отправить" на странице сертификатов](./media/azure-api-management-certs/certificates_page.png)
6. Укажите сведения в диалоговом окне и нажмите кнопку **Отправить**.

    ![Параметры](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Дополнительная информация
Связав сертификат управления с подпиской и установив соответствующий сертификат локально, вы можете программно подключаться к [REST API классической модели управления](https://msdn.microsoft.com/library/azure/mt420159.aspx) и автоматизировать различные ресурсы Azure, связанные с этой же подпиской.
