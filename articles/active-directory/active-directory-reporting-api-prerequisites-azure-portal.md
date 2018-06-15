---
title: Предварительные требования для доступа к API отчетов Azure Active Directory | Документация Майкрософт
description: Узнайте о предварительных требованиях для доступа к API отчетов Azure AD
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: a38447150dd283ff21a852690cfd48477472f7b8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588766"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Предварительные требования для доступа к API отчетов Azure Active Directory

[API-интерфейсы отчетов Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) предоставляют программный доступ к данным с помощью набора API-интерфейсов на базе REST. Эти интерфейсы API можно вызвать, используя различные языки и инструменты программирования.

API отчетов использует [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) для авторизации доступа к веб-API.

Чтобы подготовить доступ к API отчетов, сделайте следующее:

1. Назначение ролей
2. Регистрация приложения
3. предоставьте разрешения;
4. Сбор параметров конфигурации



## <a name="assign-roles"></a>Назначение ролей

Чтобы получить доступ к данным отчетов через API, необходима одна из следующих ролей:

- Чтение данных безопасности

- администратор безопасности;

- глобальный администратор.




## <a name="register-an-application"></a>Регистрация приложения

Приложение необходимо зарегистрировать, даже если вы обращаетесь к API отчетов с помощью сценария. Это позволит получить **идентификатор приложения**, необходимый для вызова авторизации, и ваш код сможет получать маркеры.

Чтобы настроить для каталога доступ к API отчетов Azure AD, необходимо войти на портал Azure с учетной записью администратора Azure, которой также назначена роль участника каталога **глобального администратора** в клиенте Azure AD.

> [!IMPORTANT]
> В приложениях, работающих под учетными данными с такими административными правами, доступно много возможностей. Поэтому храните в надежном месте идентификатор приложения и секретные учетные данные.
> 


**Вот как можно зарегистрировать приложение Azure Active Directory.**

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Azure Active Directory**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. На странице **Azure Active Directory** щелкните **Регистрация приложений**.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. На странице **Регистрация приложений** на панели инструментов вверху щелкните **Регистрация нового приложения**.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. На странице **Создание** выполните следующие действия.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. В текстовом поле **Имя** введите `Reporting API application`.

    Б. В качестве **типа приложения** выберите **Веб-приложение или API**.

    c. В текстовом поле **URL-адрес входа** введите `https://localhost`.

    d. Нажмите кнопку **Создать**. 


## <a name="grant-permissions"></a>Предоставление разрешений 

В зависимости от API, к которому вы хотите получить доступ, приложению необходимо предоставить следующие разрешения:  

| API | Разрешение |
| --- | --- |
| Microsoft Azure Active Directory | Прочитать данные каталога |
| Microsoft Graph | Прочитать все данные журнала аудита |


![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/36.png)


В следующем разделе перечислены шаги для получения доступа к обоим API. Если вам не нужен доступ к данным API, вы можете пропустить эти шаги.
 

**Вот как можно предоставить приложению разрешения на использование API.**

1. На странице **Регистрация приложений** в списке приложений щелкните **Reporting API application** (Приложение API отчетов).

2. На странице **Reporting API application** (Приложение API отчетов) на панели инструментов вверху щелкните **Параметры**. 

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. На странице **Параметры** щелкните **Необходимые разрешения**. 

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. На странице **Необходимые разрешения** в списке **API** щелкните **Windows Azure Active Directory**. 

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. На странице **Разрешение доступа** выберите параметр **Чтение данных каталога** и отмените выбор параметра **Sign in and read user profile** (Вход и чтение профиля пользователя). 

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. На панели инструментов вверху щелкните **Сохранить**.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. На странице **Необходимые разрешения** на панели инструментов вверху нажмите кнопку **Добавить**.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/32.png)

8. На странице **Добавить доступ через API** щелкните **Выбор API**.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/31.png)

9. На странице **Select an API** (Выбор API) щелкните **Microsoft Graph**, а затем нажмите кнопку **Выбрать**.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/33.png)

10. На странице **Разрешение доступа** выберите **Read all audit log data** (Прочитать все данные журнала аудита), а затем нажмите кнопку **Выбрать**.  

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/34.png)


11. На странице **Добавить доступ через API** щелкните **Готово**.  

12. На странице **Необходимые разрешения** на панели инструментов вверху щелкните **Предоставить разрешения** и выберите **Да**.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Сбор параметров конфигурации 

В этом разделе показано, как получить из каталога следующие параметры:

- Доменное имя
- Идентификатор клиента
- Секрет клиента

Эти значения необходимы при настройке вызовов API отчетов. 

### <a name="get-your-domain-name"></a>Получение имени домена

**Вот как можно получить доменное имя.**

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Azure Active Directory**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. На странице **Azure Active Directory** щелкните **Имена пользовательских доменов**.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Скопируйте нужное доменное имя из списка доменов.


### <a name="get-your-applications-client-id"></a>Получение идентификатора клиента приложения

**Вот как можно получить идентификатор клиента приложения.**

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Azure Active Directory**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. На странице **Регистрация приложений** в списке приложений щелкните **Reporting API application** (Приложение API отчетов).

3. На странице **Reporting API application** (Приложение API отчетов) рядом с полем **Идентификатор приложения** щелкните **Click to copy** (Щелкните, чтобы скопировать).

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Получение секрета клиента приложения
Чтобы получить секретный ключ клиента приложения, необходимо создать ключ и сохранить его значение при сохранении нового ключа, так как это значение невозможно получить позже.

**Вот как можно получить секрет клиента приложения.**

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Azure Active Directory**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. На странице **Регистрация приложений** в списке приложений щелкните **Reporting API application** (Приложение API отчетов).


3. На странице **Reporting API application** (Приложение API отчетов) на панели инструментов вверху щелкните **Параметры**. 

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. На странице **Параметры** в разделе **APIR Access** (Доступ APIR) щелкните **Ключи**. 

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. На странице **Ключи** выполните следующие действия.

    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. В текстовом поле **Описание** введите `Reporting API`.

    Б. Для параметра **Срок действия истекает** выберите значение **Через 2 года**.

    c. Выберите команду **Сохранить**.

    d. Скопируйте значение ключа.


## <a name="next-steps"></a>Дальнейшие действия

- [Получение данных с помощью API отчетов Azure Active Directory с сертификатами](active-directory-reporting-api-with-certificates.md)

- [Ознакомление с API отчетов](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Создание собственного решения](active-directory-reporting-api-getting-started-azure-portal.md#customize)

