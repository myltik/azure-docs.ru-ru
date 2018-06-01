---
title: Примеры шаблонов Azure Resource Manager для службы "Экземпляры контейнеров Azure"
description: Примеры шаблонов Azure Resource Manager для службы "Экземпляры контейнеров Azure"
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/17/2018
ms.author: marsma
ms.openlocfilehash: fcc2e6c52e773d95bcdfe43d881fce036fae6513
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259744"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Шаблоны Azure Resource Manager для службы "Экземпляры контейнеров Azure"

Следующие примеры шаблонов развертывают экземпляры контейнеров в различных конфигурациях.

Варианты развертывания см. в разделе [Развертывание](#deployment). Если вы хотите создавать свои шаблоны, см. сведения о формате шаблона и доступных свойствах в [справочнике по шаблону Resource Manager][ref].

## <a name="sample-templates"></a>Примеры шаблонов

| | |
|-|-|
| **Приложения** ||
| [Wordpress][app-wp] | Создает веб-сайт WordPress и его базу данных MySQL в экземпляре контейнера. Содержимое сайта WordPress и база данных MySQL сохраняются в общей папке Azure. |
| [MS NAV с SQL Server и IIS][app-nav] | Развертывает один контейнер Windows с полнофункциональной автономной средой Dynamics NAV или Dynamics 365 Business Central. |
| **Тома** ||
| [emptyDir][vol-emptydir] | Развертывает два контейнера Linux, совместно использующих том emptyDir. |
| [gitRepo][vol-gitrepo] | Развертывает контейнер Linux, который клонирует репозиторий GitHub и подключает его в качестве тома. |
| [secret][vol-secret] | Развертывает контейнер Linux с использованием сертификата PFX, подключенного в качестве секретного тома. |
| **Сеть** ||
| [Контейнер с доступом к UDP][net-udp] | Развертывает контейнер Windows или Linux, который обеспечивает доступ к UDP-порту. |
| [Контейнер Linux с общедоступным IP-адресом][net-publicip] | Развертывает один контейнер Linux, доступный через общедоступный IP-адрес. |
| **Ресурсы Azure** ||
| [Создание учетной записи хранения Azure и общей папки][az-files] | Создает учетную запись хранения и общую папку Azure в экземпляре контейнера с помощью Azure CLI.

## <a name="deployment"></a>Развертывание

Для развертывания ресурсов с помощью шаблонов Resource Manager доступны несколько вариантов:

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Портал Azure][deploy-portal]

[REST API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups