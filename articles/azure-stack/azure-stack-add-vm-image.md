---
title: "Добавление образа виртуальной машины в Azure Stack | Документация Майкрософт"
description: "Добавление образа виртуальной машины Windows или Linux организации для использования клиентами."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: de8540397b63093457382cf427a65ea0e48b93e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>Создание настраиваемого образа виртуальной машины, доступного в Azure Stack

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*

Azure Stack позволяет операторам сделать настраиваемые образы виртуальных машин доступными своим пользователям. На эти образы можно ссылаться с помощью шаблонов Azure Resource Manager или добавлять к пользовательскому интерфейсу Azure Marketplace путем создания элемента Marketplace. 

## <a name="add-a-vm-image-to-marketplace-with-powershell"></a>Добавление образа виртуальной машины в Marketplace с помощью PowerShell

Выполните следующие предварительные требования из [комплекта разработчика](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) или из внешнего клиента для Windows при [подключении через VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* [Установите PowerShell для Azure Stack](azure-stack-powershell-install.md).  

* Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md).  

* Подготовьте образ виртуального жесткого диска с операционной системой Windows или Linux в формате VHD (не VHDX).
   
   * Для образов Windows выполните инструкции по подготовке в статье [Отправка универсального диска VHD и создание виртуальных машин с его помощью в Azure](../virtual-machines/windows/upload-generalized-managed.md) в разделе о **подготовке диска VHD к отправке**.
   * Для образов Linux выполните шаги по подготовке образов или используйте имеющийся образ Linux Azure Stack, как описано в статье [Добавление образов Linux в Azure Stack](azure-stack-linux.md).  

Теперь выполните следующие действия, чтобы добавить образ в Azure Stack Marketplace:

1. Импортируйте модули Connect и ComputeAdmin:
   
   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # import the Connect and ComputeAdmin modules
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1
   ``` 

2. Войдите в среду Azure Stack. Выполните следующий скрипт в зависимости от того, развертывается ли среда Azure Stack с помощью AAD или AD FS (обязательно замените значения AAD tenantName, GraphAudience и ArmEndpoint в соответствии с конфигурацией среды): 

   а. **Azure Active Directory**. Используйте следующий командлет:

   ```PowerShell
   # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAuidence endpoint for your environment>"

   #Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
   Add-AzureRMEnvironment `
     -Name "AzureStackAdmin" `
     -ArmEndpoint $ArmEndpoint 

   Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience

   $TenantID = Get-AzsDirectoryTenantId `
     -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
     -EnvironmentName AzureStackAdmin

   Login-AzureRmAccount `
     -EnvironmentName "AzureStackAdmin" `
     -TenantId $TenantID 
   ```

   b. **Службы федерации Active Directory (AD FS)**. Используйте следующий командлет:
    
   ```PowerShell
   # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAuidence endpoint for your environment>"

   # Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
   Add-AzureRMEnvironment `
     -Name "AzureStackAdmin" `
     -ArmEndpoint $ArmEndpoint

   Set-AzureRmEnvironment `
     -Name "AzureStackAdmin" `
     -GraphAudience $GraphAudience `
     -EnableAdfsAuthentication:$true

   $TenantID = Get-AzsDirectoryTenantId `
     -ADFS 
     -EnvironmentName AzureStackAdmin 

   Login-AzureRmAccount `
     -EnvironmentName "AzureStackAdmin" `
     -TenantId $TenantID 
   ```
    
