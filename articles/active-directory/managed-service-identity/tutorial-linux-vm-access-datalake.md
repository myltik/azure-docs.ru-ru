---
title: Получение доступа к Azure Data Lake Store с помощью управляемого удостоверения службы виртуальной машины Linux
description: Руководство, в котором показано, как получить доступ к Azure Data Lake Store с помощью управляемого удостоверения службы (MSI) виртуальной машины Linux.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: f9dc1e87dee83aa3f10d5319ac3df3933b7d96a9
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="use-managed-service-identity-for-a-linux-vm-to-access-azure-data-lake-store"></a>Получение доступа к Azure Data Lake Store с помощью управляемого удостоверения службы виртуальной машины Linux

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

В этом руководстве описывается получение доступа к Azure Data Lake Store с помощью управляемого удостоверения службы (MSI) виртуальной машины Linux. Azure автоматически управляет удостоверениями, созданными с помощью MSI. Это удостоверение можно использовать для аутентификации в службах, которые поддерживают аутентификацию Azure Active Directory (Azure AD), не вставляя учетные данные в код. 

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Активирование удостоверения MSI на виртуальной машине Linux. 
> * Предоставление виртуальной машине доступа к Azure Data Lake Store.
> * Получение маркера доступа с использованием удостоверения виртуальной машины и получение доступа к Azure Data Lake Store с его помощью.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портале Azure](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Создание виртуальной машины Linux в новой группе ресурсов

В рамках этого руководства мы создадим виртуальную машину Linux. Вы также можете активировать MSI на имеющейся виртуальной машине.

1. Нажмите кнопку **Создать** в левом верхнем углу портала Azure.
2. Выберите **Вычисления**, а затем — **Сервер Ubuntu 16.04 LTS**.
3. Введите сведения о виртуальной машине. Для параметра **Тип проверки подлинности** выберите значение **Открытый ключ SSH** или **Пароль**. Созданные учетные данные позволят вам выполнить вход на виртуальную машину.

   ![Область основных сведений для создания виртуальной машины](../media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. В списке **Подписка** выберите подписку для виртуальной машины.
5. Чтобы выбрать новую группу ресурсов, в которой вы хотите создать виртуальную машину, щелкните **Группа ресурсов** > **Создать**. По завершении нажмите кнопку **ОК**.
6. Выберите размер виртуальной машины. Чтобы просмотреть дополнительные размеры, выберите **Просмотреть все** или измените фильтр **Supported disk type** (Поддерживаемые типы диска). В области параметров оставьте значения по умолчанию и нажмите кнопку **ОК**.

## <a name="enable-msi-on-your-vm"></a>Активация MSI на виртуальной машине

MSI на виртуальной машине позволяет получить маркеры доступа из Azure AD без необходимости указывать в коде учетные данные. Включение MSI предназначено для двух целей: выполняется регистрация виртуальной машины в Azure Active Directory для создания управляемого удостоверения и его настройка на этой виртуальной машине.

1. В качестве **виртуальной машины** выберите ту, на которой нужно активировать MSI.
2. В левой области выберите раздел **Конфигурация**.
3. Появится страница **Управляемое удостоверение службы**. Чтобы зарегистрировать и активировать удостоверение MSI, выберите **Да**. Если необходимо отключить его, выберите **Нет**.
   ![Выбор "Зарегистрировать в Azure Active Directory"](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)
4. Щелкните **Сохранить**.

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Предоставление виртуальной машине доступа к Azure Data Lake Store

Теперь можно предоставить виртуальной машине доступ к файлам и папкам в Azure Data Lake Store. Для выполнения этого шага можно использовать имеющийся экземпляр хранилища Data Lake Store или создать новый. Чтобы создать новый экземпляр хранилища Data Lake Store с помощью портала Azure, следуйте указаниям в статье [Начало работы с Azure Data Lake Store с помощью портала Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). В статье [Обзор Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) также есть краткие руководства по использованию Azure CLI и Azure PowerShell.

Создайте новую папку в Data Lake Store и предоставьте удостоверению MSI разрешение на чтение, запись и выполнение файлов в этой папке.

1. В левой области на портале Azure выберите **Data Lake Store**.
2. Выберите экземпляр Data Lake Store, который необходимо использовать.
3. Щелкните **Обозреватель данных** на панели команд.
4. Будет выбрана корневая папка экземпляра Data Lake Store. На панели команд щелкните **Доступ**.
5. Выберите **Добавить**.  В поле **Выбрать** введите имя виртуальной машины, например **DevTestVM**. Выберите свою виртуальную машину в результатах поиска, а затем щелкните **Выбрать**.
6. Щелкните **Выбор разрешений**.  Выберите разрешения **Чтение** и **Выполнение** и добавьте их в раздел **Эта папка**, затем добавьте их в качестве **разрешений только для доступа**. Нажмите кнопку **ОК**.  Разрешения должны быть успешно добавлены.
7. Закройте панель **Доступ**.
8. В рамках этого руководства мы создадим папку. На панели команд щелкните **Создать папку** и введите имя новой папки, например **TestFolder**.  Нажмите кнопку **ОК**.
9. Выберите созданную папку, а затем на панели команд щелкните **Доступ**.
10. Как и на шаге 5, щелкните **Добавить**. В поле **Выбрать** введите имя виртуальной машины. Выберите свою виртуальную машину в результатах поиска, а затем щелкните **Выбрать**.
11. Как и на шаге 6, щелкните **Выберите разрешения**. Выберите разрешения **Чтение**, **Запись** и **Выполнение**, добавьте их в раздел **Эта папка**, после чего установите для них значение **Запись разрешений доступа и запись разрешений по умолчанию**. Нажмите кнопку **ОК**.  Разрешения должны быть успешно добавлены.

Теперь удостоверение MSI позволяет выполнять все операции с файлами в созданной папке. Дополнительные сведения об управлении доступом к Data Lake Store см. в статье [Контроль доступа в Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>Получение маркера доступа и вызов файловой системы Data Lake Store

В хранилище Azure Data Lake Store реализована поддержка аутентификации Azure AD, поэтому она может напрямую принимать маркеры доступа, полученные через удостоверение MSI. Для аутентификации в файловой системе Data Lake Store маркер доступа, выданный службой Azure AD, отправляется конечной точке файловой системы Data Lake Store. Маркер доступа отправляется в заголовок авторизации в формате "Bearer \<ЗНАЧЕНИЕ_МАРКЕРА_ДОСТУПА\>".  Дополнительные сведения о поддержке Data Lake Store для аутентификации Azure AD см. в статье [Аутентификация в Data Lake Store с помощью Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

В этом руководстве выполняется аутентификация в файловой системе Data Lake Store посредством REST API с использованием CURL для выполнения запросов REST.

> [!NOTE]
> Клиентские пакеты SDK для файловой системы Data Lake Store еще не поддерживают управляемое удостоверение службы.

Для выполнения этих действий вам потребуется клиент SSH. Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about). Если вам нужна помощь в настройке ключей SSH-клиента, ознакомьтесь со статьей [Использование ключей SSH с Windows в Azure](../../virtual-machines/linux/ssh-from-windows.md) или [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. На портале перейдите к виртуальной машине Linux. В разделе **Обзор** выберите **Подключиться**.  
2. Подключитесь к виртуальной машине с помощью выбранного клиента SSH. 
3. В окне терминала с помощью CURL выполните запрос к локальной конечной точке MSI, чтобы получить маркер доступа для файловой системы Data Lake Store. Идентификатор ресурса для Data Lake Store: https://datalake.azure.net/.  Очень важно добавить в идентификатор ресурса конечную косую черту.
    
   ```bash
   curl http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F -H Metadata:true   
   ```
    
   Успешный ответ содержит маркер доступа, используемый для аутентификации в Data Lake Store.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. С помощью CURL выполните запрос к конечной точке REST файловой системы Data Lake Store, чтобы вывести список папок в корневой папке. Это простой способ проверить, все ли настроено правильно. Скопируйте значение маркера доступа из предыдущего шага. Очень важно, чтобы слово Bearer в заголовке авторизации начиналось с заглавной буквы B. Имя экземпляра хранилища Data Lake Store можно найти в разделе **Обзор** панели **Data Lake Store** на портале Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Успешный ответ выглядит следующим образом.

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Теперь вы можете попробовать передать файл в экземпляр Data Lake Store. Сначала создайте файл для передачи.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. С помощью CURL выполните запрос к конечной точке REST файловой системы Data Lake Store, чтобы передать файл в папку, созданную ранее. Передача подразумевает перенаправление, и CURL выполняет перенаправление автоматически. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Успешный ответ выглядит следующим образом.

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

С помощью других интерфейсов API файловой системы Data Lake Store можно добавлять содержимое к файлам, скачивать файлы и многое другое.

Поздравляем! Вы прошли аутентификацию в файловой системе Data Lake Store с помощью удостоверения MSI виртуальной машины Linux.

## <a name="related-content"></a>Связанная информация

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](overview.md).
- Для операций управления Data Lake Store использует Azure Resource Manager.  Дополнительные сведения об использовании удостоверения MSI для аутентификации в Resource Manager см. в статье [Получение доступа к Azure Resource Manager с помощью управляемого удостоверения службы виртуальной машины Linux](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm).
- Узнайте больше об [аутентификации пользователей в Data Lake Store с помощью Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).
- Узнайте больше об [операциях файловой системы в Azure Data Lake Store с использованием REST API](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api) или [интерфейсах API файловой системы WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Узнайте больше о [контроле доступа в Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.
