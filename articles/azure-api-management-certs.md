---
title: "Отправка сертификата API управления в Azure | Документация Майкрософт"
description: "Узнайте, как отправить сертификат API управления в Microsoft Azure."
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
ms.date: 04/18/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: f7b0c3acea5bef6017230df82c57ab7a6ebccd80
ms.openlocfilehash: 19e5ef3d18deb4bf49699df6449d1cafab92ff53


---
# <a name="upload-an-azure-management-api-management-certificate"></a>Отправка сертификата управления Azure для API управления
Сертификаты управления позволяют выполнять проверку подлинности в API управления службами, которые предоставляются Azure. Многие программы и средства (такие как Visual Studio или Azure SDK) будут использовать эти сертификаты для автоматизации настройки и развертывания различных служб Azure. **Это касается только классического портала Azure**.

> [!WARNING]
> Будьте осторожны! Эти типы сертификатов позволяют каждому, кто прошел проверку подлинности, управлять подпиской, с которой они связаны.
>
>

Дополнительные сведения о сертификатах Azure, включая информацию о создании самозаверяющего сертификата, см. [здесь](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Для автоматизации проверки подлинности клиентского кода можно также использовать службу [Azure Active Directory](https://azure.microsoft.com/en-us/services/active-directory/).

## <a name="upload-a-management-certificate"></a>Отправка сертификата управления
Создав сертификат управления (CER-файл только с открытым ключом), передайте его на портал. Когда сертификат доступен на портале, любой пользователь с соответствующим сертификатом (закрытым ключом) сможет подключаться через API управления и работать с ресурсами связанной подписки.

1. Перейдите на [классический портал Azure](http://manage.windowsazure.com).
2. Обязательно выберите именно ту подписку, с которой необходимо связать сертификат. Щелкните надпись **Подписки** в правом верхнем углу страницы портала.

    ![Параметры](./media/azure-api-management-certs/subscription.png)
3. Выбрав нужную подписку, щелкните **Параметры** в левой части страницы портала (может потребоваться прокрутить страницу вниз).

    ![Параметры](./media/azure-api-management-certs/settings.png)
4. Щелкните вкладку **Сертификаты управления** .

    ![Параметры](./media/azure-api-management-certs/certificates-tab.png)
5. Нажмите кнопку **Отправить** .

    ![Параметры](./media/azure-api-management-certs/upload.png)
6. Укажите в диалоговом окне необходимые данные и нажмите кнопку с **галочкой**.

    ![Параметры](./media/azure-api-management-certs/upload-dialog.png)

## <a name="next-steps"></a>Дальнейшие действия
Связав сертификат управления с подпиской и установив соответствующий сертификат локально, вы можете программно подключаться к [REST API управления службами](https://msdn.microsoft.com/library/azure/mt420159.aspx) и автоматизировать различные ресурсы Azure, связанные с этой же подпиской.



<!--HONumber=Nov16_HO3-->


