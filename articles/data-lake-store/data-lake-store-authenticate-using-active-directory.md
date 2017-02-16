---
title: "Аутентификация между службами в Data Lake Store с помощью Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как реализовать аутентификацию между службами в Data Lake Store с помощью Azure Active Directory."
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
ms.date: 01/10/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9019a4115e81a7d8f1960098b1138cd437a0460b
ms.openlocfilehash: dac6c9f3be7b4535f8cb30a9ec0c1e398ca5ff28


---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Аутентификация между службами в Data Lake Store с помощью Azure Active Directory
> [!div class="op_single_selector"]
> * [Аутентификация между службами](data-lake-store-authenticate-using-active-directory.md)
> * [Аутентификация пользователей](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store использует Azure Active Directory для аутентификации. Прежде чем создать приложение, которое работает с Azure Data Lake Store или Azure Data Lake Analytics, необходимо решить, как его следует аутентифицировать посредством Azure Active Directory (Azure AD). Доступно два основных варианта:

* Аутентификация пользователей 
* Аутентификация между службами (в этой статье) 

Оба этих варианта позволят приложению получить маркер OAuth 2.0, который вкладывается в каждый запрос к Azure Data Lake Store или Azure Data Lake Analytics.

Из этой статьи вы узнаете, как создать веб-приложение Azure AD для аутентификации между службами. Инструкции по настройке приложения Azure AD для аутентификации пользователей см. в статье [Аутентификация пользователей в Data Lake Store с помощью Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Предварительные требования
* Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-active-directory-application"></a>Создание приложения Active Directory
Из этого раздела вы узнаете, как создать и настроить веб-приложение Azure AD для аутентификации между службами в Azure Data Lake Store с помощью Azure Active Directory. Обратите внимание, что создавая "приложение Active Directory", вы фактически создаете субъект-службу, а не приложение или код.

### <a name="step-1-create-an-azure-active-directory-application"></a>Шаг 1. Создание приложения Azure Active Directory
1. Войдите в свою учетную запись Azure через [классический портал](https://manage.windowsazure.com/).
2. Выберите **Active Directory** на левой панели.
   
     ![выбор Active Directory](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
3. Выберите каталог Active Directory, который хотите использовать для создания нового приложения. Если у вас несколько каталогов Active Directory, то приложение рекомендуется создавать в каталоге вашей подписки. Доступ к ресурсам в подписке можно предоставить только тем приложением, которые находятся в том же каталоге, что и подписка.  
   
     ![выбор каталога](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
4. Чтобы просмотреть приложения в каталоге, щелкните **Приложения**.
   
     ![просмотр приложений](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)
5. Если ранее вы не создавали приложения в этом каталоге, вы увидите примерно следующее. Щелкните **Добавить приложение**.
   
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

#### <a name="note-down-the-following-properties-that-you-will-need-for-the-next-steps"></a>Запишите эти свойства, так как они вам потребуются при выполнении следующих действий:
1. Имя веб-приложения, созданное ранее на шаге 1.6.
2. Идентификатор клиента, полученный ранее на шаге 2.2.
3. Ключ, созданный ранее на шаге 2.4.
4. Идентификатор клиента, полученный ранее на шаге 2.5.

### <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>Шаг 3. Назначение приложения Azure AD файлу или папке в учетной записи Azure Data Lake Store (только для аутентификации между службами)
1. Войдите на новый [портал Azure](https://portal.azure.com) и откройте учетную запись Azure Data Lake Store, которую необходимо связать с ранее созданным приложением Azure Active Directory.
2. В колонке учетной записи «Хранилище озера данных» щелкните **Обозреватель данных**.
   
    ![Создание каталогов в учетной записи Data Lake Store](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Создание каталогов в учетной записи Data Lake")
3. В колонке **Обозреватель данных** щелкните файл (или папку), к которому необходимо предоставить доступ для приложения Azure AD, а затем нажмите кнопку **Доступ**. Чтобы настроить доступ к файлу, необходимо нажать кнопку **Доступ** в колонке **Предварительный просмотр файла**.
   
    ![Настройка списков управления доступом в файловой системе Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Настройка списков управления доступом в файловой системе Data Lake")
4. В колонке **Доступ** перечислены стандартные и пользовательские варианты доступа, уже назначенные корню. Щелкните значок **Добавить** , чтобы добавить ACL пользовательского уровня.
   
    ![Перечисление стандартных и пользовательских сценариев доступа](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Перечисление стандартных и пользовательских сценариев доступа")
5. Щелкните значок **Добавить**, чтобы открыть колонку **Добавить настраиваемый доступ**. В этой колонке щелкните **Выберите пользователя или группу**, а затем в колонке **Выберите пользователя или группу** найдите созданное ранее приложение Azure Active Directory. Если у вас много групп для поиска, воспользуйтесь текстовым полем в верхней части для фильтрации по имени группы. Выберите группу, которую необходимо добавить, и нажмите кнопку **Выбрать**.
   
    ![Добавление группы](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Добавление группы")
6. Щелкните **Выберите разрешения**, выберите необходимые разрешения, а затем укажите, назначить ли разрешения как список ACL по умолчанию, список ACL для доступа или оба этих параметра. Нажмите кнопку **ОК**.
   
    ![Назначение разрешений для группы](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Назначение разрешений для группы")
   
    Дополнительные сведения о разрешениях в Data Lake Store и списках ACL (по умолчанию и для доступа) см. в статье [Контроль доступа в Data Lake Store](data-lake-store-access-control.md).
7. В колонке **Добавить настраиваемый доступ** нажмите кнопку **ОК**. Теперь добавленная группа с соответствующими разрешениями отобразится в колонке **Доступ** .
   
    ![Назначение разрешений для группы](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Назначение разрешений для группы")    

## <a name="next-steps"></a>Дальнейшие действия
В этой статье мы создали веб-приложение Azure AD и собрали сведения, необходимые в клиентских приложениях, создаваемых с помощью пакетов SDK для .NET, Java и др. Из следующих статей вы узнаете, как с помощью веб-приложения Azure AD проходить аутентификацию в Data Lake Store, а затем выполнить другие операции в хранилище.

* [Начало работы с хранилищем озера данных Azure с помощью пакета SDK .NET](data-lake-store-get-started-net-sdk.md)
* [Начало работы с хранилищем озера данных Azure с помощью пакета SDK .NET](data-lake-store-get-started-java-sdk.md)

В данной статье приведены пошаговые инструкции для настройки и подготовки к работе субъекта-пользователя для приложения. Для получения дополнительных сведений ознакомьтесь со следующими статьями:
* [Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Создание субъекта-службы с использованием сертификата](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal#create-service-principal-with-certificate)
* [Other methods to authenticate to Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-authentication-scenarios) (Другие методы аутентификации в Azure AD)





<!--HONumber=Jan17_HO4-->


