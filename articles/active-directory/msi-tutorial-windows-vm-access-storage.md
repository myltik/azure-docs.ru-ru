---
title: "Доступ к службе хранилища Azure с помощью MSI виртуальной машины Windows"
description: "В рамках этого руководства вы узнаете, как получить доступ к службе хранилища Azure с помощью управляемого удостоверения службы (MSI) виртуальной машины Windows."
services: active-directory
documentationcenter: 
author: elkuzmen
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 09d4f81b190329421fc9fd2ebf98b941cb033a08
ms.contentlocale: ru-ru
ms.lasthandoff: 09/20/2017

---

# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage"></a>Доступ к службе хранилища Azure с помощью управляемого удостоверения службы виртуальной машины Windows

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

В этой статье описывается активация управляемого удостоверения службы (MSI) виртуальной машины Windows и его использование для получения ключей к хранилищу данных. Вы можете использовать ключи к хранилищу данных стандартным образом при выполнении операций с хранилищем, например при использовании пакета SDK службы хранилища Azure. В рамках этого руководства мы отправим и скачаем большие двоичные объекты с помощью PowerShell службы хранилища Azure. Вы научитесь:


> [!div class="checklist"]
> * включать MSI на виртуальной машине Windows; 
> * предоставлять виртуальной машине доступ к ключам к хранилищу данных в Resource Manager; 
> * получать маркер доступа с помощью удостоверения виртуальной машины и использовать его для извлечения ключей к хранилищу данных из Resource Manager. 


Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Создание виртуальной машины Windows в новой группе ресурсов

В рамках этого руководства мы создадим виртуальную машину Windows. Вы также можете активировать MSI на имеющейся виртуальной машине.

