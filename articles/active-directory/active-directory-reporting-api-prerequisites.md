---
title: "Предварительные требования для доступа к API отчетов Azure AD. | Документация Майкрософт"
description: "Узнайте о предварительных требованиях для доступа к API отчетов Azure AD"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b0f23cc578fb946247d492b9dee92cf7a9daafa1


---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Предварительные требования для доступа к API отчетов Azure AD
[Интерфейсы API отчетов Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) предоставляют программный доступ к данным с помощью набора интерфейсов API на базе REST. Эти интерфейсы API можно вызвать, используя различные языки и инструменты программирования.

API отчетов использует [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) для авторизации доступа к веб-API. 

Чтобы подготовить доступ к API отчетов, сделайте следующее:

1. Создайте приложение в клиенте Azure AD. 
2. Предоставьте ему соответствующие разрешения для доступа к данным Azure AD.
3. Получите параметры конфигурации из каталога.

Чтобы задать вопросы, обговорить проблемы или предоставить отзыв, обратитесь в [службу поддержки по инструментам создания отчетов AAD](mailto:aadreportinghelp@microsoft.com).

## <a name="create-an-azure-ad-application"></a>Создание приложения Azure AD
Чтобы настроить для каталога доступ к API отчетов Azure AD, необходимо войти на классический портал Azure с помощью учетной записи администратора подписки Azure, которой также назначена роль участника каталога глобального администратора в клиенте Azure AD.

> [!IMPORTANT]
> В приложениях, работающих под учетными данными с такими административными правами, доступно много возможностей. Поэтому храните в надежном месте идентификатор приложения и секретные учетные данные.
> 
> 

1. На [классическом портале Azure](https://manage.windowsazure.com)в области навигации слева щелкните **Active Directory**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Выберите свой каталог в списке каталогов **Active Directory** .
3. В меню вверху щелкните **Приложения**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/02.png) 
4. На нижней панели щелкните **Добавить**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/03.png) 
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение, разрабатываемое моей организацией**. 
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/04.png) 
6. В диалоговом окне **Расскажите о своем приложении** выполните следующие действия. 
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/05.png) 
   
    а. В текстовом поле **Имя** введите имя (например, приложение API отчетов).
   
    b. Выберите **Веб-приложение и/или веб-API**.
   
    c. Нажмите кнопку **Далее**.
7. В диалоговом окне **Свойства приложения** выполните следующие действия: 
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/06.png) 
   
    а. В текстовом поле **URL-адрес входа** введите `https://localhost`.
   
    b. В текстовом поле **URI кода приложения** введите ```https://localhost/ReportingApiApp```.
   
    c. Нажмите **Завершено**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Предоставление приложению разрешения на использование API
1. На [классическом портале Azure](https://manage.windowsazure.com/)в области навигации слева щелкните **Active Directory**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Выберите свой каталог в списке каталогов **Active Directory** .
3. В меню вверху щелкните **Приложения**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/02.png)
4. В списке приложений выберите созданное приложение.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/07.png)
5. В верхнем меню щелкните **Настроить**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/08.png)
6. В разделе **Разрешения для других приложений** для ресурса **Azure Active Directory** щелкните раскрывающийся список **Разрешения приложения** и выберите пункт **Прочитать данные каталога**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/09.png)
7. На нижней панели щелкните **Сохранить**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/10.png)

## <a name="gather-configuration-settings-from-your-directory"></a>Получите параметры конфигурации из каталога.
В этом разделе показано, как получить из каталога следующие параметры:

* Доменное имя
* Идентификатор клиента
* Секрет клиента

Эти значения необходимы при настройке вызовов API отчетов. 

### <a name="get-your-domain-name"></a>Получение имени домена
1. На [классическом портале Azure](https://manage.windowsazure.com)в области навигации слева щелкните **Active Directory**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Выберите свой каталог в списке каталогов **Active Directory** .
3. В меню вверху щелкните **Домены**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/11.png) 
4. Скопируйте имя домена в столбце **Доменное имя** .
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/12.png) 

### <a name="get-the-applications-client-id"></a>Получение идентификатора клиента приложения
1. На [классическом портале Azure](https://manage.windowsazure.com)в области навигации слева щелкните **Active Directory**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Выберите свой каталог в списке каталогов **Active Directory** .
3. В меню вверху щелкните **Приложения**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/02.png) 
4. В списке приложений выберите созданное приложение.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/07.png)
5. В верхнем меню щелкните **Настроить**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/08.png)
6. Скопируйте значение **идентификатор клиента**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/13.png)

### <a name="get-the-applications-client-secret"></a>Получение секрета клиента приложения
Чтобы получить секретный ключ клиента приложения, необходимо создать ключ и сохранить его значение при сохранении нового ключа, так как это значение невозможно получить позже.

1. На [классическом портале Azure](https://manage.windowsazure.com)в области навигации слева щелкните **Active Directory**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Выберите свой каталог в списке каталогов **Active Directory** .
3. В меню вверху щелкните **Приложения**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/02.png) 
4. В списке приложений выберите созданное приложение.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/07.png)
5. В верхнем меню щелкните **Настроить**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/08.png)
6. В разделе **Ключи** сделайте следующее: 
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/14.png)
   
    а. В списке значений длительности выберите значение длительности.
   
    b. На нижней панели щелкните **Сохранить**.
   
    ![Регистрация приложения](./media/active-directory-reporting-api-prerequisites/10.png)
   
    c. Скопируйте значение ключа.

## <a name="next-steps"></a>Дальнейшие действия
* Хотите получать доступ к данным из API отчетов Azure AD программным образом? См. статью [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Дополнительные сведения об отчетах Azure Active Directory см. в статье [Руководство по отчетам Azure Active Directory](active-directory-reporting-guide.md).  




<!--HONumber=Dec16_HO4-->


