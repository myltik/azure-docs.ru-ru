---
title: Запись образа виртуальной машины Linux Azure для его использования в качестве шаблона | Документация Майкрософт
description: Узнайте, как записать и подготовить образ виртуальной машины Azure под управлением Linux, созданной посредством модели развертывания с помощью Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 3b45f46197467dd7b83bd986604338e14daa8107
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Запись виртуальной машины Linux, работающей в Azure
Выполните инструкции в этой статье, чтобы подготовить и записать образ виртуальной машины под управлением Linux для Azure в рамках модели развертывания с помощью Resource Manager. При подготовке к использованию виртуальной машины удаляются личные сведения учетных записей и виртуальная машина подготавливается к использованию в качестве образа. После этого можно записать универсальный образ виртуального жесткого диска (VHD) для операционной системы, виртуальные жесткие диски для подключенных дисков данных и [шаблон Resource Manager](../../azure-resource-manager/resource-group-overview.md) для развертывания новой виртуальной машины. В этой статье подробно описано, как записать образ виртуальной машины, использующей неуправляемые диски, с помощью Azure CLI 1.0. Вы можете также [записать образ виртуальной машины, использующей Управляемые диски Azure, с помощью Azure CLI 2.0](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Управляемые диски полностью контролируются платформой Azure и не требуют никакой подготовки или выделения места. Дополнительные сведения об Управляемых дисках Azure см. в [этой статье](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Для создания виртуальных машин с помощью образа следует настроить сетевые ресурсы для каждой новой виртуальной машины и развернуть ее посредством записанных образов VHD, воспользовавшись шаблоном (файлом нотаций объектов JavaScript, т. е. JSON-файлом). Таким образом можно реплицировать виртуальную машину и ее текущую конфигурацию программного обеспечения точно так же, как вы используете образы из Azure Marketplace.

> [!TIP]
> Если вы хотите создать копию существующей виртуальной машины Linux в конкретном состоянии в целях архивации или отладки, ознакомьтесь с разделом [Создание копии виртуальной машины Linux, работающей в Azure](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). А если вам требуется передать виртуальный жесткий диск Linux локальной виртуальной машины, ознакомьтесь с разделом [Передача пользовательского образа диска и создание виртуальной машины Linux на его основе](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#before-you-begin) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.

## <a name="before-you-begin"></a>Перед началом работы
Необходимо выполнить следующие условия.

* **Виртуальная машина Azure, созданная в рамках модели развертывания с помощью Resource Manager**. Если вы еще не создали виртуальную машину Linux, для этого можно использовать [портал](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [интерфейс командной строки Azure (Azure CLI)](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или [шаблоны Resource Manager](create-ssh-secured-vm-from-template.md). 
  
    Настройте виртуальную машину согласно своим требованиям. Например, [добавьте диски данных](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), примените обновления и установите приложения. 
* **Azure CLI**. Установите [Azure CLI](../../cli-install-nodejs.md) на локальном компьютере.

## <a name="step-1-remove-the-azure-linux-agent"></a>Шаг 1. Удалите агент Linux для Azure
Сначала на виртуальной машине Linux выполните команду **waagent** с параметром **deprovision**. Эта команда удаляет файлы и данные перед подготовкой виртуальной машины к использованию. Дополнительные сведения см. в [руководстве пользователя агента Linux для Azure](../extensions/agent-windows.md).

1. Подключитесь к виртуальной машине Linux c помощью клиента SSH.
2. В окне сеанса SSH введите следующую команду.
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Эту команду следует выполнять только на той виртуальной машине, образ которой вы хотите записать. Она не гарантирует, что из образа будет удалена вся конфиденциальная информация и что он будет готов к повторному распространению.
 
3. Чтобы продолжить, введите **y** . Чтобы запрос на подтверждение не появлялся, добавьте параметр **-force** .
4. После выполнения команды введите **exit**. Клиент SSH будет закрыт.

## <a name="step-2-capture-the-vm"></a>Шаг 2. Запись виртуальной машины
Используйте Azure CLI для подготовки и записи виртуальной машины. В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: **myResourceGroup**, **myVnet** и **myVM**.

1. На локальном компьютере откройте Azure CLI и [войдите в свою подписку Azure](/cli/azure/authenticate-azure-cli). 
2. Убедитесь, что режим Resource Manager включен.
   
    ```azurecli
    azure config mode arm
    ```
3. Завершите работу отозванной виртуальной машины с помощью следующей команды.
   
    ```azurecli
    azure vm deallocate -g myResourceGroup -n myVM
    ```
4. Обобщите виртуальную машину:
   
    ```azurecli
    azure vm generalize -g myResourceGroup -n myVM
    ```
5. Теперь выполните команду **azure vm capture**, которая записывает виртуальную машину. В следующем примере виртуальные жесткие диски образа записываются с именами, которые начинаются с **MyVHDNamePrefix**, а параметр **-t** указывает путь к шаблону **MyTemplate.json**. 
   
    ```azurecli
    azure vm capture -g myResourceGroup -n myVM -p myVHDNamePrefix -t myTemplate.json
    ```
   
   > [!IMPORTANT]
   > По умолчанию VHD-файлы образа создаются в учетной записи хранения, которую использует исходная виртуальная машина. Для хранения виртуальных жестких дисков для любых новых виртуальных машин, создаваемых из образа, используйте ту же *учетную запись хранения*. 

6. Чтобы найти расположение записанного образа, откройте шаблон JSON в текстовом редакторе. В разделе **storageProfile** в контейнере **system** найдите **универсальный код ресурса (URI)** **образа**. Например, универсальный код ресурса (URI) для образа диска ОС может выглядеть так: `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Шаг 3. Создание виртуальной машины из записанного образа
Теперь с помощью образа и шаблона мы создадим виртуальную машину под управлением Linux. Ниже описывается, как можно создать виртуальную машину в новой виртуальной сети, используя Azure CLI и записанный JSON-файл шаблона.

### <a name="create-network-resources"></a>Создание сетевых ресурсов
Чтобы использовать шаблон, для новой виртуальной машины сначала необходимо настроить виртуальную сеть и сетевую карту. Мы рекомендуем создать группу ресурсов для этих ресурсов в расположении, в котором хранится образ виртуальной машины. Выполните приведенные ниже команды, подставив нужные имена ресурсов и указав соответствующий регион Azure (вместе centralus).

```azurecli
azure group create myResourceGroup1 -l "centralus"

azure network vnet create myResourceGroup1 myVnet -l "centralus"

azure network vnet subnet create myResourceGroup1 myVnet mySubnet

azure network public-ip create myResourceGroup1 myPublicIP -l "centralus"

azure network nic create myResourceGroup1 myNIC -k mySubnet -m myVnet -p myPublicIP -l "centralus"
```

### <a name="get-the-id-of-the-nic"></a>Получение идентификатора сетевой карты
Чтобы развернуть виртуальную машину из образа, используя JSON-файл, который был сохранен во время записи образа, требуется идентификатор сетевой карты. Получить идентификатор можно с помощью следующей команды.

```azurecli
azure network nic show myResourceGroup1 myNIC
```

Значение **Id** в выходных данных имеет следующий вид: `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`.

### <a name="create-a-vm"></a>Создание виртуальной машины
Теперь из записанного образа создайте виртуальную машину. Для этого выполните следующую команду. Используйте параметр **-f**, чтобы указать путь к сохраненному JSON-файлу шаблона.

```azurecli
azure group deployment create myResourceGroup1 MyDeployment -f MyTemplate.json
```

После вывода результатов команды вам будет предложено указать имя виртуальной машины, имя и пароль администратора, а также идентификатор созданной ранее сетевой карты.

```bash
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName: myNewVM
adminUserName: myAdminuser
adminPassword: ********
networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/myResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
```

Ниже приведен пример вывода на экран при успешном развертывании.

```bash
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment xxxxxxx
+ Waiting for deployment to complete
data:    DeploymentName     : MyDeployment
data:    ResourceGroupName  : MyResourceGroup1
data:    ProvisioningState  : Succeeded
data:    Timestamp          : xxxxxxx
data:    Mode               : Incremental
data:    Name                Type          Value

data:    ------------------  ------------  -------------------------------------

data:    vmName              String        myNewVM

data:    vmSize              String        Standard_D1

data:    adminUserName       String        myAdminuser

data:    adminPassword       SecureString  undefined

data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
info:    group deployment create command OK
```

### <a name="verify-the-deployment"></a>Проверка развертывания
Чтобы проверить развертывание и начать использовать новую виртуальную машину, подключитесь к ней с помощью SSH-клиента. Для этого сначала определите IP-адрес созданной виртуальной машины:

```azurecli
azure network public-ip show myResourceGroup1 myPublicIP
```

Команда возвращает общедоступный IP-адрес. По умолчанию подключение к виртуальной машине Linux по протоколу SSH выполняется через порт 22.

## <a name="create-additional-vms"></a>Создание дополнительных виртуальных машин
Развертывание дополнительных виртуальных машин с использованием записанного образа и шаблона выполняется так же, как описано в предыдущем разделе. Кроме того, для создания виртуальных машин из образа можно использовать шаблон быстрого запуска или команду **azure vm create**.

### <a name="use-the-captured-template"></a>Использование записанного шаблона
Чтобы использовать записанные образ и шаблон, выполните приведенные ниже действия (которые подробно описаны в предыдущем разделе).

* Убедитесь, что образ виртуальной машины хранится в той же учетной записи хранения, в которой размещен виртуальный жесткий диск виртуальной машины.
* Скопируйте JSON-файл шаблона и укажите уникальное имя для виртуального жесткого диска (или дисков) ОС новой виртуальной машины. Например, в разделе **storageProfile** в подразделе **vhd** в элементе **uri** укажите уникальное имя виртуального жесткого диска **osDisk**. Пример: `https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
* Создайте сетевую карту в той же или другой виртуальной сети.
* С помощью измененного JSON-файла шаблона создайте развертывание в группе ресурсов, в которой вы настраиваете виртуальную сеть.

### <a name="use-a-quickstart-template"></a>Использование шаблона быстрого запуска
Если вы хотите, чтобы параметры сети настроились автоматически при создании виртуальной машины из образа, то можете указать ее ресурсы в шаблоне. Для примера можно ознакомиться с шаблоном [101-vm-from-user-image template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) с сайта GitHub. Этот шаблон создает не только виртуальную машину из пользовательского образа, но и необходимую виртуальную сеть, общедоступный IP-адрес и сетевые ресурсы. Пошаговые инструкции по использованию шаблона на портале Azure см. в статье [How to create a virtual machine from a custom image using a ARM template](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/) (Как создать виртуальную машину из пользовательского образа с помощью шаблона ARM).

### <a name="use-the-azure-vm-create-command"></a>Команда azure vm create
Обычно для создания виртуальной машины из образа проще всего использовать шаблон Resource Manager. Однако виртуальную машину можно создать и *по запросу* — с помощью команды **azure vm create** с параметром **-Q** (**--image-urn**). В случае использования данного метода также передается параметр **-d** (**--os-disk-vhd**), чтобы указать расположение VHD-файла операционной системы для новой виртуальной машины. Этот файл должен находиться в контейнере виртуальных жестких дисков учетной записи хранения, где хранится VHD-файл образа. Эта команда автоматически копирует виртуальный жесткий диск для новой виртуальной машины в контейнер **vhds**.

Прежде чем создать виртуальную машину из образа с помощью команды **azure vm create**, выполните следующее.

1. Создайте для развертывания группу ресурсов или укажите существующую.
2. Создайте для новой виртуальной машины общедоступный IP-адрес и сетевой адаптер. Процедура создания виртуальной сети, общедоступного IP-адреса и сетевого адаптера с помощью интерфейса командной строки описана выше в этой статье. (Команда**azure vm create** может также создать и сетевую карту, но для этого потребуется указать дополнительные параметры виртуальной сети и подсети.)

Затем выполните команду, которая передает универсальный код ресурса (URI) в новый VHD-файл операционной системы и в существующий образ. В этом примере создается виртуальная машина размера Standard_A1 в регионе "Восточная часть США".

```azurecli
azure vm create -g myResourceGroup1 -n myNewVM -l eastus -y Linux \
-z Standard_A1 -u myAdminname -p myPassword -f myNIC \
-d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" \
-Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.vhd"
```

Чтобы получить информацию о дополнительных параметрах команды, выполните команду `azure help vm create`.

## <a name="next-steps"></a>Дополнительная информация
Для управления виртуальными машинами с помощью интерфейса командной строки ознакомьтесь с задачами, описанными в статье [Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](create-ssh-secured-vm-from-template.md).

