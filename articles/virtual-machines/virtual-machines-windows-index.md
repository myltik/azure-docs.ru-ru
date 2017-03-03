---
title: "Технические статьи по классическим виртуальным машинам Windows | Microsoft Azure"
description: "Полный список статей Microsoft Azure, посвященных виртуальным машинам Windows в классической модели развертывания"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-service-management
editor: 
ms.assetid: 7f9a508b-34ec-4bdb-92d1-8844480369d5
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 26c58ae4c509cb768807875ecdf96e9a24d6a472
ms.openlocfilehash: 263b3a1edc94a60e756850b2d917840be87e7a5a
ms.lasthandoff: 02/15/2017


---
# <a name="technical-articles-for-windows-vms-in-the-classic-deployment-model"></a>Технические статьи по виртуальным машинам Windows в классической модели развертывания
В этой статье перечислена вся документация, необходимая для создания виртуальных машин Azure под управлением Windows с помощью классической модели развертывания и управления ими.

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

## <a name="overview"></a>Обзор
[About Azure virtual machines (Сведения о виртуальных машинах Azure)](virtual-machines-windows-about.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Вопросы и ответы по виртуальным машинам Azure, созданным при помощи классической модели развертывания](virtual-machines-windows-classic-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Сравнение службы приложений Azure, виртуальных машин, Service Fabric и облачных служб](../app-service-web/choose-web-site-cloud-service-vm.md)

[Виртуальные машины и контейнеры в Azure](virtual-machines-windows-containers.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="environment-setup"></a>Настройка среды
[Бесплатная учетная запись](https://azure.microsoft.com/free/)

[Установка Azure PowerShell](/powershell/azureps-cmdlets-docs)

[Установка Azure CLI](../xplat-cli-install.md)

## <a name="get-started"></a>Приступая к работе
[Схема обучения для виртуальных машин Windows](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)

[Create a virtual machine running Windows in the Azure classic portal (Создание виртуальной машины Windows на классическом портале Azure)](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Log on to a Windows virtual machine using the Azure classic portal (Вход в виртуальную машину Windows с помощью классического портала Azure)](virtual-machines-windows-classic-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="plan"></a>План
[About images for virtual machines (Образы виртуальных машин)](virtual-machines-windows-classic-about-images.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Размеры виртуальных машин](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Виртуальные машины серии A (для ресурсоемких вычислений) и серии H](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Плановое обслуживание виртуальных машин Azure](virtual-machines-windows-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Руководство по реализации служб инфраструктуры Azure](virtual-machines-windows-infrastructure-subscription-accounts-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[How to configure an availability set for virtual machines in the classic deployment model (Создание группы доступности для виртуальных машин в классической модели развертывания)](virtual-machines-windows-classic-configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="deploy"></a>Развернуть
[Создание настраиваемой виртуальной машины под управлением Windows](virtual-machines-windows-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Capture an image of an Azure Windows virtual machine created with the classic deployment model (Запись образа виртуальной машины Windows Azure, созданной с помощью классической модели развертывания)](virtual-machines-windows-classic-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Создание и передача виртуального жесткого диска Windows Server в Azure](virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Автоматизация развертывания виртуальной машины Azure с помощью Chef](virtual-machines-windows-chef-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Create Windows virtual machines with Powershell and the classic deployment model (Создание виртуальных машин Windows с помощью PowerShell и классической модели развертывания)](virtual-machines-windows-classic-create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Включение пользовательских данных в виртуальную машину Azure](virtual-machines-windows-classic-inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="manage"></a>Управление
[Управление виртуальными машинами с помощью Azure PowerShell](virtual-machines-windows-classic-manage-psh.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Подключение классических виртуальных сетей к новым виртуальным сетям](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)

[Обзор агента и расширений виртуальной машины](virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Управление расширениями виртуальной машины](virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Custom Script extension for Windows virtual machines (Расширение Custom Script для виртуальных машин Windows)](virtual-machines-windows-classic-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

## <a name="configure"></a>Настройка
[Сброс пароля или службы удаленных рабочих столов для виртуальной машины Windows](virtual-machines-windows-reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Обзор расширений и компонентов виртуальной машины](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Установка и настройка Symantec Endpoint Protection на виртуальной машине Windows](virtual-machines-windows-classic-install-symantec.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Установка и настройка Trend Micro Deep Security как услуги на ВМ Windows](virtual-machines-windows-classic-install-trend.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Как настроить группу доступности для виртуальных машин в классической модели развертывания](virtual-machines-windows-classic-configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Настройка конечных точек в классической виртуальной машине Azure](virtual-machines-windows-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="storage"></a>Хранилище
[О дисках и виртуальных жестких дисках для виртуальных машин Azure](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Attach a data disk to a Windows virtual machine created with the classic deployment model (Подключение диска данных к виртуальной машине Windows, созданной с помощью классической модели развертывания)](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[How to detach a disk from a Windows virtual machine (Отключение диска от виртуальной машины Windows)](virtual-machines-windows-classic-detach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Использование диска D в качестве диска данных в виртуальной машине Windows](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="networking"></a>Сеть
[Обзор виртуальной сети](../virtual-network/virtual-networks-overview.md)

[Подключение виртуальных машин, созданных с помощью классической модели развертывания модели, к виртуальной сети или облачной службе](virtual-machines-windows-classic-connect-vms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Как создать группы безопасности сети (классические) в PowerShell](../virtual-network/virtual-networks-create-nsg-classic-ps.md)

[Создание балансировщика нагрузки](../load-balancer/load-balancer-get-started-internet-classic-portal.md)

## <a name="develop"></a>Разработка
[Создание и управление виртуальными машинами Azure в Visual Studio](virtual-machines-windows-classic-manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Создание виртуальной машины для веб-приложения с помощью Visual Studio](virtual-machines-windows-classic-web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Выполнение ресурсоемкой задачи в Java-коде на виртуальной машине](virtual-machines-windows-classic-java-run-compute-intensive-task.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Веб-приложение Hello World на Django на виртуальной машине Windows Server](virtual-machines-windows-classic-python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="workloads"></a>Рабочие нагрузки
[Пакет HPC](virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[MongoDB](virtual-machines-windows-classic-install-mongodb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[MySQL](virtual-machines-windows-classic-mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Oracle](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html#support)

[SAP](virtual-machines-windows-classic-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[SQL Server](./windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[Tomcat](virtual-machines-windows-classic-java-run-tomcat-app-server.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="reference"></a>Справочные материалы
[Команды Azure CLI в режиме управления службами Azure (ASM)](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)

[Интерфейс REST API управления службой](https://msdn.microsoft.com/library/azure/ee460799.aspx)

[.NET API управления службами](https://msdn.microsoft.com/library/azure/mt420161.aspx)

[Справочная документация по командлетам PowerShell для управления службами Azure](https://msdn.microsoft.com/library/azure/dn708504.aspx)

## <a name="troubleshooting"></a>Устранение неполадок
[Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Устранение неполадок доступа к приложению, выполняющемуся в виртуальной машине Azure](virtual-machines-windows-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Устранение ошибок выделения ресурсов при создании, перезагрузке или изменении размера виртуальных машин в Azure](virtual-machines-windows-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Устранение неполадок в классическом развертывании при создании виртуальной машины Windows в Azure](virtual-machines-windows-classic-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Устранение неполадок в классическом развертывании при перезагрузке или изменении размера существующей виртуальной машины Windows в Azure](windows/classic/virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)

