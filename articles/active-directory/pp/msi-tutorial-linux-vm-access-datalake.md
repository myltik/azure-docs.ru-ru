---
title: Как получить доступ к Azure Data Lake Store с помощью управляемого удостоверения службы виртуальной машины Linux
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
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a70f02fca5ebf575bc009623c3af648a5a80fd70
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32153750"
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Получение доступа к Azure Data Lake Store с помощью управляемого удостоверения службы виртуальной машины Linux

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

В этом руководстве описывается доступ к Azure Data Lake Store с помощью управляемого удостоверения службы (MSI) виртуальной машины Linux. Управляемыми удостоверениями службы автоматически управляет Azure. Они позволяют проходить проверку подлинности в службах, поддерживающих аутентификацию Azure AD, без указания учетных данных в коде. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Включение удостоверения MSI на виртуальной машине Linux. 
> * Предоставление виртуальной машине доступа к Azure Data Lake Store.
> * Получение маркера доступа с использованием удостоверения виртуальной машины и получение доступа к Azure Data Lake Store с его помощью.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Создание виртуальной машины Linux в новой группе ресурсов

В рамках этого руководства мы создадим виртуальную машину Linux. Вы также можете активировать MSI на имеющейся виртуальной машине.

1. Щелкните **Создать ресурс** в верхнем левом углу окна портала Azure.
2. Выберите **Вычисления**, а затем — **Сервер Ubuntu 16.04 LTS**.
3. Введите сведения о виртуальной машине. Для параметра **Тип проверки подлинности** выберите значение **Открытый ключ SSH** или **Пароль**. Созданные учетные данные позволят вам выполнить вход на виртуальную машину.

   ![Замещающий текст](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. В раскрывающемся списке выберите **подписку** для виртуальной машины.
5. Чтобы выбрать новую **группу ресурсов**, в которой вы хотите создать виртуальную машину, щелкните **Создать**. По завершении нажмите кнопку **ОК**.
6. Выберите размер виртуальной машины. Чтобы просмотреть дополнительные размеры, выберите **Просмотреть все** или измените фильтр "Поддерживаемые типы диска". В колонке параметров оставьте значения по умолчанию и нажмите кнопку **OK**.

## <a name="enable-msi-on-your-vm"></a>Активация MSI на виртуальной машине

MSI на виртуальной машине позволяет получить маркер доступа из Azure AD без необходимости указывать в коде учетные данные. На самом деле включение удостоверения MSI необходимо для установки расширения виртуальной машины MSI и непосредственно включения удостоверения MSI в Azure Resource Manager.  

1. Выберите **виртуальную машину**, на которой нужно активировать MSI.
2. В левой области навигации щелкните **Конфигурация**.
3. Появится страница **Managed Service Identity** (Управляемое удостоверение службы). Чтобы зарегистрировать и активировать MSI, нажмите кнопку **Да**. Чтобы удалить удостоверение, нажмите кнопку "Нет".
4. Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию.

   ![Замещающий текст](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Если вы хотите проверить расширения на этой **виртуальной машине Linux**, щелкните раздел **Расширения**. Если удостоверение MSI активировано, вы увидите в списке **ManagedIdentityExtensionforLinux**.

   ![Замещающий текст](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Предоставление виртуальной машине доступа к Azure Data Lake Store

Теперь можно предоставить виртуальной машине доступ к файлам и папкам в Azure Data Lake Store.  Для выполнения этого шага можно использовать имеющееся хранилище Azure Data Lake Store или создать новое.  Чтобы создать новое хранилище Data Lake Store с помощью портала Azure, следуйте этому [краткому руководству по Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-get-started-portal.md). В [документации по Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-overview.md) также есть краткие руководства, описывающие использование Azure CLI и Azure PowerShell.

Создайте новую папку в Data Lake Store и предоставьте удостоверению MSI виртуальной машины разрешение на чтение, запись и выполнение файлов в этой папке.

1. На портале Azure щелкните **Data Lake Store** в области навигации слева.
2. Выберите хранилище Data Lake Store, которое хотите использовать для этого руководства.
3. Щелкните **Обозреватель данных** на панели команд.
4. Выбрана корневая папка Data Lake Store.  На панели команд щелкните **Доступ**.
5. Щелкните **Добавить**.  В поле **Выбор** введите имя виртуальной машины, например **DevTestVM**.  Выберите свою виртуальную машину в результатах поиска, а затем щелкните **Выбрать**.
6. Щелкните **Выбор разрешений**.  Выберите разрешения **Чтение** и **Выполнение** и добавьте их в раздел **Эта папка**, затем добавьте их в качестве **разрешений только для доступа**.  Нажмите кнопку **ОК**.  Разрешения должны быть успешно добавлены.
7. Закройте колонку **Доступ**.
8. В рамках этого руководства мы создадим папку.  Щелкните **Создать папку** в командной строке и введите имя новой папки, например **TestFolder**.  Нажмите кнопку **ОК**.
9. Щелкните созданную папку, а затем щелкните **Доступ** на панели команд.
10. Как и на шаге 5, нажмите кнопку **Добавить**, в поле **Выбор** введите имя виртуальной машины, выберите ее и нажмите кнопку **Выбрать**.
11. Как и на шаге 6, щелкните **Выбор разрешений**, выберите разрешения **Чтение**, **Запись** и **Выполнение**, добавьте их в раздел **Эта папка**, после чего добавьте их в качестве **записи разрешения доступа и записи разрешений по умолчанию**.  Нажмите кнопку **ОК**.  Разрешения должны быть успешно добавлены.

Теперь удостоверение MSI виртуальной машины позволяет выполнять все операции с файлами в созданной папке.  Дополнительные сведения об управлении доступом к Data Lake Store см. в статье [Контроль доступа в Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Получение маркера доступа с использованием удостоверения MSI виртуальной машины и выполнение вызова к файловой системе Azure Data Lake Store с его помощью

В хранилище Azure Data Lake Store реализована поддержка аутентификации Azure AD, поэтому она может напрямую принимать маркеры доступа, полученные с использованием MSI.  Для аутентификации в файловой системе Data Lake Store маркер доступа, выданный службой Azure AD конечной точке файловой системы Data Lake Store, отправляется в заголовке авторизации в формате "Bearer \<ЗНАЧЕНИЕ_МАРКЕРА_ДОСТУПА\>".  Дополнительные сведения о поддержке Data Lake Store для аутентификации Azure AD см. в разделе [Аутентификация в Data Lake Store с помощью Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).

В этом руководстве выполняется аутентификация в файловой системе Data Lake Store посредством REST API с использованием CURL для выполнения запросов REST.

> [!NOTE]
> Клиентские пакеты SDK для файловой системы Data Lake Store еще не поддерживают управляемое удостоверение службы.  Это руководство будет обновлено, как только поддержка будет добавлена в пакет SDK.

Для выполнения этих действий вам потребуется клиент SSH. Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about). Если вам нужна помощь в настройке ключей SSH-клиента, ознакомьтесь с разделом [Использование ключей SSH с Windows в Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) или [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. На портале перейдите на виртуальную машину Linux и в разделе **Обзор** щелкните **Подключиться**.  
2. **Подключитесь** к виртуальной машине с помощью выбранного клиента SSH. 
3. В окне терминала с помощью CURL выполните запрос к локальной конечной точке MSI, чтобы получить маркер доступа для файловой системы Data Lake Store.  Идентификатор ресурса для Data Lake Store: https://datalake.azure.net/.  Очень важно включить в идентификатор ресурса конечную косую черту.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
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

4. С помощью CURL выполните запрос к конечной точке REST файловой системы Data Lake Store, чтобы вывести список папок в корневой папке.  Это простой способ проверить, все ли настроено правильно.  Скопируйте значение маркера доступа из предыдущего шага.  Очень важно, чтобы слово Bearer в заголовке авторизации начиналось с заглавной буквы B.  Имя хранилища Data Lake Store можно найти в разделе **Обзор** колонки "Data Lake Store" на портале Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Успешный ответ выглядит следующим образом.

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Теперь вы можете попробовать передать файл в Data Lake Store.  Сначала создайте файл для передачи.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. С помощью CURL выполните запрос к конечной точке REST файловой системы Data Lake Store, чтобы передать файл в папку, созданную ранее.  Передача подразумевает перенаправление, и CURL выполняет перенаправление автоматически. 

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

Поздравляем!  Вы прошли аутентификацию в файловой системе Data Lake Store с помощью удостоверения MSI виртуальной машины.

## <a name="related-content"></a>Связанная информация

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md).
- Для операций управления Data Lake Store использует Azure Resource Manager.  Дополнительные сведения об использовании удостоверения MSI виртуальной машины для аутентификации в Resource Manager см. в разделе [Получение доступа к Azure Resource Manager с помощью управляемого удостоверения службы виртуальной машины Linux](../managed-service-identity/msi-tutorial-linux-vm-access-arm.md).
- Узнайте больше об [аутентификации пользователей в Data Lake Store с помощью Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- Узнайте больше об [операциях файловой системы в Azure Data Lake Store с использованием REST API](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) или [интерфейсах API файловой системы WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis.md).
- Узнайте больше о [контроле доступа в Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.