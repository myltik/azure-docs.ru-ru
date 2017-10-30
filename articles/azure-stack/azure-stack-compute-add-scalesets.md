---
title: "Обеспечение доступности масштабируемых наборов виртуальных машин в Azure Stack"
description: "Сведения о том, как администратор облака может добавить масштабируемый набор виртуальных машин в Azure Stack Marketplace"
services: azure-stack
author: anjayajodha
ms.service: azure-stack
ms.topic: article
ms.date: 9/25/2017
ms.author: anajod
keywords: 
ms.openlocfilehash: 31aeb963bdf4fd32712bc6f29f64060ec1c77cb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Обеспечение доступности масштабируемых наборов виртуальных машин в Azure Stack

*Область применения: интегрированные системы Azure Stack и пакет средств разработки Azure Stack*

Масштабируемые наборы виртуальных машин представляют собой вычислительный ресурс Azure Stack. С их помощью можно развернуть набор идентичных виртуальных машин и управлять им. При одинаковой настройке всех виртуальных машин масштабируемые наборы не требуют их предварительной подготовки. Это упрощает создание крупномасштабных служб, предназначенных для больших вычислений, больших данных и контейнерных рабочих нагрузок.

В этой статье приводятся пошаговые инструкции для обеспечения доступности масштабируемых наборов в Azure Stack Marketplace. После завершения этой процедуры пользователи смогут добавлять масштабируемые наборы виртуальных машин в свои подписки.

Масштабируемые наборы виртуальных машин в Azure Stack подобны масштабируемым наборам виртуальных машин в Azure. Дополнительные сведения см. в следующих видеороликах.
* [Марк Руссинович (Mark Russinovich) рассказывает о масштабируемых наборах Azure.](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Масштабируемые наборы виртуальных машин с Гаем Бауэрманом (Guy Bowerman).](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

В Azure Stack масштабируемые наборы виртуальных машин не поддерживают автоматическое масштабирование. Добавить дополнительные экземпляры в масштабируемый набор можно с помощью портала Azure Stack, шаблонов Resource Manager или PowerShell.

## <a name="prerequisites"></a>Предварительные требования
* **PowerShell и средства**

   Установите и настройте PowerShell для Azure Stack и средства Azure Stack. См. статью [Начало работы с PowerShell в Azure Stack](azure-stack-powershell-configure-quickstart.md).

   После установки средств Azure Stack не забудьте импортировать следующий модуль PowerShell (путь относительно папки .\ComputeAdmin в папке AzureStack-Tools-master):

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **Образ операционной системы**

   Если образ операционной системы еще не добавлен в Azure Stack Marketplace, см. статью [Добавление образа виртуальной машины Windows Server 2016 в Azure Stack Marketplace](azure-stack-add-default-image.md).

   Для поддержки Linux скачайте Ubuntu Server 16.04 и добавьте его с помощью ```Add-AzsVMImage``` со следующими параметрами: ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```.

## <a name="add-the-virtual-machine-scale-set"></a>Добавление масштабируемого набора виртуальных машин

Отредактируйте следующий скрипт PowerShell для вашей среды, а затем запустите его, чтобы добавить масштабируемый набор виртуальных машин в Azure Stack Marketplace. 

``$User`` — учетная запись, используемая для подключения портала администрирования. Например, serviceadmin@contoso.onmicrosoft.com.

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds
Select-AzureRmProfile -Profile $AzsEnvContext

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="remove-a-virtual-machine-scale-set"></a>Удаление масштабируемого набора виртуальных машин

Чтобы удалить из коллекции элемент масштабируемого набора виртуальных машин, выполните следующую команду PowerShell:

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> Возможно, элемент коллекции не будет удален сразу же. Может потребоваться обновить портал несколько раз, прежде чем элемент будет удален из Marketplace.


## <a name="next-steps"></a>Дальнейшие действия
[Часто задаваемые вопросы об Azure Stack](azure-stack-faq.md)