3. Добавьте образ виртуальной машины, вызвав командлет `Add-AzsVMImage`. В командлете Add-AzsVMImage укажите для osType значение Windows или Linux. Укажите издателя, предложение, SKU и версию для образа виртуальной машины. Дополнительные сведения о разрешенных параметрах см. в разделе [Параметры](#parameters). Эти параметры используются шаблонами Azure Resource Manager для ссылки на образ виртуальной машины. Ниже приведен пример вызова скрипта:
     
     ```powershell
     Add-AzsVMImage `
       -publisher "Canonical" `
       -offer "UbuntuServer" `
       -sku "14.04.3-LTS" `
       -version "1.0.0" `
       -osType Linux `
       -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
     ```

Команда выполняет следующие действия:

* выполняет проверку подлинности в среде Azure Stack;
* отправляет локальный VHD-файл в только что созданное временное хранилище;
* добавляет образ виртуальной машины в репозиторий образов;
* создает элемент Marketplace.

Чтобы убедиться, что команда была успешно выполнена, перейдите в Marketplace на портале, а затем проверьте, доступен ли образ виртуальной машины в категории **Виртуальные машины**.

![Образ виртуальной машины успешно добавлен](./media/azure-stack-add-vm-image/image5.PNG) 

## <a name="remove-a-vm-image-with-powershell"></a>Удаление образа виртуальной машины с помощью PowerShell

Если образ виртуальной машины, отправленный ранее, больше не требуется, его можно удалить из Marketplace с помощью следующего командлета:

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>Параметры

| Параметр | Описание |
| --- | --- |
| **publisher** |Сегмент имени издателя образа виртуальной машины, которое используется пользователями при развертывании образа. Например, Microsoft. Не используйте пробел или другие специальные символы в этом поле. |
| **offer** |Сегмент имени предложения образа виртуальной машины, которое используется пользователями при развертывании образа виртуальной машины. Например, WindowsServer. Не используйте пробел или другие специальные символы в этом поле. |
| **sku** |Сегмент имени SKU образа виртуальной машины, которое используется пользователями при развертывании образа виртуальной машины. Например, Datacenter2016. Не используйте пробел или другие специальные символы в этом поле. |
| **version** |Сегмент версии образа виртуальной машины, которая используется пользователями при развертывании образа виртуальной машины. Эта версия имеет формат *\#.\#.\#*. Например, 1.0.0. Не используйте пробел или другие специальные символы в этом поле. |
| **osType** |Тип ОС образа должен быть Windows или Linux. |
| **osDiskLocalPath** |Локальный путь к виртуальному жесткому диску ОС, который отправляется в качестве образа виртуальной машины в Azure Stack. |
| **dataDiskLocalPaths** |Дополнительный массив локальных путей для дисков данных, которые можно отправить как часть образа виртуальной машины. |
| **CreateGalleryItem** |Логический флаг, который определяет, следует ли создать элемент в Marketplace. По умолчанию установлено значение true. |
| **title** |Отображаемое имя элемента Marketplace. По умолчанию оно состоит из значений Publisher-Offer-Sku образа виртуальной машины. |
| **description** |Описание элемента Marketplace. |
| **расположение** |Расположение, в которое необходимо опубликовать образ виртуальной машины. По умолчанию установлено значение локального расположения.|
| **osDiskBlobURI** |При необходимости скрипт также принимает универсальный код ресурса (URI) хранилища BLOB-объектов для osDisk. |
| **dataDiskBlobURIs** |При необходимости этот скрипт также принимает массив универсальных кодов ресурсов (URI) хранилища BLOB-объектов для добавления дисков с данными в образ. |

## <a name="add-a-vm-image-through-the-portal"></a>Добавление образа виртуальной машины через портал

> [!NOTE]
> Этот метод требует отдельного создания элементов Marketplace.

Одно из требований к образам заключается в том, чтобы на них можно было ссылаться с помощью URI хранилища BLOB-объектов. Подготовьте образ операционной системы Windows или Linux в формате VHD (не VHDX), а затем отправьте образ в учетную запись хранения в Azure или Azure Stack. Если образ уже отправлен в хранилище BLOB-объектов в Azure или Azure Stack, шаг 1 можно пропустить.

1. [Отправьте образ виртуальной машины Windows в Azure для развертываний Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) или следуйте инструкциям в статье [Add Linux images to Azure Stack](azure-stack-linux.md) (Добавление образов Linux в Azure Stack) для образов Linux. Перед отправкой образа необходимо учитывать следующие моменты:

   * Образ эффективнее отправлять в хранилище BLOB-объектов Azure Stack, чем в хранилище BLOB-объектов Azure, так как для размещения образа в репозитории образов Azure Stack требуется меньше времени. 
   
   * При отправке [образов виртуальной машины Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) не забудьте заменить шаг **Вход в Azure** шагом [Настройка среды PowerShell для оператора Azure Stack](azure-stack-powershell-configure-admin.md).  

   * Запишите URI хранилища BLOB-объектов, куда отправлен образ, который имеет такой формат: *&lt;учетная_запись_хранения&gt;/&lt;контейнер_больших_двоичных_объектов&gt;/&lt;имя_целевого_VHD&gt;*.vhd

   * Чтобы настроить для большого двоичного объекта анонимный доступ, перейдите в контейнер больших двоичных объектов учетной записи хранения, где VHD образа виртуальной машины был отправлен в **большой двоичный объект**, а затем выберите **Политика доступа**. Если требуется, можно создать подписанный URL-адрес для контейнера и включить его как часть URI большого двоичного объекта.

   ![Переход к большим двоичным объектам учетной записи хранения](./media/azure-stack-add-vm-image/image1.png)

   ![Установка общего доступа к большим двоичным объектам](./media/azure-stack-add-vm-image/image2.png)

2. Войдите в Azure Stack в качестве оператора. В меню щелкните **Больше служб** > **Поставщики ресурсов**, выберите **Вычисления** > **Образы виртуальных машин** > **Добавить**.

3. В колонке **Add a VM Image** (Добавление образа виртуальной машины) введите издателя, предложение, номер SKU и версию образа виртуальной машины. Эти сегменты имен ссылаются на образ виртуальной машины в шаблонах Resource Manager. Выберите правильное значение для параметра **osType**. В поле **OD Disk Blob URI** (URI большого двоичного объекта диска ОС) введите URI большого двоичного объекта, куда был отправлен образ, и нажмите кнопку **Создать**, чтобы создать образ виртуальной машины.
   
   ![Начало создания образа](./media/azure-stack-add-vm-image/image4.png)

   При успешном создании образа состояние образа виртуальной машины изменяется на "Успешно".

4. Чтобы сделать образ виртуальной машины более доступным для потребления пользователем в пользовательском интерфейсе, лучше всего [создать элемент Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Дальнейшие действия

[Подготовка виртуальной машины](azure-stack-provision-vm.md)