<properties
   pageTitle="Инфраструктура обновления для образов Red Hat Enterprise Linux | Microsoft Azure"
   description="Обзор службы обновления yum для предоставляемого по запросу экземпляра Red Hat Enterprise Linux в Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="01/13/2016"
   ms.author="kyliel"/>

# Инфраструктура обновления для образов Red Hat Enterprise Linux

С помощью Red Hat Update Infrastructure (RHUI) в Azure вы можете управлять содержимым репозитория yum для образов Azure Red Hat Enterprise Linux (RHEL). Предоставляемым по запросу экземплярам RHEL, созданным из Azure Marketplace, будет открыт доступ к региональному репозиторию yum. В этом случае они смогут использовать добавочные обновления.

Список репозитория yum, который находится под управлением RHUI, настраивается в вашем экземпляре RHEL во время подготовки. Поэтому выполнять дополнительную настройку не нужно — просто запустите `yum update` после запуска экземпляра RHEL.

## Обзор RHUI
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) является высокомасштабируемым решением для управления содержимым репозитория yum для облачных экземпляров Red Hat Enterprise Linux, размещенных сертифицированными компанией Red Hat поставщиками облачных служб. Являясь продуктом на основе вышестоящего проекта Pulp, RHUI позволяет поставщикам облачных решений локально зеркалировать размещенное Red Hat содержимое репозитория, создавать пользовательские репозитории с собственным содержимым и предоставлять большим группам пользователей доступ к таким репозиториям с помощью системы доставки содержимого с балансировкой нагрузки.

## Регионы, в которых развернута инфраструктура RHUI
RHUI развернута во всех открытых регионах, указанных на [панели мониторинга состояния Azure](https://azure.microsoft.com/status/). Это означает, что в этих регионах службу обновления yum можно получить без дополнительной платы. В дальнейшем эта информация будет обновляться.

## Получение обновлений из локального репозитория обновления (например Red Hat Network Satellite)

Для получения обновлений из локального репозитория обновления необходима лицензия Red Hat Cloud Access и действующая подписка Red Hat для Azure.

Затем необходимо отменить регистрацию RHUI и повторно зарегистрироваться в локальной инфраструктуре обновления. Ниже приведены подробные инструкции.

1.	Отредактируйте /etc/yum.repos.d/rh-cloud.repo и измените все `enabled=1` на `enabled=0`. Например:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	Отредактируйте /etc/yum/pluginconf.d/rhnplugin.conf и измените `enabled=0` на `enabled=1`.
3.	Затем зарегистрируйтесь в Red Hat Network (RHN).

        rhn_register

    или

        rhnreg_ks


> [AZURE.NOTE] За передачу исходящих данных будет взиматься плата (см. [сведения о ценах](http://azure.microsoft.com/pricing/details/data-transfers/)). Чтобы получать добавочные обновления без дополнительных затрат, рекомендуется использовать RHUI по умолчанию.

## Дальнейшие действия
Теперь, когда вы располагаете общими сведениями о RHUI в Azure, вы можете создать образ RHEL из [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/) и использовать `yum update` в имеющемся экземпляре RHEL.

<!---HONumber=AcomDC_0224_2016-->