---
title: "Аутентификация в Data Lake Store с помощью Active Directory | Документация Майкрософт"
description: "Узнайте, как выполнять аутентификацию в Data Lake Store с помощью Active Directory."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 820b7c5d-4863-4225-9bd1-df4d8f515537
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 35cde786bbc091c58f4dcb341cd47ce4c4f4b46c
ms.openlocfilehash: 02e52c3aba82ab8e3a8b1dc921731c29e505e23e


---
# <a name="service-to-serivce-authentication-with-data-lake-store-using-azure-active-directory"></a>Аутентификация между службами в Data Lake Store с помощью Azure Active Directory
> [!div class="op_single_selector"]
> * [Аутентификация между службами](data-lake-store-authenticate-using-active-directory.md)
> * [Аутентификация пользователей](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store использует Azure Active Directory для аутентификации. Прежде чем создать приложение, которое работает с Azure Data Lake Store или Azure Data Lake Analytics, необходимо решить, как его следует аутентифицировать посредством Azure Active Directory (Azure AD). Доступно два основных варианта:

* аутентификация пользователей и 
* аутентификация между службами. 

Оба этих варианта позволят приложению получить маркер OAuth 2.0, который вкладывается в каждый запрос к Azure Data Lake Store или Azure Data Lake Analytics.

Из этой статьи вы узнаете, как создать веб-приложение Azure AD для аутентификации между службами. Инструкции по настройке приложения Azure AD для аутентификации пользователей см. в статье [Аутентификация пользователей в Data Lake Store с помощью Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Предварительные требования
* Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
* Идентификатор подписки. Его можно получить на портале Azure. Например, он доступен в колонке учетной записи Data Lake Store.
  
    ![Получение идентификатора подписки](./media/data-lake-store-authenticate-using-active-directory/get-subscription-id.png)
* Доменное имя Azure AD. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. На приведенном ниже снимке экрана указано имя домена **contoso.microsoft.com**, а идентификатор GUID в скобках — это идентификатор клиента. 
  
    ![Получение домена AAD](./media/data-lake-store-authenticate-using-active-directory/get-aad-domain.png)

## <a name="service-to-service-authentication"></a>Взаимодействие между службами
Этот подход рекомендуется, если нужно, чтобы приложение автоматически выполняло аутентификацию с помощью Azure AD, не требуя от пользователя ввода учетных данных. Приложение само сможет пройти аутентификацию, имея действительные учетные данные, которые можно настроить таким образом, чтобы использовать несколько лет.

### <a name="what-do-i-need-to-use-this-approach"></a>Что мне нужно для применения этого подхода?
* Доменное имя Azure AD (см. предварительные требования в этой статье).
* **Веб-приложение**Azure AD.
* Идентификатор клиента для веб-приложения Azure AD.
* Секрет клиента для веб-приложения Azure AD.
* Конечная точка маркера для веб-приложения Azure AD.
* Разрешите веб-приложению Azure AD доступ к файлу или папке Data Lake Store либо учетной записи Data Lake Analytics, с которой требуется работать.

Инструкции по созданию веб-приложения Azure AD и его настройке для выполнения перечисленных выше требований см. в разделе [Создание приложения Active Directory](#create-an-active-directory-application) ниже.

> [!NOTE]
> По умолчанию приложение Azure AD настроено на использование секрета клиента, который можно получить из приложения Azure AD. Однако, если вы хотите, чтобы приложение Azure AD использовало сертификат, то необходимо создать веб-приложение Azure AD с помощью Azure PowerShell, как описано в разделе [Create a service principal with certificate](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate) (Создание субъекта-службы с сертификатом).
> 
> 

## <a name="create-an-active-directory-application"></a>Создание приложения Active Directory
Из этого раздела вы узнаете, как создать и настроить веб-приложение Azure AD для аутентификации между службами в Azure Data Lake Store с помощью Azure Active Directory. 

### <a name="step-1-create-an-azure-active-directory-application"></a>Шаг 1. Создание приложения Azure Active Directory
> [!NOTE]
> Для описанных ниже действий используется портал Azure. Приложение Azure AD можно создать также с помощью [Azure PowerShell](../resource-group-authenticate-service-principal.md) или [интерфейса командной строки Azure](../resource-group-authenticate-service-principal-cli.md).
> 
> 

1. Войдите в свою учетную запись Azure через [классический портал](https://manage.windowsazure.com/).
2. Выберите **Active Directory** на левой панели.
   
     ![выбор Active Directory](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
3. Выберите каталог Active Directory, который хотите использовать для создания нового приложения. Если у вас несколько каталогов Active Directory, то приложение рекомендуется создавать в каталоге вашей подписки. Доступ к ресурсам в подписке можно предоставить только тем приложением, которые находятся в том же каталоге, что и подписка.  
   
     ![выбор каталога](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
4. Чтобы просмотреть приложения в каталоге, щелкните **Приложения**.
   
     ![просмотр приложений](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)
5. Если ранее вы не создавали приложений в этом каталоге, вы увидите примерно следующее: Щелкните **Добавить приложение**.
   
     ![Добавить приложение](./media/data-lake-store-authenticate-using-active-directory/create-application.png)
   
     Также можно щелкнуть **Добавить** на нижней панели.
   
     ![добавление](./media/data-lake-store-authenticate-using-active-directory/add-icon.png)
6. Укажите имя и тип приложения. Для работы с данным руководством выберите параметр **Веб-приложение и/или веб-API** , а затем нажмите кнопку "Далее".
   
     ![указание имени приложения](./media/data-lake-store-authenticate-using-active-directory/tell-us-about-your-application.png)

    > [!TIP]
    > Укажите имя приложения, которое будет проще найти. Далее в этом руководстве вы выполните поиск этого приложения и назначите его учетной записи Data Lake Store.
    > 
    > 

7. Задайте свойства приложения. В поле **URL-адрес входа**введите универсальный код ресурса (URI) веб-сайта, который описывает приложение. Существование этого сайта не проверяется. 
   В поле **URI ИДЕНТИФИКАТОРА ПРИЛОЖЕНИЯ**введите URI, который идентифицирует ваше приложение.
   
     ![свойства приложения](./media/data-lake-store-authenticate-using-active-directory/app-properties.png)
   
    Щелкните флажок, чтобы завершить работу мастера и создать приложение.

### <a name="step-2-get-client-id-client-secret-and-token-endpoint"></a>Шаг 2. Получение идентификатора клиента, секрета клиента и конечной точки маркера
При программном входе необходимо указывать идентификатор приложения. Если приложение выполняется под собственными учетными данными, потребуется также ключ проверки подлинности.

1. Откройте вкладку **Настройка**, чтобы задать пароль приложения.
   
     ![настройка приложения](./media/data-lake-store-authenticate-using-active-directory/application-configure.png)
2. Скопируйте значение в поле **Идентификатор клиента**.
   
     ![Идентификатор клиента](./media/data-lake-store-authenticate-using-active-directory/client-id.png)
3. Если приложение будет выполняться под собственными учетными данными, прокрутите окно до раздела **Ключи** и выберите срок действия пароля.
   
     ![ключи](./media/data-lake-store-authenticate-using-active-directory/create-key.png)
4. Нажмите **Сохранить** , чтобы сохранить созданный ключ.
   
    ![Сохранить](./media/data-lake-store-authenticate-using-active-directory/save-icon.png)
   
    Сохранный ключ появится на экране, и вы сможете его скопировать. Позже получить ключ будет невозможно, поэтому необходимо скопировать его сейчас.
   
    ![сохраненный ключ](./media/data-lake-store-authenticate-using-active-directory/save-key.png)
5. Чтобы получить конечную точку маркера, внизу экрана выберите **Просмотр конечных точек**. Необходимое значение для поля **Конечная точка маркера OAuth 2.0** показано ниже.  
   
    ![Идентификатор клиента](./media/data-lake-store-authenticate-using-active-directory/save-tenant.png)

### <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>Шаг 3. Назначение приложения Azure AD файлу или папке в учетной записи Azure Data Lake Store (только для аутентификации между службами)
1. Войдите на новый [портал Azure](https://portal.azure.com) и откройте учетную запись Azure Data Lake Store, которую необходимо связать с ранее созданным приложением Azure Active Directory.
2. В колонке учетной записи «Хранилище озера данных» щелкните **Обозреватель данных**.
   
    ![Создание каталогов в учетной записи хранения озера данных](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Create directories in Data Lake account")
3. В колонке **Обозреватель данных** щелкните файл (или папку), к которому необходимо предоставить доступ для приложения Azure AD, а затем нажмите кнопку **Доступ**. Чтобы настроить доступ к файлу, необходимо нажать кнопку **Доступ** в колонке **Предварительный просмотр файла**.
   
    ![Настройка списков управления доступом в файловой системе озера данных](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Set ACLs on Data Lake file system")
4. В колонке **Доступ** перечислены стандартные и пользовательские варианты доступа, уже назначенные корню. Щелкните значок **Добавить** , чтобы добавить ACL пользовательского уровня.
   
    ![Перечисление стандартных и пользовательских сценариев доступа](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "List standard and custom access")
5. Щелкните значок **Добавить**, чтобы открыть колонку **Добавить настраиваемый доступ**. В этой колонке щелкните **Выберите пользователя или группу**, а затем в колонке **Выберите пользователя или группу** найдите созданное ранее приложение Azure Active Directory. Если у вас много групп для поиска, воспользуйтесь текстовым полем в верхней части для фильтрации по имени группы. Выберите группу, которую необходимо добавить, и нажмите кнопку **Выбрать**.
   
    ![Добавление группы](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Add a group")
6. Щелкните **Выберите разрешения**, выберите необходимые разрешения, а затем укажите, назначить ли разрешения как список ACL по умолчанию, список ACL для доступа или оба этих параметра. Нажмите кнопку **ОК**.
   
    ![Назначение разрешений для группы](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Assign permissions to group")
   
    Дополнительные сведения о разрешениях в Data Lake Store и списках ACL (по умолчанию и для доступа) см. в статье [Контроль доступа в Data Lake Store](data-lake-store-access-control.md).
7. В колонке **Добавить настраиваемый доступ** нажмите кнопку **ОК**. Теперь добавленная группа с соответствующими разрешениями отобразится в колонке **Доступ** .
   
    ![Назначение разрешений для группы](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Assign permissions to group")    

## <a name="next-steps"></a>Дальнейшие действия
В этой статье мы создали веб-приложение Azure AD и собрали сведения, необходимые в клиентских приложениях, создаваемых с помощью пакетов SDK для .NET, Java и др. Из следующих статей вы узнаете, как с помощью веб-приложения Azure AD проходить аутентификацию в Data Lake Store, а затем выполнить другие операции в хранилище.

* [Начало работы с хранилищем озера данных Azure с помощью пакета SDK .NET](data-lake-store-get-started-net-sdk.md)
* [Начало работы с хранилищем озера данных Azure с помощью пакета SDK .NET](data-lake-store-get-started-java-sdk.md)




<!--HONumber=Nov16_HO5-->


