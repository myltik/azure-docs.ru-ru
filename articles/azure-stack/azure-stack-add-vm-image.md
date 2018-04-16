---
title: Добавление образа виртуальной машины в Azure Stack | Документация Майкрософт
description: Добавление образа виртуальной машины Windows или Linux организации для использования клиентами.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/05/2018
ms.author: mabrigg
ms.openlocfilehash: eb2035f6e667a9b3ab642d42cb9bb5ecf5c86fb1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>Создание настраиваемого образа виртуальной машины, доступного в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В Azure Stack операторы могут сделать настраиваемые образы виртуальных машин доступными своим пользователям. На эти образы можно ссылаться с помощью шаблонов Azure Resource Manager или добавлять к пользовательскому интерфейсу Azure Marketplace в качестве элемента Marketplace.

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>Добавление образа виртуальной машины в Marketplace с помощью PowerShell

Выполните следующие предварительные требования с помощью либо [пакета средств разработки](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), либо внешнего клиента на базе Windows (при [подключении через VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)).

1. [Установите PowerShell для Azure Stack](azure-stack-powershell-install.md).  

2. Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md).  

3. Подготовьте образ виртуального жесткого диска с операционной системой Windows или Linux в формате VHD (не в формате VHDX).

   * Инструкции по подготовке образа Windows см. в статье [Отправка универсального диска VHD и создание виртуальных машин с его помощью в Azure](../virtual-machines/windows/upload-generalized-managed.md).

   * Инструкции для образов виртуальных машин Linux см. в статье [Добавление образов Linux в Azure Stack](azure-stack-linux.md). Выполните действия, чтобы подготовить образ, или используйте имеющийся образ Azure Stack Linux, как описано в статье.    

   Azure Stack поддерживает диски фиксированного размера в формате VHD. Фиксированный формат структурирует логический диск в файле линейно, то есть смещение диска X хранится в смещении BLOB-объекта X. Небольшая сноска в конце BLOB-объекта описывает свойства VHD-файла. Чтобы проверить, фиксированного ли размера ваш диск, используйте команду [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) в PowerShell.  

   > [!IMPORTANT]
   >  Azure Stack не поддерживает динамические диски VHD. Изменение размера динамического диска, подключенного к виртуальной машине, приведет к неисправному состоянию виртуальной машины. Чтобы устранить эту проблему, удалите виртуальную машину, не удаляя ее диск — большой двоичный объект VHD в учетной записи хранения. Затем преобразуйте VHD из динамического диска в диск с фиксированным размером и повторно создайте виртуальную машину.

Чтобы добавить образ в Azure Stack Marketplace, сделайте следующее:

1. Импортируйте модули Connect и ComputeAdmin:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1
   ```

2. Войдите в среду Azure Stack. Выполните один из следующих скриптов, в зависимости от того, с помощью чего развернута среда Azure Stack (Azure Active Directory (Azure AD) или службы федерации Active Directory (AD FS)). (Замените Azure AD значение `tenantName`, конечную точку `GraphAudience` и значение `ArmEndpoint` в соответствии с конфигурацией своей среды.)

    * **Azure Active Directory**. Выполните следующий командлет:

      ```PowerShell
      # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "<Resource Manager endpoint for your environment>"

      # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "<GraphAuidence endpoint for your environment>"

      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
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

   * **Службы федерации Active Directory.** Выполните следующий командлет:

        ```PowerShell
        # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
        $ArmEndpoint = "<Resource Manager endpoint for your environment>"

        # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
        $GraphAudience = "<GraphAuidence endpoint for your environment>"

        # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
        Add-AzureRMEnvironment `
          -Name "AzureStackAdmin" `
          -ArmEndpoint $ArmEndpoint

        Set-AzureRmEnvironment `
          -Name "AzureStackAdmin" `
          -GraphAudience $GraphAudience `
          -EnableAdfsAuthentication:$true

        $TenantID = Get-AzsDirectoryTenantId `
          -ADFS `
          -EnvironmentName AzureStackAdmin

        Login-AzureRmAccount `
          -EnvironmentName "AzureStackAdmin" `
          -TenantId $TenantID
        ```

3. Добавьте образ виртуальной машины, вызвав командлет `Add-AzsVMImage`. В командлете `Add-AzsVMImage` укажите для `osType` значение Windows или Linux. Укажите издателя, предложение, SKU и версию для образа виртуальной машины. Дополнительные сведения о разрешенных параметрах см. в [этом разделе](#parameters). Эти параметры используются шаблонами Azure Resource Manager для ссылки на образ виртуальной машины. В следующем примере вызывается скрипт:

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

Чтобы убедиться, что команда выполнена успешно, на портале перейдите в Marketplace. Убедитесь, что образ виртуальной машины доступен в категории **Вычисления**.

![Образ виртуальной машины успешно добавлен](./media/azure-stack-add-vm-image/verify-vm.png)

## <a name="remove-a-vm-image-by-using-powershell"></a>Удаление образа виртуальной машины с помощью PowerShell

Если отправленный образ виртуальной машины больше не требуется, его можно удалить из Marketplace с помощью следующего командлета:

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>Параметры

| Параметр | ОПИСАНИЕ |
| --- | --- |
| **publisher** |Сегмент имени издателя образа виртуальной машины, которое применяется пользователями при развертывании образа. Например, **Microsoft**. Не используйте пробел или другие специальные символы в этом поле. |
| **offer** |Сегмент имени предложения образа виртуальной машины, которое применяется пользователями при развертывании образа виртуальной машины. Например, **WindowsServer**. Не используйте пробел или другие специальные символы в этом поле. |
| **sku** |Сегмент имени SKU образа виртуальной машины, которое применяется пользователями при развертывании образа виртуальной машины. Например, **Datacenter2016**. Не используйте пробел или другие специальные символы в этом поле. |
| **version** |Версия виртуальной машины, которая применяется пользователями при развертывании образа виртуальной машины. Эта версия имеет формат *\#.\#.\#*. Например, **1.0.0**. Не используйте пробел или другие специальные символы в этом поле. |
| **osType** |Тип ОС образа должен быть **Windows** или **Linux**. |
| **osDiskLocalPath** |Локальный путь к виртуальному жесткому диску ОС, который отправляется в качестве образа виртуальной машины в Azure Stack. |
| **dataDiskLocalPaths** |Дополнительный массив локальных путей для дисков данных, которые можно отправить как часть образа виртуальной машины. |
| **CreateGalleryItem** |Логический флаг, который определяет, следует ли создать элемент в Marketplace. По умолчанию установлено значение **true**. |
| **title** |Отображаемое имя элемента Marketplace. По умолчанию установлено значение `Publisher-Offer-Sku` образа виртуальной машины. |
| **description** |Описание элемента Marketplace. |
| **расположение** |Расположение, в которое необходимо опубликовать образ виртуальной машины. По умолчанию установлено значение **локального** расположения.|
| **osDiskBlobURI** |При необходимости скрипт также принимает универсальный код ресурса (URI) хранилища BLOB-объектов для `osDisk`. |
| **dataDiskBlobURIs** |При необходимости этот скрипт также принимает массив универсальных кодов ресурсов (URI) хранилища BLOB-объектов для добавления дисков с данными в образ. |

## <a name="add-a-vm-image-through-the-portal"></a>Добавление образа виртуальной машины через портал

> [!NOTE]
> С помощью этого метода необходимо создать элемент Marketplace отдельно.

Нужно предоставить возможность ссылаться на образы в URI хранилища BLOB-объектов. Подготовьте образ операционной системы Windows или Linux в формате VHD (не VHDX), а затем отправьте образ в учетную запись хранения в Azure или Azure Stack. Если образ уже отправлен в хранилище BLOB-объектов в Azure или Azure Stack, шаг 1 можно пропустить.

1. [Отправьте образ виртуальной машины Windows в Azure для развертываний Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) или следуйте инструкциям в статье [Добавление образов Linux в Azure Stack](azure-stack-linux.md) для образов Linux. Перед отправкой образа необходимо принять во внимание следующие факторы:

   * Azure Stack поддерживает диски фиксированного размера в формате VHD. Фиксированный формат структурирует логический диск в файле линейно, то есть смещение диска X хранится в смещении BLOB-объекта X. Небольшая сноска в конце BLOB-объекта описывает свойства VHD-файла. Чтобы проверить, фиксированного ли размера ваш диск, используйте команду [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) в PowerShell.  

    > [!IMPORTANT]
   >  Azure Stack не поддерживает динамические диски VHD. Изменение размера динамического диска, подключенного к виртуальной машине, приведет к неисправному состоянию виртуальной машины. Чтобы устранить эту проблему, удалите виртуальную машину, не удаляя ее диск — большой двоичный объект VHD в учетной записи хранения. Затем преобразуйте VHD из динамического диска в диск с фиксированным размером и повторно создайте виртуальную машину.

   * Образ эффективнее отправлять в хранилище BLOB-объектов Azure Stack, чем в хранилище BLOB-объектов Azure, так как для размещения образа в репозитории образов Azure Stack требуется меньше времени.

   * При отправке [образов виртуальной машины Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) не забудьте заменить шаг **Вход в Azure** шагом [Настройка среды PowerShell для оператора Azure Stack](azure-stack-powershell-configure-admin.md).  

   * Запишите URI хранилища BLOB-объектов, в которое передается образ. URI хранилища BLOB-объектов имеет следующий формат: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*.vhd.

   * Чтобы настроить для большого двоичного объекта анонимный доступ, перейдите в контейнер больших двоичных объектов учетной записи хранения, в который был отправлен VHD образа виртуальной машины. Выберите **Большой двоичный объект**, а затем — **Политика доступа**. Если требуется, можно создать подписанный URL-адрес для контейнера и включить его как часть URI большого двоичного объекта.

   ![Переход к большим двоичным объектам учетной записи хранения](./media/azure-stack-add-vm-image/image1.png)

   ![Установка общего доступа к большим двоичным объектам](./media/azure-stack-add-vm-image/image2.png)

2. Войдите в Azure Stack как оператор. В меню выберите **Больше служб** > **Поставщики ресурсов**. Выберите **Среда выполнения приложений** > **Образы виртуальных машин** > **Добавить**.

3. В колонке **Add a VM Image** (Добавление образа виртуальной машины) введите издателя, предложение, номер SKU и версию образа виртуальной машины. Эти сегменты имен ссылаются на образ виртуальной машины в шаблонах Resource Manager. Выберите правильное значение для параметра **osType**. В поле **OS Disk Blob URI** (URI большого двоичного объекта диска ОС) введите URI большого двоичного объекта, куда был отправлен образ, и нажмите кнопку **Создать**, чтобы создать образ виртуальной машины.

   ![Начало создания образа](./media/azure-stack-add-vm-image/image4.png)

   При успешном создании образа состояние образа виртуальной машины изменяется на **Успешно**.

4. Чтобы сделать образ виртуальной машины более доступным для потребления пользователем в пользовательском интерфейсе, лучше всего [создать элемент Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Дополнительная информация

[Подготовка виртуальной машины](azure-stack-provision-vm.md)
