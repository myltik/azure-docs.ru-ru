---
title: "Восстановление файлов на виртуальной машине с помощью службы архивации Azure | Документы Майкрософт"
description: "Сведения о процедуре восстановления на уровне файлов для виртуальной машины Azure с помощью служб архивации и восстановления."
services: backup, virtual-machines
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/29/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: abad99f862e3831e70254e76e768e4eb7b2a5053
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Восстановление файлов на виртуальной машине в Azure
Служба архивации Azure создает точки восстановления, которые хранятся в геоизбыточных хранилищах восстановления. Используя точку восстановления, можно восстановить всю виртуальную машину или только отдельные файлы. В этой статье подробно описывается восстановление отдельных файлов. Из этого руководства вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * Составление списка и выбор точек восстановления
> * Подключение точки восстановления к виртуальной машине
> * Восстановление файлов из точки восстановления

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.18 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Предварительные требования
Для выполнения этого руководства требуется виртуальная машина Linux, защищенная с помощью службы архивации Azure. Для имитации случайного удаления файлов и процесса восстановления мы удалим страницу с веб-сервера. Если вам требуется виртуальная машина Linux, на которой выполняется веб-сервер и которая защищена с помощью службы архивации Azure, см. раздел [Резервное копирование виртуальной машины в Azure с помощью интерфейса командной строки](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Обзор службы архивации
Когда система Azure запускает архивацию, расширение резервного копирования на виртуальной машине делает моментальный снимок на момент времени. Расширение резервного копирования устанавливается на виртуальной машине при запросе первой резервной копии. Если на момент архивации виртуальная машина не запущена, служба архивации Azure также может создавать моментальный снимок базового хранилища.

По умолчанию служба архивации Azure создает резервную копию, согласованную с файловой системой. После создания моментального снимка службой архивации Azure данные передаются в хранилище служб восстановления. Для повышения эффективности служба архивации Azure анализирует блоки данных и передает только те из них, которые были изменены с момента предыдущего резервного копирования.

После передачи данных служба удаляет моментальный снимок и создает точку восстановления.


## <a name="delete-a-file-from-a-vm"></a>Удаление файла из виртуальной машины
Если вы случайно удалили файл или внесли в него изменения, можно восстановить отдельные файлы из точки восстановления. Этот процесс позволяет просмотреть файлы, сохраненные в точке восстановления, и восстановить только необходимые файлы. В этом примере мы удаляем файл с веб-сервера для демонстрации процесса восстановления на уровне файла.

1. Чтобы подключиться к виртуальной машине, получите ее IP-адрес с помощью команды [az vm show](/cli/azure/vm?view=azure-cli-latest#az_vm_show):

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. Чтобы убедиться, что веб-сайт работает, откройте в веб-браузере общедоступный IP-адрес виртуальной машины. Не закрывайте окно веб-браузера.

    ![Веб-страница NGINX по умолчанию](./media/tutorial-restore-files/nginx-working.png)

3. Подключитесь к виртуальной машине по протоколу SSH. Замените *publicIpAddress* общедоступным IP-адресом, который вы получили в предыдущей команде:

    ```bash
    ssh publicIpAddress
    ```

4. Удалите с веб-сервера страницу по умолчанию по адресу */var/www/html/index.nginx-debian.html* следующим образом:

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. В веб-браузере обновите страницу. Веб-сайт больше не загружает страницу, как показано в следующем примере:

    ![Веб-сайт NGINX больше не загружает страницу по умолчанию](./media/tutorial-restore-files/nginx-broken.png)

6. Закройте сеанс SSH для виртуальной машины следующим образом:

    ```bash
    exit
    ```


## <a name="generate-file-recovery-script"></a>Создание сценария восстановления файла
Чтобы восстановить файлы, служба архивации Azure предоставляет сценарий для запуска на виртуальной машине: она подключает точку восстановления как локальный диск. Вы можете просмотреть этот локальный диск, восстановить файлы на виртуальной машине, а затем отключить точку восстановления. Служба архивации Azure продолжает выполнять резервное копирование данных согласно назначенной политике расписания и хранения.

1. Чтобы создать список точек восстановления для виртуальной машины, используйте команду [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list). В этом примере мы выбираем самую последнюю точку восстановления для виртуальной машины с именем *myVM*, защищенной в хранилище *myRecoveryServicesVault*:

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. Чтобы получить сценарий, который подключает точку восстановления к виртуальной машине, используйте команду [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_mount_rp). В следующем примере мы получаем сценарий для виртуальной машины с именем *myVM*, защищенной в хранилище *myRecoveryServicesVault*.

    Замените *myRecoveryPointName* именем точки восстановления, полученной в предыдущей команде:

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    Сценарий скачивается, и отображается пароль, как показано в следующем примере:

    ```
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. Для передачи сценария на виртуальную машину используйте безопасное копирование (SCP). Укажите имя загруженного сценария и замените *publicIpAddress* общедоступным IP-адресом виртуальной машины. Убедитесь, что вы указали завершающий символ `:` в конце команды SCP следующим образом:

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```


## <a name="restore-file-to-your-vm"></a>Восстановление файла на виртуальной машине
Скопировав сценарий восстановления на виртуальную машину, вы можете подключить точку восстановления и восстановить файлы.

1. Подключитесь к виртуальной машине по протоколу SSH. Замените *publicIpAddress* общедоступным IP-адресом виртуальной машины следующим образом:

    ```bash
    ssh publicIpAddress
    ```

2. Чтобы сценарий работал правильно, необходимо добавить разрешения на выполнение с помощью команды **chmod**. Введите имя собственного сценария:

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. Чтобы подключить точку восстановления, запустите сценарий. Введите имя собственного сценария:

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    Во время выполнения сценария вам будет предложено ввести пароль для доступа к точке восстановления. Введите пароль, указанный в выходных данных предыдущей команды [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_mount_rp), с помощью которой был создан сценарий восстановления.

    В выходных данных сценария будет указан путь к точке подключения. В следующем примере выходные данные указывают, что точка восстановления подключена по адресу */home/azureuser/myVM-20170919213536/Volume1*:

    ```
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465
    
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
    
    ************ Volumes of the recovery point and their mount paths on this machine ************
    
    Sr.No.  |  Disk  |  Volume  |  MountPath
    
    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1
    
    ************ Open File Explorer to browse for files. ************
    ```

4. Используйте команду **cp**, чтобы скопировать веб-страницу по умолчанию NGINX из подключенной точки восстановления обратно в исходное расположение файла. Замените точку подключения */home/azureuser/myVM-20170919213536/Volume1* именем вашего расположения:

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

6. В веб-браузере обновите страницу. Веб-сайт снова правильно загружает страницу, как показано в следующем примере:

    ![Теперь веб-сайт NGINX загружается правильно](./media/tutorial-restore-files/nginx-restored.png)

7. Закройте сеанс SSH для виртуальной машины следующим образом:

    ```bash
    exit
    ```

8. Отключите точку восстановления от виртуальной машины с помощью команды [az backup restore files unmount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_unmount_rp). В следующем примере выполняется отключение точки восстановления от виртуальной машины *myVM* в хранилище *myRecoveryServicesVault*.

    Замените *myRecoveryPointName* именем вашей собственной точки восстановления, полученной в предыдущей команде:
    
    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве описывается подключение точки восстановления к виртуальной машине и восстановление файлов для веб-сервера. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Составление списка и выбор точек восстановления
> * Подключение точки восстановления к виртуальной машине
> * Восстановление файлов из точки восстановления

Перейдите к следующему руководству, чтобы узнать о том, как выполнить резервное копирование сервера Windows Server в Azure.

> [!div class="nextstepaction"]
> [Архивация Windows Server в Azure](tutorial-backup-windows-server-to-azure.md)

