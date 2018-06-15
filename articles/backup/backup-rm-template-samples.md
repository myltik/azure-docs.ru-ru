---
title: Шаблоны Azure Resource Manager для Azure Backup
description: Примеры Azure Backup PowerShell
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 04/18/2018
ms.author: markgal
ms.custom: mvc
ms.openlocfilehash: 0aac49be397f5e1c86fa834d341399775fd71cfa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607078"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Шаблоны Azure Resource Manager для Azure Backup

В приведенной ниже таблице представлены ссылки на шаблоны Azure Resource Manager для хранилищ служб восстановления и функций Azure Backup.

|   |   |
|---|---|
|**Хранилище служб восстановления** | |
| [создание хранилища служб восстановления](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create);| Создайте хранилище служб восстановления, Хранилище можно использовать для Azure Backup и Azure Site Recovery. |
|**Настройка виртуальных машин**| |
| [Backup Resource Manager VMs to Recovery Services Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) (Резервное копирование виртуальных машин диспетчера ресурсов в хранилище служб восстановления) | Используйте имеющееся хранилище служб восстановления и политику Backup для резервного копирования виртуальных машин диспетчера ресурсов в той же группе ресурсов.|
| [Backup ARM and Classic IaaS VMs to Recovery Services Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) (Резервное копирование виртуальных машин ARM и классических виртуальных машин IaaS в хранилище служб восстановления) | Шаблон для резервного копирования классических виртуальных машин и виртуальных машин диспетчера ресурсов. |
| [Create Weekly Backup Policy for Recovery Services Vault to protect Azure IaaS VMs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) (Создание политики еженедельного резервного копирования для хранилища служб восстановления для защиты виртуальных машин Azure IaaS) | Шаблон создает хранилище служб восстановления и политику еженедельного резервного копирования, которая используется для резервного копирования классических виртуальных машин и виртуальных машин диспетчера ресурсов.|
| [Create Daily Backup Policy for Recovery Services Vault to protect Azure IaaS VMs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) (Создание политики ежедневного резервного копирования для хранилища служб восстановления для защиты виртуальных машин Azure IaaS) | Шаблон создает хранилище служб восстановления и политику ежедневного резервного копирования, которая используется для резервного копирования классических виртуальных машин и виртуальных машин диспетчера ресурсов.|
| [Create a simple windows VM and configure backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) (Создание простой виртуальной машины Windows и настройка резервного копирования) | Шаблон создает хранилище виртуальных машин и хранилищ Windows Server с включенной политикой резервного копирования по умолчанию.|
|**Мониторинг заданий службы архивации** |  |
| [OMS monitoring solution for Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) (Решение для мониторинга OMS Azure Backup) | Шаблон развертывает решение для мониторинга OMS Azure Backup, который позволяет отслеживать задания резервного копирования и восстановления, оповещения и облачное хранилище, используемое в хранилищах служб восстановления.|  
|   |   |

