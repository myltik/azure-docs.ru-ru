---
title: "Применение Azure CLI для пользователей Azure Stack | Документация Майкрософт"
description: "Узнайте, как использовать межплатформенный интерфейс командной строки (CLI) для развертывания ресурсов и управления ими в Azure Stack"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: d184bb9edbe2542d7321d8b9ccc5d23f2401f8d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Применение Azure CLI для пользователей Azure Stack

*Область применения: интегрированные системы Azure Stack и пакет SDK для Azure Stack*

Задачи оператора Azure Stack не предусмотрены для выполнения при помощи CLI. Но чтобы пользователи могли использовать CLI для управления ресурсами, оператор Azure Stack должен предоставить следующие артефакты:

* **Корневой сертификат ЦС Azure Stack.** Он нужен в том случае, если CLI используется на рабочей станции без пакета SDK для Azure Stack.  

* **Конечная точка псевдонимов виртуальных машин.** Она предоставляет псевдонимы, например "UbuntuLTS" или "Win2012Datacenter", которые позволяют при развертывании виртуальной машины в одном параметре указать нужного издателя образа, предложение, номер SKU и версию.  

В разделах ниже описано, как получить эти значения.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Экспорт корневого сертификата ЦС Azure Stack

Корневой сертификат ЦС Azure Stack входит в состав пакета SDK и размещается на виртуальной машине клиента, где выполняется среда пакета разработки. Войдите в пакет разработки или на виртуальную машину клиента и выполните скрипт ниже, чтобы экспортировать корневой сертификат Azure Stack в PEM-формате.

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Cerficate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Настройка конечной точки псевдонимов виртуальных машин

Оператору Azure Stack следует настроить общедоступную конечную точку с файлом псевдонимов виртуальных машин.  Файл псевдонимов виртуальных машин хранит общее имя для образа в JSON-формате. Имя можно указать в соответствующем параметре команды Azure CLI при развертывании виртуальной машины.  

Прежде чем добавить запись в файл псевдонимов, необходимо [скачать образ из marketplace]((azure-stack-download-azure-marketplace-item.md) или [опубликовать собственный пользовательский образ](azure-stack-add-vm-image.md).  Когда вы публикуете пользовательский образ, запишите сведения об издателе, предложении, номере SKU и версии, которые указываете во время публикации.  Если используется образ из marketplace, эти сведения можно получить с помощью командлета ```Get-AzureVMImage```.  
   
В качестве отправной точки можно использовать предлагаемый [образец файла псевдонимов](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) с несколькими широко используемыми псевдонимами образов.  Этот файл следует располагать так, чтобы все клиенты, использующие CLI, имели к нему доступ.  Например, его можно поместить в учетную запись хранилища BLOB-объектов, а пользователям предоставить соответствующий URL-адрес:

1.  Скачайте [образец файла](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) с сайта GitHub.
2.  Создайте учетную записи хранения в Azure Stack.  После этого создайте новый контейнер BLOB-объектов.  Установите для политики доступа значение public.  
3.  Отправьте JSON-файл в только что созданный контейнер.  Теперь щелкните имя BLOB-объекта и выберите URL-адрес в разделе его свойств.


## <a name="next-steps"></a>Дальнейшие действия

[Развертывание шаблонов с помощью интерфейса командной строки Azure](azure-stack-deploy-template-command-line.md)

[Подключение с помощью PowerShell](azure-stack-connect-powershell.md)

[Управление разрешениями пользователей](azure-stack-manage-permissions.md)

