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
ms.date: 01/10/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9019a4115e81a7d8f1960098b1138cd437a0460b
ms.openlocfilehash: a50fc687a1738a55c3d22eb3e12060397c162e06


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

Из этой статьи вы узнаете, как создать веб-приложение Azure AD для аутентификации пользователей. Инструкции по настройке приложения Azure AD для аутентификации между службами см. в статье [Аутентификация между службами в Data Lake Store с помощью Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Предварительные требования
* Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
* Идентификатор подписки. Его можно получить на портале Azure. Например, он доступен в колонке учетной записи Data Lake Store.
  
    ![Получение идентификатора подписки](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)
* Доменное имя Azure AD. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. На приведенном ниже снимке экрана указано имя домена **contoso.microsoft.com**, а идентификатор GUID в скобках — это идентификатор клиента. 
  
    ![Получение домена AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>Аутентификация пользователей
Этот подход рекомендуется, если вам нужно, чтобы пользователи входили в приложение с помощью Azure AD. Приложение будет иметь тот же уровень доступа к ресурсам Azure, что и вошедший пользователь. Пользователю понадобится периодически вводить свои учетные данные, чтобы у вашего приложения оставался доступ.

В результате входа пользователя приложение получает маркер доступа и маркер обновления. Маркер доступа вкладывается в каждый запрос к Data Lake Store и Data Lake Analytics и по умолчанию действителен в течение одного часа. Маркер обновления может использоваться для получения нового маркера доступа. По умолчанию он действителен до двух недель, при условии, что используется регулярно. Для входа пользователей можно использовать два разных подхода.

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
* **Веб-приложение** Azure AD.
* Идентификатор клиента для веб-приложения Azure AD.
* URI ответа для веб-приложения Azure AD.
* Настроенные делегированные разрешения.

Инструкции по созданию веб-приложения Azure AD и его настройке для выполнения перечисленных выше требований см. в разделе [Создание приложения Active Directory](#create-an-active-directory-application) ниже. 

## <a name="create-an-active-directory-application"></a>Создание приложения Active Directory
Из этого раздела вы узнаете, как создать и настроить веб-приложение Azure AD для аутентификации пользователей в Azure Data Lake Store с помощью Azure Active Directory.

### <a name="step-1-create-an-azure-active-directory-application"></a>Шаг 1. Создание приложения Azure Active Directory
> [!NOTE]
> Для описанных ниже действий используется портал Azure. Приложение Azure AD можно создать также с помощью [Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md) или [интерфейса командной строки Azure](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md).
> 
> 

1. Войдите в свою учетную запись Azure через [классический портал](https://manage.windowsazure.com/).
2. Выберите **Active Directory** на левой панели.
   
     ![выбор Active Directory](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory.png)
3. Выберите каталог Active Directory, который хотите использовать для создания нового приложения. Если у вас несколько каталогов Active Directory, то приложение рекомендуется создавать в каталоге вашей подписки. Доступ к ресурсам в подписке можно предоставить только тем приложением, которые находятся в том же каталоге, что и подписка.  
   
     ![выбор каталога](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory-details.png)
4. Чтобы просмотреть приложения в каталоге, щелкните **Приложения**.
   
     ![просмотр приложений](./media/data-lake-store-end-user-authenticate-using-active-directory/view-applications.png)
5. Если ранее вы не создавали приложений в этом каталоге, вы увидите примерно следующее: Щелкните **Добавить приложение**.
   
     ![Добавить приложение](./media/data-lake-store-end-user-authenticate-using-active-directory/create-application.png)
   
     Также можно щелкнуть **Добавить** на нижней панели.
   
     ![добавление](./media/data-lake-store-end-user-authenticate-using-active-directory/add-icon.png)
6. Укажите имя и тип приложения. Для работы с данным руководством выберите параметр **Веб-приложение и/или веб-API** , а затем нажмите кнопку "Далее".
   
     ![указание имени приложения](./media/data-lake-store-end-user-authenticate-using-active-directory/tell-us-about-your-application.png)
7. Задайте свойства приложения. В поле **URL-адрес входа**введите универсальный код ресурса (URI) веб-сайта, который описывает приложение. Существование этого сайта не проверяется. 
   В поле **URI ИДЕНТИФИКАТОРА ПРИЛОЖЕНИЯ**введите URI, который идентифицирует ваше приложение.
   
     ![свойства приложения](./media/data-lake-store-end-user-authenticate-using-active-directory/app-properties.png)
   
    Щелкните флажок, чтобы завершить работу мастера и создать приложение.

### <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>Шаг 2. Получение идентификатора клиента, URI ответа и настроенных делегированных разрешений
1. Откройте вкладку **Настройка**, чтобы задать пароль приложения.
   
     ![настройка приложения](./media/data-lake-store-end-user-authenticate-using-active-directory/application-configure.png)
2. Скопируйте значение в поле **Идентификатор клиента**.
   
     ![Идентификатор клиента](./media/data-lake-store-end-user-authenticate-using-active-directory/client-id.png)
3. В разделе **Единый вход** скопируйте **URI ответа**.
   
    ![Идентификатор клиента](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-get-reply-uri.png)
4. В разделе **Разрешения для других приложений** щелкните **Добавить приложение**
   
    ![Идентификатор клиента](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)
5. В разделе **Разрешения для других приложений** выберите **Azure Data Lake** и **API управления службами Microsoft** **Azure**, а затем щелкните флажок.
6. По умолчанию у новых добавленных служб параметр **Делегированные разрешения** имеет значение&0;. Щелкните раскрывающийся список **Делегированные разрешения** для Azure Data Lake и службы управления Microsoft Azure и задайте значение 1. Результат должен выглядеть следующим образом.
   
     ![Идентификатор клиента](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
7. Щелкните **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия
В этой статье мы создали веб-приложение Azure AD и собрали сведения, необходимые в клиентских приложениях, создаваемых с помощью пакетов SDK для .NET, Java и др. Из следующих статей вы узнаете, как с помощью веб-приложения Azure AD проходить аутентификацию в Data Lake Store, а затем выполнить другие операции в хранилище.

* [Начало работы с хранилищем озера данных Azure с помощью пакета SDK .NET](data-lake-store-get-started-net-sdk.md)
* [Начало работы с хранилищем озера данных Azure с помощью пакета SDK .NET](data-lake-store-get-started-java-sdk.md)




<!--HONumber=Jan17_HO4-->


