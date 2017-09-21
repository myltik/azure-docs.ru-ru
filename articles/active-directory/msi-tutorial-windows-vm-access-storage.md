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
ms.date: 09/14/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 86d88e3d44f442171f69d0baea5e7d689b963277
ms.contentlocale: ru-ru
ms.lasthandoff: 09/14/2017

---

# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-storage"></a>Доступ к службе хранилища Azure с помощью управляемого удостоверения службы (MSI) виртуальной машины Windows

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

В этой статье описывается активация управляемого удостоверения службы (MSI) виртуальной машины Linux и его использование для получения ключей к хранилищу данных. Вы можете использовать ключи к хранилищу данных стандартным образом при выполнении операций с хранилищем, например при использовании пакета SDK службы хранилища Azure. В рамках этого руководства мы отправим и скачаем большие двоичные объекты с помощью Azure CLI. Вы научитесь:


> [!div class="checklist"]
> * включать MSI на виртуальной машине Windows; 
> * создавать учетную запись хранения;
> * предоставлять виртуальной машине доступ к службе хранилища; 
> * получать маркер доступа к учетной записи хранения, используя удостоверение виртуальной машины. 


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

1. Выберите **виртуальную машину**, на которой нужно активировать MSI.
2. В левой области навигации щелкните **Конфигурация**.
3. Появится страница **Managed Service Identity** (Управляемое удостоверение службы). Чтобы зарегистрировать и активировать MSI, нажмите кнопку **Да**. Чтобы удалить удостоверение, нажмите кнопку "Нет".
4. Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию.

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Если вы хотите проверить расширения на этой **виртуальной машине Windows**, щелкните **Расширения**. Если удостоверение MSI активировано, вы увидите в списке **ManagedIdentityExtensionforWindows**.

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-new-storage-account"></a>Создание учетной записи хранения 

Ключи к хранилищу данных можно использовать в обычном режиме при выполнении операций с хранилищем. В этом примере мы подробно рассмотрим отправку и скачивание больших двоичных объектов с помощью Azure CLI. 

1. Перейдите на боковую панель и выберите **Хранилище**.  
2. Создайте **учетную запись хранения**.  
3. В области **Модель развертывания** введите **диспетчер ресурсов** и **тип учетной записи** с **общим назначением**.  
4. Проверьте, чтобы были выбраны те же **подписка** и **группа ресурсов**, которые использовались при создании **виртуальной машины Linux**.

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="grant-your-vm-identity-access-to-use-storage-keys"></a>Предоставление удостоверению виртуальной машины доступа к ключам к хранилищу данных 

С помощью MSI код может получить маркеры доступа, чтобы пройти аутентификацию и получить доступ к ресурсам, поддерживающим аутентификацию Azure AD.   

1. Перейдите на вкладку **Хранилище**.  
2. Выберите **учетную запись хранения**, созданную ранее.   
3. Перейдите к элементу **Управление доступом (IAM)** на панели слева.  
4. Затем **добавьте** новое назначение роли виртуальной машины, выберите для параметра **Роль** значение **Роль службы оператора ключей учетных записей хранения**.  
5. В раскрывающемся списке далее в поле **Назначение доступа к** выберите ресурс **Виртуальная машина**.  
6. Проверьте, чтобы в раскрывающемся списке **Подписка** была выбрана нужная подписка. В поле **Группа ресурсов** выберите **Все группы ресурсов**.  
7. И наконец, в поле **Выбрать** выберите свою виртуальную машину Windows в раскрывающемся списке, а затем нажмите кнопку **Сохранить**. 

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и вызов Azure Resource Manager с его помощью 

На этом этапе понадобится использовать **PowerShell**.  Если эта среда не установлена, загрузите ее [отсюда](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1. На портале перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Windows и в разделе **Обзор** щелкните **Подключить**. 
2. Введите **имя пользователя** и **пароль**, добавленные при создании виртуальной машины Windows. 
3. Теперь, когда создано **подключение к удаленному рабочему столу** с виртуальной машиной, откройте **PowerShell** в удаленном сеансе. 
4. С помощью команды Invoke-WebRequest PowerShell сделайте запрос к локальной конечной точке MSI, чтобы получить маркер доступа к Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost/50342/oauth2/token -Method GET -Body @resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Значение параметра resource должно точно совпадать со значением, которое будет использоваться в Azure AD. Если используется идентификатор ресурса Resource Manager, добавьте косую черту после универсального кода ресурса (URI).
    
    Затем извлеките полный ответ, который хранится в виде форматированной строки JSON в объекте $response. 
    
    ```powershell
    $content = $repsonse.Content | ConvertFrom-Json
    ```
    Затем извлеките маркер доступа из ответа.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Теперь вызовите Azure Resource Manager с использованием маркера доступа. В этом примере мы также используем команду Invoke-WebRequest PowerShell для вызова Azure Resource Manager и включаем маркер доступа в заголовок авторизации.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE]
    > URL-адрес чувствителен к регистру, поэтому должен использоваться тот же регистр, который использовался, когда вы присваивали имя группе ресурсов. Проверьте, чтобы в resourceGroup обязательно использовался символ верхнего регистра — G.
    
## <a name="get-the-storage-keys-from-azure-resource-manager"></a>Получение ключей к хранилищу данных из Azure Resource Manager 

```powershell
PS C:\> $keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/97f51385-2edc-4b69-bed8-7778dd4cb761/resourceGroups/SKwan_Test/providers/Microsoft.Storage/storageAccounts/skwanteststorage/listKeys/?api-version=2016-12-01 -Method POST$ -Headers @{Authorization="Bearer $ARMToken"}
```

```powershell
PS C:\> $keysContent = $keysResponse.Content | ConvertFrom-Json
```

```powershell
PS C:\> $key = $keysContent.keys[0].value
```

**Создание файла для отправки с помощью Azure CLI**

```bash
echo "This is a test text file." > test.txt
```

**Отправка файла с помощью Azure CLI и прохождение аутентификации с помощью ключа к хранилищу данных**

> [!NOTE]
> Не забудьте сначала установить командлеты службы хранилища Azure Install-Module Azure.Storage. 

Запрос PowerShell:


```powershell
PS C:\> $ctx = New-AzureStorageContext -StorageAccountName skwanteststorage -StorageAccountKey $key
PS C:\> Set-AzureStorageBlobContent -File test.txt -Container testcontainer -Blob testblob -Context $ctx
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

**Скачивание файла с помощью Azure CLI и прохождение аутентификации с помощью ключа к хранилищу данных**

Запрос PowerShell:

```powershell
PS C:\> Get-AzureStorageBlobContent -Blob <blob name> -Container <container name> -Destination <file> -Context $ctx
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





