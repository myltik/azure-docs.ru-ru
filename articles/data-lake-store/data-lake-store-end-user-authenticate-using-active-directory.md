---
title: "Аутентификация пользователей в Data Lake Store с помощью Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как реализовать аутентификацию пользователей в Data Lake Store с помощью Azure Active Directory."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ec586ecd-1b42-459e-b600-fadbb7b80a9b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: f10bc67e4ee814d5aa0accff1a3dc1426b818084
ms.contentlocale: ru-ru
ms.lasthandoff: 08/29/2017

---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Аутентификация пользователей в Data Lake Store с помощью Azure Active Directory
> [!div class="op_single_selector"]
> * [Аутентификация между службами](data-lake-store-authenticate-using-active-directory.md)
> * [Аутентификация пользователей](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store использует Azure Active Directory для аутентификации. Прежде чем создать приложение, которое работает с Azure Data Lake Store или Azure Data Lake Analytics, необходимо решить, как его следует аутентифицировать посредством Azure Active Directory (Azure AD). Доступно два основных варианта:

* Аутентификация пользователей (в этой статье)
* Взаимодействие между службами

Оба этих варианта позволят приложению получить маркер OAuth 2.0, который вкладывается в каждый запрос к Azure Data Lake Store или Azure Data Lake Analytics.

Из этой статьи вы узнаете, как создать **собственное приложение Azure AD для аутентификации пользователей**. Инструкции по настройке приложения Azure AD для аутентификации между службами см. в статье [Аутентификация между службами в Data Lake Store с помощью Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Предварительные требования
* Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

* Идентификатор подписки. Его можно получить на портале Azure. Например, он доступен в колонке учетной записи Data Lake Store.
  
    ![Получение идентификатора подписки](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Доменное имя Azure AD. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. На приведенном ниже снимке экрана указано имя домена **contoso.onmicrosoft.com**, а идентификатор GUID в скобках — это идентификатор клиента. 
  
    ![Получение домена AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>Аутентификация пользователей
Мы советуем применить этот подход, если вам нужно, чтобы пользователи входили в приложение с помощью Azure AD. Приложение будет иметь тот же уровень доступа к ресурсам Azure, что и вошедший пользователь. Пользователю необходимо периодически вводить свои учетные данные, чтобы у вашего приложения оставался доступ.

В результате входа пользователя приложение получает маркер доступа и маркер обновления. Маркер доступа вкладывается в каждый запрос к Data Lake Store и Data Lake Analytics и по умолчанию действителен в течение одного часа. Маркер обновления может использоваться для получения нового маркера доступа. По умолчанию он действителен до двух недель. Для входа пользователей можно использовать два разных подхода.

### <a name="using-the-oauth-20-pop-up"></a>Использование всплывающего окна OAuth 2.0
Приложение может активировать всплывающее окно авторизации OAuth 2.0, в котором пользователь может ввести свои учетные данные. При необходимости это всплывающее окно также работает с процессом двухфакторной проверки подлинности (2FA) Azure AD. 

> [!NOTE]
> Этот метод еще не поддерживается в библиотеке аутентификации Azure AD (ADAL) для Java или Python.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Непосредственная передача учетных данных пользователя
Приложение могут напрямую предоставлять Azure AD учетные данные пользователя. Этот метод работает только для учетных записей на основе идентификаторов организации. Он не подходит для личных учетных записей пользователей или учетных записей на основе Live ID, включая те, которые заканчиваются на @outlook.com или @live.com. Кроме того, данный метод несовместим с учетными записями пользователей, для которых требуется двухфакторная проверка подлинности (2FA) Azure AD.

### <a name="what-do-i-need-to-use-this-approach"></a>Что мне нужно для применения этого подхода?
* Доменное имя Azure AD (см. предварительные требования в этой статье).
* **Собственное приложение** Azure AD.
* Идентификатор приложения для собственного приложения Azure AD.
* URI перенаправления для собственного приложения Azure AD.
* Настроенные делегированные разрешения.


## <a name="step-1-create-an-active-directory-native-application"></a>Шаг 1. Создание собственного приложения Active Directory

Создайте и настройте собственное приложение Azure AD для аутентификации пользователей в Azure Data Lake Store с помощью Azure Active Directory. Инструкции см. в разделе [Создание приложения Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

При выполнении инструкций по ссылке обязательно выберите тип приложения **Native** (Собственный), как показано на снимке экрана ниже.

![Создание веб-приложения](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Создание собственного приложения")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Шаг 2. Получение идентификатора приложения и URI перенаправления

В разделе [Получение идентификатора приложения и ключа проверки подлинности](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) описывается, как получить идентификатор приложения (также называемый идентификатором клиента на классическом портале Azure) для собственного приложения Azure AD.

Чтобы получить универсальный код ресурса (URI) перенаправления, выполните следующие действия.

1. На портале Azure выберите **Azure Active Directory**, щелкните **Регистрация приложений**, затем найдите и щелкните созданное собственное приложение Azure AD.

2. В колонке **Параметры** приложения щелкните **URI перенаправления**.

    ![Получение URI перенаправления](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Скопируйте отображенное значение.


## <a name="step-3-set-permissions"></a>Шаг 3. Установка разрешений

1. На портале Azure выберите **Azure Active Directory**, щелкните **Регистрация приложений**, затем найдите и щелкните созданное собственное приложение Azure AD.

2. В колонке **Параметры** приложения щелкните **Необходимые разрешения** и нажмите кнопку **Добавить**.

    ![Идентификатор клиента](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. В колонке **Добавить доступ через API** щелкните **Выбор API** > **Azure Data Lake** > **Выбрать**.

    ![Идентификатор клиента](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  В колонке **Добавить доступ через API** щелкните **Выбор разрешений**, установите флажок, чтобы предоставить **полный доступ к Data Lake Store**, и нажмите кнопку **Выбрать**.

    ![Идентификатор клиента](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Нажмите кнопку **Done**(Готово).

5. Повторите последние два шага, чтобы также предоставить разрешения для **API управления службами Microsoft Azure**.
   
## <a name="next-steps"></a>Дальнейшие действия
В этой статье мы создали собственное приложение Azure AD и собрали сведения, необходимые в клиентских приложениях, создаваемых с помощью пакетов SDK для .NET, Java, REST API и др. Из следующих статей вы узнаете, как с помощью веб-приложения Azure AD проходить аутентификацию в Data Lake Store, а затем выполнить другие операции в хранилище.

* [Начало работы с хранилищем озера данных Azure с помощью пакета SDK .NET](data-lake-store-get-started-net-sdk.md)
* [Начало работы с хранилищем озера данных Azure с помощью пакета SDK .NET](data-lake-store-get-started-java-sdk.md)
* [Начало работы с Azure Data Lake Store с помощью REST API](data-lake-store-get-started-rest-api.md)


