---
title: "Запись образа виртуальной машины Linux | Документация Майкрософт"
description: "Узнайте, как записать образ виртуальной машины Azure под управлением Linux, созданной с помощью классической модели развертывания."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 59a88a9f2db745db22007f6b528f8d41016bde16
ms.lasthandoff: 03/21/2017


---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Запись классической виртуальной машины Linux в виде образа
> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Узнайте, как [выполнить эти действия с помощью модели Resource Manager](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

В этой статье показано, как записать классическую виртуальную машину Azure под управлением Linux, чтобы использовать ее в качестве образа при создании других виртуальных машин. Данный образ виртуальной машины включает в себя диск операционной системы и прочие диски данных, присоединенные к виртуальной машине. Он не включает в себя сетевую конфигурацию, поэтому ее необходимо настроить при создании другой виртуальной машины из образа.

Azure хранит образ в папке **Images** (Образы) вместе со всеми передаваемыми пользователем образами. Дополнительные сведения об образах см. в статье [Об образах виртуальных машин Linux][About Virtual Machine Images in Azure].

## <a name="before-you-begin"></a>Перед началом работы
В шагах этой статьи предполагается, что вы уже создали виртуальную машину Azure, используя классическую модель развертывания, и настроили операционную систему, а также присоединили диски данных. Если необходимо создать виртуальную машину, то прочтите статью [Создание виртуальной машины Linux с помощью интерфейса командной строки Azure][How to Create a Linux Virtual Machine].

## <a name="capture-the-virtual-machine"></a>Запись виртуальной машины
1. [Подключитесь к виртуальной машине](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) с помощью выбранного клиента SSH.
2. В окне SSH введите следующую команду. Выходные данные `waagent` могут незначительно отличаться в зависимости от версии этой служебной программы.

    ```bash
    sudo waagent -deprovision+user
    ```

    Эта команда пытается очистить систему и сделать ее пригодной для повторной подготовки. В ходе операции выполняются такие задачи:

   * удаляются ключи узла SSH (если в файле конфигурации для Provisioning.RegenerateSshHostKeyPair указано значение «y»);
   * очищается конфигурация имени сервера в /etc/resolv.conf;
   * удаляется пароль пользователя `root` из /etc/shadow (если в файле конфигурации для Provisioning.DeleteRootPassword указано значение "y");
   * удаляются кэшированные аренды DHCP-клиентов.
   * возвращается имя узла localhost.localdomain;
   * удаляется последняя подготовленная учетная запись пользователя (полученная из /var/lib/waagent) и **связанные с ней данные**.

     > [!NOTE]
     > Процедура отзыва приводит к удалению файлов и данных, так как образ подготавливается к использованию. Эту команду следует выполнять только на виртуальных машинах, которые требуется записать как шаблон нового образа. Отзыв не гарантирует, что из образа будет удалена вся конфиденциальная информация и что он будет готов к повторному распространению третьим сторонам.

3. Чтобы продолжить, введите **y** . Чтобы предотвратить появление запроса на подтверждение, добавьте параметр `-force` .
4. Введите **Exit** , чтобы закрыть SSH-клиент.

   > [!NOTE]
   > Далее предполагается, что вы уже [установили интерфейс командной строки Azure CLI](../cli-install-nodejs.md) (Azure CLI) на клиентском компьютере. Все следующие действия можно также выполнить на [классическом портале Azure][Azure classic portal].

5. На клиентском компьютере откройте интерфейс командной строки Azure и войдите в подписку Azure. Дополнительные сведения см. в статье [Войдите в Azure из командной строки Azure](../xplat-cli-connect.md).
6. Убедитесь, что вы находитесь в режиме управления службами:

    ```azurecli
    azure config mode asm
    ```

7. Завершите работу виртуальной машины, которая была отозвана. В следующем примере завершается работа виртуальной машины с именем `myVM`:

    ```azurecli
    azure vm shutdown myVM
    ```

   > [!NOTE]
   > Вы можете просмотреть список всех виртуальных машин, созданных в подписке, с помощью команды `azure vm list`.

8. Когда виртуальная машина остановлена, запишите образ. В следующем примере выполняется запись виртуальной машины `myVM` и создается обобщенный образ `myNewVM`:

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    Подкоманда `-t` удаляет исходную виртуальную машину.

9. Новый образ станет доступным в списке образов, которые можно использовать для настройки любой новой виртуальной машины. Его можно просмотреть с помощью следующей команды:

   ```azurecli
   azure vm image list
   ```

   На [портале Azure](http://portal.azure.com) в окне **Образы VM (классика)**, которое относится к службам **Вычисления**, отобразится новый образ. Чтобы открыть окно **Образы VM (классика)**, в нижней части списка служб Azure выберите _Больше служб_ и найдите службы **Вычисления**.   

   ![Успешная запись образа](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Дальнейшие действия
Образ готов к использованию для создания виртуальных машин. Вы можете использовать команду Azure CLI `azure vm create` и указать имя образа, который создали. Дополнительные сведения см. в статье [Команды Azure CLI в режиме управления службами Azure (ASM)](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

Кроме того, с помощью [классического портала Azure][Azure classic portal] можно создать настраиваемую виртуальную машину, выбрав метод **Из коллекции** и указав образ, который вы создали. Дополнительные сведения см. в статье [Создание виртуальной машины Linux с помощью интерфейса командной строки Azure][How to Create a Custom Virtual Machine].

**См. также:** [Руководство пользователя агента Linux для Azure](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[Azure classic portal]: http://manage.windowsazure.com
[About Virtual Machine Images in Azure]: virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]: virtual-machines-linux-classic-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]:windows/classic/attach-disk.md
[How to Create a Linux Virtual Machine]: virtual-machines-linux-classic-create-custom.md

