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
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 69a860f89601fce7614d77f1bcd839b6ca540b88
ms.contentlocale: ru-ru
ms.lasthandoff: 08/29/2017

---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Аутентификация между службами в Data Lake Store с помощью Azure Active Directory
> [!div class="op_single_selector"]
> * [Аутентификация между службами](data-lake-store-authenticate-using-active-directory.md)
> * [Аутентификация пользователей](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store использует Azure Active Directory для аутентификации. Прежде чем создать приложение, которое работает с Azure Data Lake Store или Azure Data Lake Analytics, необходимо решить, как его следует аутентифицировать с помощью Azure Active Directory (Azure AD). Доступно два основных варианта:

* Аутентификация пользователей 
* Аутентификация между службами (в этой статье) 

Оба этих варианта позволят приложению получить маркер OAuth 2.0, который вкладывается в каждый запрос к Azure Data Lake Store или Azure Data Lake Analytics.

Из этой статьи вы узнаете, как создать **веб-приложение Azure AD для аутентификации между службами**. Инструкции по настройке приложения Azure AD для аутентификации пользователей см. в статье [Аутентификация пользователей в Data Lake Store с помощью Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Предварительные требования
* Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Шаг 1. Создание веб-приложения Active Directory

Создайте и настройте веб-приложение Azure AD для аутентификации между службами в Azure Data Lake Store с помощью Azure Active Directory. Инструкции см. в разделе [Создание приложения Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

При выполнении инструкций по ссылке обязательно выберите тип приложения **Веб-приложение или API**, как показано на снимке экрана.

![Создание веб-приложения](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Создание веб-приложения")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Шаг 2. Получение идентификатора приложения, ключа проверки подлинности и идентификатора клиента
При программном входе необходимо указывать идентификатор приложения. Если приложение работает с использованием собственных учетных данных, потребуется также ключ проверки подлинности.

* Инструкции о том, как получить идентификатор приложения и ключ проверки подлинности (который также называется секретом клиента) для приложения, см. в разделе [Получение идентификатора приложения и ключа проверки подлинности](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Инструкции о том, как получить код клиента, см. в разделе [Получение идентификатора клиента](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>Шаг 3. Назначение приложения Azure AD файлу или папке в учетной записи Azure Data Lake Store (только для аутентификации между службами)
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

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Шаг 4. Получение конечной точки маркера OAuth 2.0 (только для приложений на основе Java)

1. Войдите на новый [портал Azure](https://portal.azure.com) и щелкните "Active Directory" в левой области.

2. В левой области щелкните **Регистрация приложений**.

3. В верхней части колонки "Регистрация приложений" щелкните **Конечные точки**.

    ![Конечная точка маркера OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "OAuth token endpoint")

4. Из списка конечных точек скопируйте значение конечной точки маркера OAuth 2.0.

    ![Конечная точка маркера OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "OAuth token endpoint")   

## <a name="next-steps"></a>Дальнейшие действия
В этой статье мы создали веб-приложение Azure AD и собрали сведения, необходимые в клиентских приложениях, создаваемых с помощью пакетов SDK для .NET, Java и др. Из следующих статей вы узнаете, как с помощью веб-приложения Azure AD проходить аутентификацию в Data Lake Store, а затем выполнить другие операции в хранилище.

* [Начало работы с хранилищем озера данных Azure с помощью пакета SDK .NET](data-lake-store-get-started-net-sdk.md)
* [Начало работы с хранилищем озера данных Azure с помощью пакета SDK .NET](data-lake-store-get-started-java-sdk.md)

В данной статье приведены пошаговые инструкции для настройки и подготовки к работе субъекта-пользователя для приложения. Для получения дополнительных сведений ознакомьтесь со следующими статьями:
* [Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Создание субъекта-службы с использованием сертификата](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal#create-service-principal-with-certificate)
* [Other methods to authenticate to Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-authentication-scenarios) (Другие методы аутентификации в Azure AD)



