<properties
    pageTitle="Создание виртуальной машины Linux с помощью портала Azure | Microsoft Azure"
    description="Создание виртуальной машины Linux с помощью портала Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="04/05/2016"
    ms.author="v-livech"
/>

# Создание виртуальной машины Linux с помощью портала Azure

В этой статье показано, как создать виртуальную машину Linux с помощью [портала Azure](https://portal.azure.com/) — быстро и без установки программного обеспечения. Для этого вам понадобится только [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/) и [файлы открытого и закрытого ключей SSH](virtual-machines-linux-mac-create-ssh-keys.md).


1. Войдя на портал Azure с удостоверением учетной записи Azure, щелкните **+Создать** в левом верхнем углу:

    ![экран\_1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. Щелкните в **Marketplace** элемент **Виртуальные машины**, а затем выберите в списке образов **Рекомендуемые приложения** пункт **Ubuntu Server 14.04 LTS**. Отобразится следующий экран:

    ![экран\_2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. Внизу страницы проверьте модель развертывания (`Resource Manager`) и нажмите кнопку **Создать**.

4. На странице **Основные сведения** укажите следующие данные:
    - имя виртуальной машины;
    - имя пользователя-администратора;
    - тип проверки подлинности (следует выбрать **Открытый ключ SSH**);
    - открытый ключ SSH в виде строки (по умолчанию используется строка из каталога `~/.ssh/`);
    - имя группы ресурсов (для создания группы развертывания) или имя существующей группы.

    Нажмите кнопку **ОК**, чтобы продолжить и выбрать размер виртуальной машины. Должен отобразиться примерно такой экран:

    ![экран\_3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

5. Выберите размер **DS1**, чтобы установить Ubuntu на твердотельном накопителе категории "Премиум", а затем нажмите кнопку **Выбрать** для настройки параметров.

    ![экран\_4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

6. В разделе **Параметры** оставьте настройки по умолчанию для хранилища и сети, а затем нажмите кнопку **ОК**, чтобы просмотреть сводку.

    ![экран\_5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

7. Проверьте параметры для новой виртуальной машины Ubuntu и нажмите кнопку **ОК**.

    ![экран\_6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

8. Откройте панель мониторинга портала и в разделе **Сетевые интерфейсы** выберите нужный сетевой адаптер.

    ![экран\_7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

9. Откройте меню общедоступных IP-адресов в параметрах сетевого адаптера.

    ![экран\_8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

10. Подключитесь к общедоступному IP-адресу по протоколу SSH, используя открытый ключ SSH.

```
user@slackware$ ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## Дальнейшие действия

Если нужно, вы можете [добавить диск](virtual-machines-linux-add-disk.md).

<!---HONumber=AcomDC_0413_2016-->