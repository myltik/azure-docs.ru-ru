---
title: Запись образа виртуальной машины Windows для Azure | Документация Майкрософт
description: Запись образа виртуальной машины Azure Windows, созданной с использованием классической модели развертывания.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 24366d3adf63fb6fafb8afa9f1a9fb359e88d8ed
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30914061"
---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Запись образа виртуальной машины Azure Windows, созданной с использованием классической модели развертывания
> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Сведения о модели диспетчера ресурсов см. в разделе [Запись управляемого образа универсальной виртуальной машины в Azure](../capture-image-resource.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

В этой статье показано, как записать виртуальную машину Azure под управлением Windows, чтобы использовать ее в качестве образа при создании других виртуальных машин. Этот образ включает диск с операционной системой и все диски с данными, подключенные к виртуальной машине. Он не включает сетевые конфигурации, поэтому необходимо настроить конфигурацию сети при создании других виртуальных машин на базе этого образа.

Azure хранит образ в разделе **Образы VM (классика)** службы **Вычисления**, которая отображается при просмотре всех служб Azure. Здесь же хранятся все отправленные вами образы. Дополнительные сведения об образах см. в разделе [Об образах виртуальных машин](about-images.md?toc=%2fazure%2fvirtual-machines%2fWindows%2fclassic%2ftoc.json).

## <a name="before-you-begin"></a>Перед началом работы
В этой статье предполагается, что вы уже создали виртуальную машину Azure и настроили операционную систему, а также присоединили диски данных. Ознакомьтесь со следующими статьями для получения сведений о создании и подготовке виртуальной машины (если вы еще не сделали этого):

* [Создание виртуальной машины из образа](createportal.md)
* [Подключение диска с данными к виртуальной машине](attach-disk.md)
* Убедитесь, что роли сервера поддерживаются Sysprep. Дополнительные сведения см. в разделе [Поддержка ролей сервера в Sysprep](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!WARNING]
> Этот процесс удаляет исходную виртуальную машину, как только она будет зарегистрирована.
>
>

Перед записью образа виртуальной машины Azure мы рекомендуем создать резервную копию целевой виртуальной машины. Это можно сделать с помощью службы архивации Azure. Дополнительные сведения см. в статье [Архивация виртуальных машин Azure](../../../backup/backup-azure-arm-vms.md). Доступны другие решения от сертифицированных партнеров. Чтобы узнать, что сейчас доступно, выполните поиск в Azure Marketplace.

## <a name="capture-the-virtual-machine"></a>Запись виртуальной машины
1. На [портале Azure](http://portal.azure.com) **подключитесь** к виртуальной машине. Указания см. в статье [Вход в виртуальную машину под управлением Windows с помощью классического портала Azure][How to sign in to a virtual machine running Windows Server].
2. Откройте окно командной строки с правами администратора.
3. Измените каталог на `%windir%\system32\sysprep`и запустите файл sysprep.exe.
4. Откроется диалоговое окно **Программа подготовки системы** . Выполните следующее:

   * В разделе **Действие по очистке системы** выберите **Запуск при первом включении компьютера (OOBE)** и убедитесь, что установлен флажок **Обобщить**. Дополнительные сведения об использовании Sysprep см. в статье [How to Use Sysprep: An Introduction][How to Use Sysprep: An Introduction] (Как использовать Sysprep: введение).
   * В разделе **Параметры завершения работы** выберите **Завершение работы**.
   * Последовательно выберите **ОК**.

   ![Запуск Sysprep](./media/capture-image/SysprepGeneral.png)
5. Sysprep завершит работу виртуальной машины, при этом ее состояние на портале Azure изменится на **Остановлена**.
6. На портале Azure щелкните **Виртуальные машины (классика)**, а затем выберите виртуальную машину, которую требуется записать. Группа **Образы VM (классика)** отображается в разделе **Вычисления** при просмотре раздела **Все службы**.

7. На панели команд щелкните **Запись**.

   ![Запись виртуальной машины](./media/capture-image/CaptureVM.png)

   Отображается диалоговое окно **Запись виртуальной машины** .

8. В поле **Имя образа** введите имя нового образа. В поле **Метка образа** укажите метку нового образа.

9. Установите флажок **I've run Sysprep on the virtual machine** (Я запустил Sysprep на виртуальной машине). Этот флажок ссылается на действия с использованием Sysprep, описанные в шагах 3–5. Прежде чем добавить образ Windows Server в набор настраиваемых образов, его _необходимо_ обобщить, запустив программу Sysprep.

10. После завершения записи новый образ будет доступным в **Marketplace** в разделе **Вычисления** в контейнере **Образы VM (классика)**.

    ![Успешная запись образа](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Дополнительная информация
Образ готов к использованию для создания виртуальных машин. Для этого создайте виртуальную машину, выбрав в нижней части меню служб **Все службы**, а затем в группе **Вычисления** щелкнув **Образы VM (классика)**. Инструкции см. в статье [Создание виртуальной машины из образа](createportal.md).

[How to sign in to a virtual machine running Windows Server]:connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
