---
title: Миграция виртуальных машин в Resource Manager с помощью интерфейса командной строки Azure | Документация Майкрософт
description: В этой статье последовательно описывается поддерживаемый платформой перенос ресурсов из классической модели в модель Azure Resource Manager с помощью Azure CLI.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: f986246e74305789eb2978a95fd6a3e51accd25e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30909757"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Перенос ресурсов IaaS из классического развертывания в развертывание с помощью Azure Resource Manager с использованием Azure CLI
Ниже последовательно описано, как использовать команды интерфейса командной строки Azure (CLI) для переноса ресурсов IaaS из классической модели развертывания в модель развертывания с помощью Azure Resource Manager. Для выполнения инструкций в этой статье требуется [Azure CLI 1.0](../../cli-install-nodejs.md). Так как интерфейс Azure CLI 2.0 применим только для ресурсов Azure Resource Manager, его нельзя использовать для этой миграции.

> [!NOTE]
> Все операции, описанные здесь, являются идемпотентными. Если вы столкнетесь с какой-либо проблемой, не связанной с неподдерживаемой функцией или ошибкой конфигурации, мы рекомендуем повторить подготовку, прервать или зафиксировать текущую операцию. Платформа повторит попытку.
> 
> 

<br>
Ниже приведена блок-схема с последовательностью действий во время переноса.

![Screenshot that shows the migration steps](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Шаг 1. Подготовка к переносу
Ниже приведено несколько рекомендаций для оценки переноса ресурсов IaaS из классической модели в модель Resource Manager.

* Прочитайте [список неподдерживаемых конфигураций и компонентов](../windows/migration-classic-resource-manager-overview.md). Если у вас есть виртуальные машины, которые используют неподдерживаемые конфигурации или компоненты, мы рекомендуем отложить перенос до того момента, пока не будет заявлено об их поддержке. Также вы можете удалить такую функцию или вынести ее за пределы конфигурации, чтобы выполнить перенос.
* Если у вас есть текущие автоматизированные сценарии, которые развертывают инфраструктуру и приложения, попробуйте создать аналогичную программу установки для миграции с помощью этих сценариев. Вы можете также настроить примеры среды с помощью портала Azure.

> [!IMPORTANT]
> В настоящее время не поддерживается перенос шлюзов приложений из классической модели в модель Resource Manager. Чтобы перенести классическую виртуальную сеть со шлюзом приложений, удалите этот шлюз перед выполнением операции подготовки для перемещения сети. После завершения переноса повторно подключите шлюз в Azure Resource Manager. 
>
>Шлюзы ExpressRoute, подключенные к каналам ExpressRoute в другой подписке, перенести автоматически невозможно. В таких случаях удалите шлюз ExpressRoute, перенесите виртуальную сеть и создайте шлюз заново. Дополнительные сведения см. в статье [Перенос каналов ExpressRoute и связанных виртуальных сетей из классической модели развертывания на модель Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Шаг 2. Настройка подписки и регистрация поставщика
Для сценариев миграции следует настроить среду для классической модели и модели Resource Manager. [Установите интерфейс командной строки Azure](../../cli-install-nodejs.md) (Azure CLI) и [выберите подписку](/cli/azure/authenticate-azure-cli).

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

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Шаг 3. Проверка наличия достаточного числа виртуальных ЦП для виртуальных машин Azure, развертываемых с помощью Resource Manager, в регионе Azure, в котором находится текущее развертывание или виртуальная сеть
Для этого шага необходимо будет переключиться в режим `arm`. Для этого воспользуйтесь следующей командой.

```
azure config mode arm
```

Чтобы проверить текущее количество виртуальных ЦП в Azure Resource Manager, используйте приведенную ниже команду CLI. Чтобы узнать больше о квотах на виртуальные ЦП, см. соответствующий раздел статьи [Подписка Azure, границы, квоты и ограничения службы](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

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

Во-первых, проверьте возможность переноса облачной службы с помощью следующей команды.

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

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

![Снимок экрана командной строки с выделенным полным именем виртуальной сети.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

В приведенном выше примере **virtualNetworkName** представляет собой полное имя **Group classicubuntu16 classicubuntu16**.

Во-первых, проверьте возможность переноса виртуальной сети с помощью следующей команды.

```shell
azure network vnet validate-migration <virtualNetworkName>
```

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

## <a name="next-steps"></a>Дополнительная информация

* [Обзор поддерживаемого платформой переноса ресурсов IaaS из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Планирование переноса ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community tools to migrate IaaS resources from classic to Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Инструменты сообщества для перемещения ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Распространенные ошибки миграции](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Часто задаваемые вопросы о миграции из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
