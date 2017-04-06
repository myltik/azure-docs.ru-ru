---
title: "Миграция виртуальных машин в Resource Manager с помощью интерфейса командной строки Azure | Документация Майкрософт"
description: "В этой статье последовательно описывается поддерживаемый платформой перенос ресурсов из классической модели в модель Azure Resource Manager с помощью Azure CLI."
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 5152367ec4a2ef225f08f2b8d4cb2e92eea8ce26
ms.lasthandoff: 03/22/2017


---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Перенос ресурсов IaaS из классического развертывания в развертывание с помощью Azure Resource Manager с использованием Azure CLI
Ниже последовательно описано, как использовать команды интерфейса командной строки Azure (CLI) для переноса ресурсов IaaS из классической модели развертывания в модель развертывания с помощью Azure Resource Manager. Для выполнения инструкций в этой статье требуется [Azure CLI](../cli-install-nodejs.md).

> [!NOTE]
> Все операции, описанные здесь, являются идемпотентными. Если вы столкнетесь с какой-либо проблемой, не связанной с неподдерживаемой функцией или ошибкой конфигурации, мы рекомендуем повторить подготовку, прервать или зафиксировать текущую операцию. Платформа повторит попытку.
> 
> 

<br>
Ниже приведена блок-схема с последовательностью действий во время переноса.

![Screenshot that shows the migration steps](./media/virtual-machines-windows-migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Шаг 1. Подготовка к переносу
Ниже приведено несколько рекомендаций для оценки переноса ресурсов IaaS из классической модели в модель Resource Manager.

* Прочитайте [список неподдерживаемых конфигураций и компонентов](virtual-machines-windows-migration-classic-resource-manager.md). Если у вас есть виртуальные машины, которые используют неподдерживаемые конфигурации или компоненты, мы рекомендуем отложить перенос до того момента, пока не будет заявлено об их поддержке. Также вы можете удалить такую функцию или вынести ее за пределы конфигурации, чтобы выполнить перенос.
* Если у вас есть текущие автоматизированные сценарии, которые развертывают инфраструктуру и приложения, попробуйте создать аналогичную программу установки для миграции с помощью этих сценариев. Вы можете также настроить примеры среды с помощью портала Azure.

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Шаг 2. Настройка подписки и регистрация поставщика
Для сценариев миграции следует настроить среду для классической модели и модели Resource Manager. [Установите интерфейс командной строки Azure](../cli-install-nodejs.md) (Azure CLI) и [выберите подписку](../xplat-cli-connect.md).

Выполните вход со своей учетной записью.

    azure login

Выберите подписку Azure с помощью следующей команды.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> Регистрация — однократное действие, но, прежде чем выполнять миграцию, вам нужно зарегистрироваться. Если вы не зарегистрируетесь, отобразится такое сообщение об ошибке: 
> 
> *Неправильный запрос: Подписка не зарегистрирована для миграции.* 
> 
> 

Зарегистрируйтесь в поставщике ресурсов миграции с помощью такой команды: Обратите внимание, что в некоторых случаях время ожидания этой команды истекает. Однако регистрация будет успешной.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Подождите пять минут для завершения регистрации. Состояние утверждения регистрации можно проверить, выполнив следующую команду. Убедитесь, что RegistrationState имеет значение `Registered` , прежде чем продолжить.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Теперь переключите интерфейс командной строки в режим `asm`.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Шаг 3. Проверка наличия достаточного числа ядер виртуальной машины Azure Resource Manager в регионе Azure текущего развертывания или виртуальной сети
Для этого шага необходимо будет переключиться в режим `arm`. Для этого воспользуйтесь следующей командой.

```
azure config mode arm
```

Чтобы проверить текущее количество ядер в Azure Resource Manager, можно использовать приведенную ниже команду интерфейса командной строки. Чтобы узнать больше о квотах ядер, ознакомьтесь с разделом [Ограничения и диспетчер ресурсов Azure](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

После завершения проверки на этом шаге можно переключиться обратно в режим `asm` .

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Шаг 4. Вариант 1: миграция виртуальных машин в облачной службе
Получите список облачных служб, выполнив следующую команду, а затем выберите облачную службу для переноса. Обратите внимание: если виртуальные машины в облачной службе размещены в виртуальной сети или им назначены веб-роли или рабочие роли, вы получите сообщение об ошибке.

    azure service list

Выполните следующую команду, чтобы получить имя развертывания для облачной службы из подробных выходных данных. В большинстве случаев имя развертывания совпадает с именем облачной службы.

    azure service show <serviceName> -vv

Подготовьте к переносу виртуальные машины в облачной службе. Возможно два варианта.

Если вы хотите перенести виртуальные машины в виртуальную сеть, созданную платформой, используйте следующую команду.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Если вы хотите перенести их в существующую виртуальную сеть в модели развертывания с помощью Resource Manager, используйте следующую команду.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Когда операция подготовки успешно завершится, вы сможете просмотреть подробные выходные данные о состоянии миграции виртуальных машин, чтобы убедиться, что все они находятся в состоянии `Prepared` .

    azure vm show <vmName> -vv

Проверьте конфигурацию для подготовленных ресурсов с помощью интерфейса командной строки или портала Azure. Если вы не готовы к миграции и хотите вернуть предыдущее состояние, используйте следующую команду.

    azure service deployment abort-migration <serviceName> <deploymentName>

Если подготовленная конфигурация вас устраивает, можете продолжать процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Шаг 4. Вариант 2: миграция виртуальных машин в виртуальной сети
Выберите виртуальную сеть, в которую будете переносить ресурсы. Обратите внимание: если в виртуальной сети есть виртуальные машины, веб-роли или рабочие роли с неподдерживаемыми конфигурациями, вы получите сообщение об ошибке проверки.

Выполните следующую команду, чтобы получить все виртуальные сети в подписке.

    azure network vnet list

Результат должен выглядеть следующим образом.

![Снимок экрана командной строки с выделенным полным именем виртуальной сети.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

В приведенном выше примере **virtualNetworkName** представляет собой полное имя **Group classicubuntu16 classicubuntu16**.

Подготовьте выбранную виртуальную сеть к переносу, используя следующую команду.

    azure network vnet prepare-migration <virtualNetworkName>

Проверьте конфигурацию для подготовленных виртуальных машин с помощью интерфейса командной строки или портала Azure. Если вы не готовы к миграции и хотите вернуть предыдущее состояние, используйте следующую команду.

    azure network vnet abort-migration <virtualNetworkName>

Если подготовленная конфигурация вас устраивает, можете продолжать процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Шаг 5. Перенос учетной записи хранения
После переноса виртуальных машин рекомендуется перенести учетную запись хранения.

Подготовьте учетную запись хранения к переносу, используя следующую команду:

    azure storage account prepare-migration <storageAccountName>

Проверьте конфигурацию для подготовленной учетной записи с помощью интерфейса командной строки или портала Azure. Если вы не готовы к миграции и хотите вернуть предыдущее состояние, используйте следующую команду.

    azure storage account abort-migration <storageAccountName>

Если подготовленная конфигурация вас устраивает, можете продолжать процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Дальнейшие действия
* [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
* [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)