1.  Щелкните **Создать** в верхнем левом углу портала Azure.
2.  Выберите **Вычисления**, а затем — **Windows Server 2016 Datacenter**. 
3.  Введите сведения о виртуальной машине. **Имя пользователя** и **пароль**, созданные здесь, используются для входа на виртуальную машину.
4.  В раскрывающемся списке выберите нужную **подписку** для виртуальной машины.
5.  Чтобы выбрать новую **группу ресурсов**, в которой вы хотите создать виртуальную машину, щелкните **Создать**. По завершении нажмите кнопку **ОК**.
6.  Выберите размер виртуальной машины. Чтобы просмотреть дополнительные размеры, выберите **Просмотреть все** или измените фильтр **Supported disk type** (Поддерживаемые типы диска). В колонке параметров оставьте значения по умолчанию и нажмите кнопку **OK**.

    ![Замещающий текст](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Активация MSI на виртуальной машине

MSI на виртуальной машине позволяет получить маркер доступа из Azure AD без необходимости указывать в коде учетные данные. На самом деле активация MSI необходима для выполнения двух функций: установки расширения виртуальной машины в MSI и непосредственно активации MSI на виртуальной машине.  

1. Перейдите в группу ресурсов новой виртуальной машины и выберите виртуальную машину, созданную на предыдущем шаге.
2. В разделе настроек виртуальной машины в левой части экрана щелкните **Конфигурация**.
3. Чтобы зарегистрировать и активировать MSI, нажмите кнопку **Да**. Чтобы удалить удостоверение, нажмите кнопку "Нет".
4. Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию.

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Если вы хотите проверить расширения на этой виртуальной машине, щелкните **Расширения**. Если удостоверение MSI активировано, вы увидите в списке **ManagedIdentityExtensionforWindows**.

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>Создание учетной записи хранения 

Если у вас еще нет учетной записи хранения, создайте ее. Этот шаг можно пропустить и предоставить виртуальной машине доступ с помощью MSI к ключам имеющейся учетной записи хранения. 

1. Щелкните **Создать** в верхнем левом углу портала Azure.
2. Щелкните **Хранилище**, а затем **Учетная запись хранения**, после чего отобразится новая панель "Создание учетной записи хранения".
3. Введите имя учетной записи хранения, которая будет использоваться далее.  
4. Для параметра **Модель развертывания** необходимо выбрать "Resource Manager", а для параметра **Account kind** (Тип учетной записи) — "Общее назначение". 
5. Убедитесь, что значения **подписки** и **группы ресурсов** соответствуют указанным при создании виртуальной машины на предыдущем шаге.
6. Щелкните **Создать**.

    ![Создание учетной записи хранения](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Создание контейнера больших двоичных объектов в учетной записи хранения

Позже мы отправим и скачаем файл в новую учетную запись хранения. Так как файлам необходимо хранилище BLOB-объектов, нужно создать контейнер больших двоичных объектов, в котором будет храниться файл.

1. Вернитесь к только что созданной учетной записи хранения.
2. Щелкните ссылку **Контейнеры** на панели навигации слева в разделе "Служба BLOB-объектов".
3. Щелкните **+ Контейнер** в верхней части страницы, после чего появится панель "Новый контейнер".
4. Присвойте контейнеру имя, выберите уровень доступа, а затем нажмите кнопку **ОК**. Указанное имя будет использоваться далее в этом руководстве. 

    ![Создание контейнера хранилища](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vm-identity-access-to-use-storage-keys"></a>Предоставление удостоверению виртуальной машины доступа к ключам к хранилищу данных 

В службе хранилища Azure не встроена поддержка проверки подлинности Azure AD.  Тем не менее, можно использовать MSI для извлечения из Resource Manager ключей хранилища и использовать их для доступа к хранилищу.  На этом шаге нужно предоставить вашей виртуальной машине доступ с помощью MSI к ключам учетной записи хранения.   

1. Перейдите на вкладку **Хранилище**.  
2. Выберите **учетную запись хранения**, созданную ранее.   
3. Перейдите к элементу **Управление доступом (IAM)** на панели слева.  
4. Затем **добавьте** новое назначение роли виртуальной машины, выберите для параметра **Роль** значение **Роль службы оператора ключей учетных записей хранения**.  
5. В раскрывающемся списке далее в поле **Назначение доступа к** выберите ресурс **Виртуальная машина**.  
6. Проверьте, чтобы в раскрывающемся списке **Подписка** была выбрана нужная подписка. В поле **Группа ресурсов** выберите **Все группы ресурсов**.  
7. И наконец, в поле **Выбрать** выберите свою виртуальную машину Windows в раскрывающемся списке, а затем нажмите кнопку **Сохранить**. 

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и вызов Azure Resource Manager с его помощью 

На этом этапе понадобится использовать **PowerShell** Azure Resource Manager.  Если у вас он не установлен, [скачайте последнюю версию](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1), прежде чем продолжить.

1. На портале перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Windows и в разделе **Обзор** щелкните **Подключить**. 
2. Введите **имя пользователя** и **пароль**, добавленные при создании виртуальной машины Windows. 
3. Теперь, когда создано **подключение к удаленному рабочему столу** с виртуальной машиной, откройте **PowerShell** в удаленном сеансе. 
4. С помощью команды Invoke-WebRequest PowerShell сделайте запрос к локальной конечной точке MSI, чтобы получить маркер доступа к Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Значение параметра resource должно точно совпадать со значением, которое будет использоваться в Azure AD. Если используется идентификатор ресурса Resource Manager, добавьте косую черту после универсального кода ресурса (URI).
    
    Затем извлеките полный ответ, который хранится в виде форматированной строки JSON в объекте $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Затем извлеките маркер доступа из ответа.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
 
## <a name="get-storage-keys-from-azure-resource-manager-to-make-storage-calls"></a>Получение ключей к хранилищу данных из Azure Resource Manager для создания вызовов хранилища 

Теперь мы используем PowerShell для выполнения вызова к Resource Manager с помощью маркера доступа, полученного на предыдущем этапе, чтобы получить ключ доступа к хранилищу. После получения ключа доступа к хранилищу можно вызвать операции отправки или скачивания в хранилище.

```powershell
PS C:\> $keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> URL-адрес чувствителен к регистру, поэтому должен использоваться тот же регистр, который использовался, когда вы присваивали имя группе ресурсов. Проверьте, чтобы в resourceGroups обязательно использовался символ "G" (прописная буква). 

```powershell
PS C:\> $keysContent = $keysResponse.Content | ConvertFrom-Json
PS C:\> $key = $keysContent.keys[0].value
```

Затем нужно создать файл с именем "test.txt". Используйте ключ к хранилищу данных для проверки подлинности с помощью PowerShell службы хранилища Azure и отправьте файл в контейнер больших двоичных объектов, а затем скачайте его.

```bash
echo "This is a test text file." > test.txt
```

> [!NOTE]
> Не забудьте сначала установить командлеты службы хранилища Azure Install-Module Azure.Storage. 

Отправить созданный большой двоичный объект можно с помощью командлета PowerShell `Set-AzureStorageBlobContent`.

```powershell
PS C:\> $ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
PS C:\> Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Ответ:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Скачать отправленный большой двоичный объект с помощью командлета PowerShell `Get-AzureStorageBlobContent`.

```powershell
PS C:\> Get-AzureStorageBlobContent -Blob <blob name> -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Ответ:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```





