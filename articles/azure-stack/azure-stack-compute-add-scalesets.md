---
title: Обеспечение доступности масштабируемых наборов виртуальных машин в Azure Stack | Документация Майкрософт
description: Сведения о том, как оператор облака может добавить масштабируемый набор виртуальных машин в Azure Stack Marketplace
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.topic: article
ms.date: 04/06/2018
ms.author: brenduns
ms.reviewer: anajod
keywords: ''
ms.openlocfilehash: cdabd2a9d336cdd8ac83d27460fe129c45b7e1c6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Обеспечение доступности масштабируемых наборов виртуальных машин в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Масштабируемые наборы виртуальных машин представляют собой вычислительный ресурс Azure Stack. С их помощью можно развернуть набор идентичных виртуальных машин и управлять им. При одинаковой настройке всех виртуальных машин масштабируемые наборы не требуют их предварительной подготовки. Это упрощает создание крупномасштабных служб, предназначенных для больших вычислений, больших данных и контейнерных рабочих нагрузок.

В этой статье приводятся пошаговые инструкции для обеспечения доступности масштабируемых наборов в Azure Stack Marketplace. После завершения этой процедуры пользователи смогут добавлять масштабируемые наборы виртуальных машин в свои подписки.

Масштабируемые наборы виртуальных машин в Azure Stack подобны масштабируемым наборам виртуальных машин в Azure. Дополнительные сведения см. в следующих видеороликах.
* [Марк Руссинович (Mark Russinovich) рассказывает о масштабируемых наборах Azure.](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Масштабируемые наборы виртуальных машин с Гаем Бауэрманом (Guy Bowerman).](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Azure Stack не поддерживает автоматическое масштабирование для масштабируемых наборов виртуальных машин. Добавить дополнительные экземпляры в масштабируемый набор можно с помощью портала Azure Stack, шаблонов Resource Manager или PowerShell.

## <a name="prerequisites"></a>предварительным требованиям
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

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Обновление образов в масштабируемом наборе виртуальных машин 
После создания масштабируемого набора виртуальных машин пользователи смогут обновлять образы в этом наборе, не создавая набор масштабирования заново. Процесс обновления образа будет разным в следующих сценариях.

1. В шаблоне развертывания для масштабируемого набора виртуальных машин указано значение **latest** для параметра *version*.  

   Если параметр *version* в разделе шаблона *imageReference* для масштабируемого набора имеет значение **latest**, при увеличении масштаба в масштабируемом наборе всегда применяется последняя из доступных версий. Кода увеличение масштаба в наборе завершится, вы можете удалить старые экземпляры из масштабируемого набора виртуальных машин.  (Значения параметров *publisher*, *offer* и *sku* сохраняются неизменными.) 

   В следующим примере показан выбор параметра *latest*.  

          "imageReference": {
             "publisher": "[parameters('osImagePublisher')]",
             "offer": "[parameters('osImageOffer')]",
             "sku": "[parameters('osImageSku')]",
             "version": "latest"
             }

   Чтобы новый образ применялся при увеличении масштаба, его необходимо скачать.  

   - Если в Marketplace доступен образ более поздней версии, чем тот, который используется в масштабируемом наборе, новый образ скачивается и заменяет собой старый образ. После замены образа пользователь может продолжить увеличение масштаба. 

   - Если версия образа в Marketplace совпадает с той, которая используется в масштабируемом наборе, удалите образ более ранней версии и замените его новым образом. В период между удалением исходного образа и завершением скачивания нового операции увеличения масштаба недоступны. 
      
     Такой процесс применяется для повторного объединения образов в формате разреженного файла, который появился в версии 1803. 
 

2. В шаблоне масштабируемого набора виртуальных машин для параметра *version* указано не значение **latest**, а конкретный номер версии.  

     Если вы скачаете образ более поздней версии (с изменением номера доступной версии), увеличение масштаба для этого масштабируемого набора становится невозможным. Это сделано намеренно, поскольку указанная в шаблоне версия образа должна быть всегда доступна.  

Дополнительные сведения см. в разделе [Диски и образы операционной системы](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images).  


## <a name="remove-a-virtual-machine-scale-set"></a>Удаление масштабируемого набора виртуальных машин

Чтобы удалить из коллекции элемент масштабируемого набора виртуальных машин, выполните следующую команду PowerShell:

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> Возможно, элемент коллекции не будет удален сразу же. Возможно, вам потребуется обновить портал несколько раз, прежде чем элемент будет отображаться как удаленный из Marketplace.


## <a name="next-steps"></a>Дополнительная информация
[Часто задаваемые вопросы об Azure Stack](azure-stack-faq.md)

