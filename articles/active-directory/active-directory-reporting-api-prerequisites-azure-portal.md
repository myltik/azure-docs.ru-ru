---
title: "Предварительные требования для доступа к API отчетов Azure AD | Документация Майкрософт"
description: "Узнайте о предварительных требованиях для доступа к API отчетов Azure AD"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2017
ms.author: dhanyahk;markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 8f8510b38d795a90850ef58084a04718cb5ac324
ms.contentlocale: ru-ru
ms.lasthandoff: 05/17/2017


---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Предварительные требования для доступа к API отчетов Azure AD

[Интерфейсы API отчетов Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) предоставляют программный доступ к данным с помощью набора интерфейсов API на базе REST. Эти интерфейсы API можно вызвать, используя различные языки и инструменты программирования.

API отчетов использует [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) для авторизации доступа к веб-API. 

Чтобы получить доступ к данным отчетов через API, необходима одна из следующих ролей:

- Чтение данных безопасности
- администратор безопасности;
- глобальный администратор.


Чтобы подготовить доступ к API отчетов, сделайте следующее:

1. Регистрация приложения 
2. предоставьте разрешения; 
3. соберите параметры конфигурации. 

Чтобы задать вопросы, обговорить проблемы или предоставить отзыв, обратитесь в [службу поддержки по инструментам создания отчетов AAD](mailto:aadreportinghelp@microsoft.com).

## <a name="register-an-azure-active-directory-application"></a>Регистрация приложения Azure Active Directory

Приложение необходимо зарегистрировать, даже если вы обращаетесь к API отчетов с помощью сценария. Это позволит получить **идентификатор приложения**, необходимый для вызова авторизации, и ваш код сможет получать маркеры.

Чтобы настроить для каталога доступ к API отчетов Azure AD, необходимо войти на портал Azure с учетной записью администратора Azure, которой также назначена роль участника каталога **глобального администратора** в клиенте Azure AD.

> [!IMPORTANT]
> В приложениях, работающих под учетными данными с такими административными правами, доступно много возможностей. Поэтому храните в надежном месте идентификатор приложения и секретные учетные данные.
> 


**Вот как можно зарегистрировать приложение Azure Active Directory.**

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Active Directory**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. В колонке **Azure Active Directory** щелкните **Регистрация приложений**.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. В колонке **Регистрация приложений** на панели инструментов вверху щелкните **Регистрация нового приложения**.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. В колонке **Создание** выполните следующее.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    а. В текстовом поле **Имя** введите `Reporting API application`.

    b. Для параметра **Тип приложения** выберите `Web app / API`.

    c. В текстовом поле **URL-адрес входа** введите `https://localhost`.

    г) Щелкните **Создать**. 


## <a name="grant-permissions"></a>Предоставление разрешений 

Цель этого этапа — предоставить приложению разрешения **Чтение данных каталога** для API **Windows Azure Active Directory**.

![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/16.png)
 

**Вот как можно предоставить приложению разрешения на использование API.**

1. В колонке **Регистрация приложений** в списке приложений щелкните **Reporting API application** (Приложение API отчетов).

2. В колонке **Reporting API application** (Приложение API отчетов) на панели инструментов вверху щелкните **Параметры**. 

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. В колонке **Параметры** щелкните **Необходимые разрешения**. 

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. В колонке **Необходимые разрешения** в списке **API** щелкните **Windows Azure Active Directory**. 

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. В колонке **Включить доступ** выберите **Чтение данных каталога**. 

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. На панели инструментов вверху щелкните **Сохранить**.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

## <a name="gather-configuration-settings"></a>Сбор параметров конфигурации 
В этом разделе показано, как получить из каталога следующие параметры:

* Доменное имя
* Идентификатор клиента
* Секрет клиента

Эти значения необходимы при настройке вызовов API отчетов. 

### <a name="get-your-domain-name"></a>Получение имени домена

**Вот как можно получить доменное имя.**

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Active Directory**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. В колонке **Azure Active Directory** щелкните **Доменные имена**.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Скопируйте нужное доменное имя из списка доменов.


### <a name="get-your-applications-client-id"></a>Получение идентификатора клиента приложения

**Вот как можно получить идентификатор клиента приложения.**

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Active Directory**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. В колонке **Регистрация приложений** в списке приложений щелкните **Reporting API application** (Приложение API отчетов).

3. В колонке **Reporting API application** (Приложение API отчетов) рядом с полем **Идентификатор приложения** щелкните **Щелкните, чтобы скопировать**.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Получение секрета клиента приложения
Чтобы получить секретный ключ клиента приложения, необходимо создать ключ и сохранить его значение при сохранении нового ключа, так как это значение невозможно получить позже.

**Вот как можно получить секрет клиента приложения.**

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Active Directory**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. В колонке **Регистрация приложений** в списке приложений щелкните **Reporting API application** (Приложение API отчетов).


3. В колонке **Reporting API application** (Приложение API отчетов) на панели инструментов вверху щелкните **Параметры**. 

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. В колонке **Параметры** в разделе **APIR Access** (Доступ APIR) щелкните **Ключи**. 

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. В колонке **Ключи** выполните следующие действия.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    а. В текстовом поле **Описание** введите `Reporting API`.

    b. Для параметра **Срок действия истекает** выберите значение **Через 2 года**.

    c. Щелкните **Сохранить**.

    г) Скопируйте значение ключа.


## <a name="next-steps"></a>Дальнейшие действия
* Хотите получать доступ к данным из API отчетов Azure AD программным образом? См. статью [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Дополнительные сведения об отчетах Azure Active Directory см. в статье [Руководство по отчетам Azure Active Directory](active-directory-reporting-guide.md).  


