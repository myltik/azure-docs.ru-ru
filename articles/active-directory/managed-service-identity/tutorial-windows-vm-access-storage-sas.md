---
title: Использование управляемого удостоверения службы виртуальной машины Windows для доступа к службе хранилища Azure с учетными данными SAS
description: Руководство по использованию управляемого удостоверения службы (MSI) виртуальной машины Windows для доступа к службе хранилища Azure с учетными данными SAS вместо ключа доступа к учетной записи хранения.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: ac807bf12e0d6a465b1741d34e6d3d02885c8ea1
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303779"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Руководство по использованию управляемого удостоверения службы виртуальной машины Windows для доступа к службе хранилища Azure с учетными данными SAS

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

В этом руководстве вы узнаете, как применить управляемое удостоверение службы (MSI) для виртуальной машины Windows и как с его помощью получить учетные данные SAS (подписанный URL-адрес). Для этого используются [учетные данные SAS службы](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

SAS службы позволяет предоставить определенной службе (в нашем случае службе BLOB-объектов) ограниченный доступ к объектам в учетной записи хранения на ограниченный срок, не передавая ей ключ доступа к учетной записи. Подписанный URL-адрес можно использовать стандартным образом для операций с хранилищем, например при использовании пакета SDK для службы хранилища Azure. В этом руководстве демонстрируются отправка и скачивание большого двоичного объекта с помощью PowerShell службы хранилища Azure. Вы научитесь:


> [!div class="checklist"]
> * включать MSI на виртуальной машине Windows; 
> * предоставлять виртуальной машине доступ к подписанным URL-адресам учетной записи хранения в Resource Manager; 
> * получать маркер доступа с помощью удостоверения виртуальной машины и использовать его для извлечения SAS из Resource Manager. 

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Создание виртуальной машины Windows в новой группе ресурсов

В рамках этого руководства мы создадим виртуальную машину Windows. Вы также можете активировать MSI на имеющейся виртуальной машине.

1.  Нажмите кнопку **+ Создание службы** в верхнем левом углу портала Azure.
2.  Выберите **Вычисления**, а затем — **Windows Server 2016 Datacenter**. 
3.  Введите сведения о виртуальной машине. **Имя пользователя** и **пароль**, созданные здесь, используются для входа на виртуальную машину.
4.  В раскрывающемся списке выберите нужную **подписку** для виртуальной машины.
5.  Чтобы выбрать новую **группу ресурсов**, в которой вы хотите создать виртуальную машину, щелкните **Создать**. По завершении нажмите кнопку **ОК**.
6.  Выберите размер виртуальной машины. Чтобы просмотреть дополнительные размеры, выберите **Просмотреть все** или измените фильтр **Supported disk type** (Поддерживаемые типы диска). В колонке параметров оставьте значения по умолчанию и нажмите кнопку **OK**.

    ![Замещающий текст](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Активация MSI на виртуальной машине

MSI на виртуальной машине позволяет получить маркер доступа из Azure AD без необходимости указывать в коде учетные данные. На самом деле включение MSI выполняет две функции: выполняется регистрация виртуальной машины в Azure Active Directory для создания управляемого удостоверения и его настройка на этой виртуальной машине.

1. Перейдите в группу ресурсов новой виртуальной машины и выберите виртуальную машину, созданную на предыдущем шаге.
2. В разделе настроек виртуальной машины на левой панели щелкните **Конфигурация**.
3. Чтобы зарегистрировать и активировать MSI, нажмите кнопку **Да**. Чтобы удалить удостоверение, нажмите кнопку "Нет".
4. Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию.

    ![Замещающий текст](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения. 

Если у вас еще нет учетной записи хранения, создайте ее. Этот шаг можно пропустить и предоставить виртуальной машине доступ с помощью MSI к учетным данным SAS имеющейся учетной записи хранения. 

1. Нажмите кнопку **+ Создание службы** в верхнем левом углу портала Azure.
2. Щелкните **Хранилище**, а затем **Учетная запись хранения**, после чего отобразится новая панель "Создание учетной записи хранения".
3. Введите имя учетной записи хранения, которая будет использоваться далее.  
4. Для параметра **Модель развертывания** необходимо выбрать "Resource Manager", а для параметра **Account kind** (Тип учетной записи) — "Общее назначение". 
5. Убедитесь, что значения **подписки** и **группы ресурсов** соответствуют указанным при создании виртуальной машины на предыдущем шаге.
6. Нажмите кнопку **Создать**.

    ![Создание учетной записи хранения](../media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Создание контейнера больших двоичных объектов в учетной записи хранения

Позже мы отправим и скачаем файл в новую учетную запись хранения. Так как файлам необходимо хранилище BLOB-объектов, нужно создать контейнер больших двоичных объектов, в котором будет храниться файл.

1. Вернитесь к только что созданной учетной записи хранения.
2. Щелкните ссылку **Контейнеры** в разделе "Служба BLOB-объектов" на панели слева.
3. Щелкните **+ Контейнер** в верхней части страницы, после чего появится панель "Новый контейнер".
4. Присвойте контейнеру имя, выберите уровень доступа, а затем нажмите кнопку **ОК**. Указанное имя будет использоваться далее в этом руководстве. 

    ![Создание контейнера хранилища](../media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>Предоставление прав на использование SAS хранилища для MSI виртуальной машины 

В службе хранилища Azure не встроена поддержка проверки подлинности Azure AD.  Тем не менее, можно использовать MSI для извлечения SAS хранилища из Resource Manager и для доступа к хранилищу с помощью SAS.  На этом шаге вы предоставите виртуальной машине доступ к использованию SAS учетной записи хранения.   

1. Вернитесь к только что созданной учетной записи хранения.   
2. Щелкните ссылку **Управление доступом (IAM)** на панели слева.  
3. Щелкните **+ Добавить** в верхней части страницы, чтобы добавить новое назначение роли для виртуальной машины.
4. Для параметра **Роль** в правой части страницы выберите значение "Участник учетных записей хранения".  
5. Рядом с ним в раскрывающемся списке **Назначение доступа к** выберите ресурс "Виртуальная машина".  
6. Убедитесь, что нужная подписка присутствует в раскрывающемся списке **Подписки**, и установите для параметра **Группы ресурсов** значение "Все группы ресурсов".  
7. И наконец, в разделе **Выбрать** выберите в раскрывающемся списке нужную виртуальную машину Windows и нажмите кнопку **Сохранить**. 

    ![Замещающий текст](../media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и вызов Azure Resource Manager с его помощью 

Далее в этом руководстве мы будем работать с виртуальной машиной, которую только что создали.

На этом этапе вы примените командлеты PowerShell для Azure Resource Manager.  Если у вас он не установлен, [скачайте последнюю версию](https://docs.microsoft.com/powershell/azure/overview), прежде чем продолжить.

1. На портале Azure перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Windows и вверху в разделе **Обзор** щелкните **Подключить**.
2. Введите **имя пользователя** и **пароль**, добавленные при создании виртуальной машины Windows. 
3. Теперь, когда создано **подключение к удаленному рабочему столу** с виртуальной машиной, откройте PowerShell в удаленном сеансе. 
4. С помощью команды Invoke-WebRequest PowerShell сделайте запрос к локальной конечной точке MSI, чтобы получить маркер доступа к Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Значение параметра resource должно точно совпадать со значением, которое будет использоваться в Azure AD. Если используется идентификатор ресурса Resource Manager, добавьте косую черту после универсального кода ресурса (URI).
    
    Затем извлеките элемент content, который хранится в виде форматированной строки JSON в объекте $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Затем извлеките маркер доступа из ответа.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Получение учетных данных SAS из Azure Resource Manager для обращения к хранилищу 

Теперь используйте PowerShell для выполнения вызова Resource Manager с помощью маркера доступа, полученного на предыдущем этапе, чтобы создать учетные данные SAS хранилища. Как только мы получим учетные данные SAS, можно вызывать операции хранилища.

Для этого мы будем использовать следующие параметры HTTP-запроса, чтобы создать учетные данные SAS:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Эти параметры включены в тело запроса POST на получение учетных данных SAS. Дополнительные сведения о параметрах для создания учетных данных SAS см. в [справочнике по выводу SAS службы с использованием REST](/rest/api/storagerp/storageaccounts/listservicesas).

Сначала преобразуйте параметры в формат JSON, а затем выполните вызов конечной точки хранилища `listServiceSas` для создания учетных данных SAS:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> URL-адрес чувствителен к регистру, поэтому должен использоваться тот же регистр, который использовался, когда вы присваивали имя группе ресурсов. Проверьте, чтобы в resourceGroups обязательно использовался символ "G" (прописная буква). 

Теперь можно извлечь учетные данные SAS из ответа.

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Учетные данные SAS будут выглядеть следующим образом:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Затем нужно создать файл с именем "test.txt". Используйте учетные данные SAS для проверки подлинности с помощью командлета `New-AzureStorageContent` и отправьте файл в контейнер больших двоичных объектов, а затем скачайте его.

```bash
echo "This is a test text file." > test.txt
```

Сначала установите командлеты службы хранилища Azure с помощью `Install-Module Azure.Storage`, а затем отправьте созданный большой двоичный объект с помощью командлета PowerShell `Set-AzureStorageBlobContent`.

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Ответ:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Скачать отправленный большой двоичный объект с помощью командлета PowerShell `Get-AzureStorageBlobContent`.

```powershell
Get-AzureStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Ответ:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```


## <a name="related-content"></a>Связанная информация

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](overview.md).
- Чтобы узнать, как выполнить шаги из этого руководства с использованием ключа учетной записи хранения, см. статью [Доступ к службе хранилища Azure с помощью управляемого удостоверения службы виртуальной машины Windows](tutorial-windows-vm-access-storage.md).
- Дополнительные сведения о возможностях SAS для учетной записи службы хранилища Azure см. в следующих статьях:
  - [Использование подписанных URL-адресов (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Создание подписанного URL-адреса уровня службы](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.


