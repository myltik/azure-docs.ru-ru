---
title: 'Шифрование дисков Azure: часто задаваемые вопросы | Документация Майкрософт'
description: В этой статье приведены часто задаваемые вопросы о шифровании дисков Microsoft Azure для виртуальных машин IaaS под управлением Windows и Linux.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/18/2018
ms.author: devtiw
ms.openlocfilehash: cb523b4fbf6e8abdf5c5158ab041d3485add9b23
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-disk-encryption-faq"></a>Шифрование дисков Azure: часто задаваемые вопросы

В этой статье приведены часто задаваемые вопросы о шифровании дисков Azure для виртуальных машин IaaS под управлением Windows и Linux. Дополнительные сведения см. в статье [Дисковое шифрование Azure для виртуальных машин IaaS под управлением Windows и Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>В каких регионах предоставляется общедоступная версия шифрования дисков Azure?

Шифрование дисков Azure для виртуальных машин IaaS под управлением Windows и Linux предоставляется в режиме общедоступной версии во всех общедоступных регионах Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Какие возможности предоставляет шифрование дисков Azure?

Шифрование дисков Azure (общедоступная версия) поддерживает шаблоны Azure Resource Manager, Azure PowerShell и Azure CLI. Это обеспечивает большую гибкость. Шифрование дисков на виртуальных машинах IaaS можно включить тремя разными способами. Дополнительные сведения о возможностях для пользователей и пошаговые инструкции по работе с шифрованием дисков Azure доступны в [сценариях и примерах развертывания шифрования дисков Azure](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Какова стоимость шифрования дисков Azure?

Плата за шифрование дисков Azure для виртуальных машин не взимается.

## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Какие уровни виртуальных машин поддерживает шифрование дисков Azure?

Шифрование дисков Azure доступно на виртуальных машинах уровня "Стандартный", включая виртуальные машины IaaS серии [A, D, DS, G, GS и F](https://azure.microsoft.com/pricing/details/virtual-machines/). Эта возможность также доступна для виртуальных машин с хранилищем класса Рremium. На виртуальных машинах уровня "Базовый" она недоступна.

## <a name="what-linux-distributions-does-azure-disk-encryption-support"></a>Какие дистрибутивы Linux поддерживает шифрование дисков Azure?

Шифрование дисков Azure поддерживается для следующих дистрибутивов и версий серверов Linux:

| Дистрибутив Linux | Version (версия) | Тип тома, для которого поддерживается шифрование|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | Диск операционной системы и данных |
| Ubuntu | 14.04.5-DAILY-LTS | Диск операционной системы и данных |
| RHEL | 7.4 | Диск данных* |
| RHEL | 7.3 | Диск данных* |
| RHEL | 7,2 | Диск данных* |
| RHEL | 6,8 | Диск данных* |
| RHEL | 6.7 | Диск данных* |
| CentOS | 7.3 | Диск операционной системы и данных |
| CentOS | 7.2n | Диск операционной системы и данных |
| CentOS | 6,8 | Диск операционной системы и данных |
| CentOS | 7.1. | Диск данных |
| CentOS | 7.0 | Диск данных |
| CentOS | 6.7 | Диск данных |
| CentOS | 6.6 | Диск данных |
| CentOS | 6,5 | Диск данных |
| openSUSE | 13.2 | Диск данных |
| SLES | 12 с пакетом обновления 1 (SP1) | Диск данных |
| SLES | Приоритет: 12-SP1 | Диск данных |
| SLES | HPC 12 | Диск данных |
| SLES | Приоритет: 11-SP4 | Диск данных |
| SLES | 11 SP4 | Диск данных |

*__ADE поддерживается для RHEL для дисков данных. Текущая реализация ADE будет работать для дисков ОС, но сейчас совместная поддержка не предоставляется. Корпорация Майкрософт и Red Hat работают над решением с совместной поддержкой. Тем временем вы можете ознакомиться с технической документацией ADE по шифрованию диска операционной системы Linux [здесь](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption).__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Как приступить к работе с шифрованием дисков Azure?

Чтобы приступить к работе, прочтите технический документ [Дисковое шифрование Azure для виртуальных машин IaaS под управлением Windows и Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Можно ли с помощью шифрования дисков Azure зашифровать загрузочные тома и тома данных?

Да, на виртуальных машинах IaaS под управлением Windows и Linux можно зашифровать как загрузочные тома, так и тома данных. Чтобы зашифровать данные на виртуальных машинах под управлением Windows, необходимо сначала зашифровать том операционной системы. На виртуальных машинах Linux можно зашифровать том данных без предварительного шифрования тома операционной системы. После шифрования тома операционной системы виртуальной машины IaaS под управлением Linux шифрование невозможно отключить.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok-capability"></a>Дает ли шифрование дисков Azure возможность создания собственных ключей (BYOK)?

Да, вы можете предоставить собственные ключи шифрования ключей. Эти ключи хранятся в Azure Key Vault, что представляет собой хранилище ключей для шифрования дисков Azure. Дополнительные сведения о сценариях поддержки ключа шифрования ключей см. в [сценариях и примерах развертывания шифрования дисков Azure](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Можно ли использовать ключ шифрования ключей, созданный в Azure?

Да, можно использовать Azure Key Vault, чтобы создать ключ шифрования ключей для использования шифрования дисков Azure. Эти ключи хранятся в Azure Key Vault, что представляет собой хранилище ключей для шифрования дисков Azure. Дополнительные сведения о сценариях поддержки ключа шифрования ключей см. в [сценариях и примерах развертывания шифрования дисков Azure](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Можно ли использовать локальную службу управления ключами или HSM для защиты ключей шифрования?

Использовать локальную службу управления ключами или HSM для защиты ключей шифрования с шифрованием дисков Azure нельзя. Для защиты ключей шифрования можно использовать только Azure Key Vault. Дополнительные сведения о сценариях поддержки ключа шифрования ключей см. в [сценариях и примерах развертывания шифрования дисков Azure](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Каковы предварительные требования для настройки шифрования дисков Azure?

Предварительным требованием является наличие скрипта PowerShell. С помощью этого скрипта можно создать приложение Azure Active Directory, создать хранилище ключей или установить имеющееся для доступа к шифрованию диска, чтобы включить шифрование и обеспечить защиту секретов и ключей. Дополнительные сведения о сценариях поддержки ключа шифрования ключей см. в [сценариях и примерах развертывания и предварительных требованиях шифрования дисков Azure](azure-security-disk-encryption.md#prerequisites).

## <a name="where-can-i-get-more-information-on-how-to-use-powershell-for-configuring-azure-disk-encryption"></a>Где можно получить дополнительные сведения о том, как использовать PowerShell для настройки шифрования дисков Azure?

У нас есть несколько информативных статей, в которых описывается, как выполнять основные задачи шифрования дисков Azure, а также более сложные сценарии. Описание основных задач см. в записи [Explore Azure Disk Encryption with Azure Powershell](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/) (Изучение возможностей дискового шифрования Azure с помощью Azure PowerShell). Описание более сложных сценариев см. в записи блога [Explore Azure Disk Encryption with Azure PowerShell – Part 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/) (Изучение возможностей дискового шифрования Azure с помощью Azure PowerShell — часть 2).

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Какую версию Azure PowerShell поддерживает шифрование дисков Azure?

Для настройки шифрования дисков Azure используйте последнюю версию пакета SDK для Azure PowerShell. Скачайте последнюю версию [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Шифрование дисков Azure *не* поддерживается в пакете SDK для Azure версии 1.1.0.

> [!NOTE]
> Расширение предварительной версии шифрования дисков Azure для Linux считается устаревшим. Дополнительные сведения см. в записи блога [Deprecating Azure disk encryption preview extension for Linux IaaS VMs](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/) (Объявлено неподдерживаемым расширение предварительной версии шифрования дисков Azure для виртуальных машин IaaS Linux).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Можно ли применять шифрование дисков Azure в пользовательском образе Linux?

Шифрование дисков Azure применить в пользовательском образе Linux нельзя. Мы поддерживаем только образы Linux из коллекции поддерживаемых дистрибутивов, приведенных выше. Пользовательские образы Linux сейчас не поддерживаются.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Можно ли применить обновления к виртуальной машине Linux Red Hat с использованием команды yum update?

Да, можно выполнить обновление или исправление для виртуальной машины Red Hat Linux. Дополнительные сведения см. в записи блога [Applying updates to a encrypted Azure IaaS Red Hat VM using Yum Update](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/) (Применение обновлений к зашифрованной виртуальной машине Azure IaaS Red Hat, использующей обновление Yum).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Какие действия по шифрованию дисков Azure рекомендуется использовать для Linux?

Для достижения наилучших результатов в Linux рекомендуется такая последовательность действий:
* Для начала возьмите неизмененный готовый образ из коллекции, соответствующий требуемому дистрибутиву и версии операционной системы.
* Создайте резервные копии всех подключенных дисков, которые будут зашифрованы.  Это позволит выполнить восстановление в случае сбоя, например при перезагрузке виртуальной машины до завершения шифрования.
* Выполните шифрование (этот процесс может занять несколько часов или даже дней в зависимости от характеристик виртуальной машины и размера всех подключенных дисков данных).
* При необходимости настройте образ и добавьте к нему программное обеспечение.

Если эти действия невозможно выполнить, в качестве альтернативы шифрованию всего диска с помощью dm-crypt можно воспользоваться [шифрованием службы хранилища](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE) на уровне учетной записи хранения платформы.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>Что такое том BEK или /mnt/azure_bek_disk?

Том Bek для Windows или /mnt/azure_bek_disk для Linux — это том локальных данных, который надежно хранит ключи шифрования для зашифрованных виртуальных машин IaaS Azure.
> [!NOTE]
> Не удаляйте и не изменяйте содержимое на этом диске. Не отключайте диск, так как ключ шифрования необходим для любой операции шифрования на виртуальной машине IaaS.

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Где можно задать вопрос или оставить отзыв?

Задать вопрос или оставить отзыв можно на [форуме по шифрованию дисков Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Дополнительная информация
Из этого документа вы получили ответы на самые распространенные вопросы, связанные с шифрованием дисков Azure. Дополнительные сведения об этой службе и ее возможностях см. в статьях:

- [Шифрование диска в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Шифрование виртуальной машины Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Шифрование неактивных данных в Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)