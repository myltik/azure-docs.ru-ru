<properties 
	pageTitle="Создание и передача виртуального жесткого диска Red Hat Enterprise Linux для использования в Azure" 
	description="Узнайте, как создать и передать виртуальный жесткий диск (VHD) Azure, содержащий операционную систему RedHat Linux." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="SuperScottz" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="mingzhan"/>


# Подготовка виртуальной машины на основе RedHat для Azure
В этой статье вы узнаете, как подготовить виртуальную машину Red Hat Enterprise Linux (RHEL) для использования в Azure. Статья охватывает RHEL версий 6.7, 7.1 и 7.2, а также низкоуровневые оболочки для подготовки виртуальных машин Hyper-V, KVM и VMWare. Подробнее о требованиях к участникам в программе Red Hat Cloud Access см. на [веб-сайте Red Hat Cloud Access](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) и в статье [Запуск RHEL в Azure](https://access.redhat.com/articles/1989673).




##Подготовка образа с помощью диспетчера Hyper-V 
###Предварительные требования
В этом разделе предполагается, что вы уже установили на виртуальный жесткий диск (VHD) образ RHEL из ISO-файла, полученного с веб-сайта Red Hat. Дополнительные сведения об установке образа операционной системы с помощью диспетчера Hyper-V см. в статье [Установка роли Hyper-V и настройка виртуальной машины](http://technet.microsoft.com/library/hh846766.aspx).

**Замечания по установке RHEL**

- Более новый формат VHDX не поддерживается в Azure. Преобразовать диск в формат VHD можно с помощью диспетчера Hyper-V или командлета PowerShell convert-vhd.

- При установке системы Linux рекомендуется использовать стандартные разделы, а не LVM (как правило, значение по умолчанию во многих дистрибутивах). Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой ВМ в целях устранения неполадок. При желании LVM или RAID может использоваться на дисках данных.

- Не настраивайте раздел подкачки на диске с ОС. Можно настроить агент Linux для создания файла подкачки на временном диске ресурсов. Дополнительные сведения описаны далее.

- Все VHD-диски должны иметь размер, кратный 1 МБ.

- При использовании qemu img для преобразования диска в формат VHD обратите внимание, что в команде qemu-img версии 2.2.1 и выше есть ошибка, которая приводит к созданию неправильно отформатированного диска VHD. Эта ошибка будет устранена в следующем выпуске qemu-img. На данный момент рекомендуется использовать qemu-img версии 2.2.0 или более ранних версий.


###RHEL 6.7

1.	В диспетчере Hyper-V выберите виртуальную машину.

2.	Щелкните **Подключение**, чтобы открыть окно консоли для виртуальной машины.

3.	Удалите NetworkManager, выполнив следующую команду:

        # sudo rpm -e --nodeps NetworkManager

    **Примечание.** Если пакет еще не установлен, эта команда завершится с сообщением об ошибке. Это ожидаемое поведение.

4.	Создайте файл с именем **network** в каталоге `/etc/sysconfig/`. Файл должен содержать следующий текст:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	Создайте файл с именем **ifcfg-eth0** в каталоге `/etc/sysconfig/network-scripts/`. Файл должен содержать следующий текст:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	Переместите или удалите правила udev во избежание создания статических правил для интерфейса Ethernet. Эти правила приводят к появлению проблем при клонировании виртуальной машины в Microsoft Azure или Hyper-V:
            
        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

        # sudo chkconfig network on

8.	Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.	Пакет WALinuxAgent `WALinuxAgent-<version>` был принудительно отправлен в репозиторий Fedora EPEL 6. Включите репозиторий epel, выполнив следующую команду:

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

10.	Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/boot/grub/menu.lst` в текстовом редакторе и убедитесь, что ядро по умолчанию содержит следующие параметры.

        console=ttyS0 
        earlyprintk=ttyS0 
        rootdelay=300 
        numa=off

    Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Это приведет к отключению NUMA в связи с неисправностью версии ядра, используемого RHEL 6.

    Помимо вышесказанного, рекомендуется удалить следующие параметры:

        rhgb quiet crashkernel=auto

    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт.

    При желании можно настроить параметр crashkernel, однако учитывайте, что он сокращает объем доступной памяти в виртуальной машине на 128 МБ или более, что может оказаться проблемой в виртуальных машинах небольшого размера.

11.	Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки. Обычно это сделано по умолчанию. Измените файл /etc/ssh/sshd\_config, включив в него следующую строку:

        ClientAliveInterval 180

12.	Установите агент Linux для Azure, выполнив следующую команду:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    **Примечание.** Установка пакета WALinuxAgent приведет к удалению пакетов NetworkManager и NetworkManager-gnome, если они еще не были удалены, как описано в шаге 2.

13.	Не создавайте пространство подкачки на диске с ОС. Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure. Следует отметить, что локальный диск ресурсов является временным диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените следующие параметры в /etc/waagent.conf соответствующим образом:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14.	Отмените регистрацию подписки (при необходимости), выполнив следующую команду:

        # sudo subscription-manager unregister

15.	Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16.	В диспетчере Hyper-V выберите **Действие -> Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.

###RHEL 7.1/7.2

1.  В диспетчере Hyper-V выберите виртуальную машину.

2.	Щелкните Подключение, чтобы открыть окно консоли для виртуальной машины.

3.	Создайте файл с именем **network** в каталоге `/etc/sysconfig/`. Файл должен содержать следующий текст:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.	Создайте файл с именем **ifcfg-eth0** в каталоге `/etc/sysconfig/network-scripts/`. Файл должен содержать следующий текст:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

5.	Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

        # sudo chkconfig network on

6.	Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/etc/default/grub` в текстовом редакторе и измените параметр **GRUB\_CMDLINE\_LINUX**, например:

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Помимо вышесказанного, рекомендуется удалить следующие параметры:

        rhgb quiet crashkernel=auto
    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно настроить параметр crashkernel, однако учитывайте, что он сокращает объем доступной памяти в виртуальной машине на 128 МБ или более, что может оказаться проблемой в виртуальных машинах небольшого размера.

8.	После внесения изменений в файл `/etc/default/grub`, как указано выше, выполните следующую команду для повторного создания конфигурации grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.	Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки. Обычно это сделано по умолчанию. Измените файл `/etc/ssh/sshd_config`, добавив в него следующую строку:

        ClientAliveInterval 180

10.	Пакет WALinuxAgent `WALinuxAgent-<version>` был принудительно отправлен в репозиторий Fedora EPEL 6. Включите репозиторий epel, выполнив следующую команду:

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

11.	Установите агент Linux для Azure, выполнив следующую команду:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service 

12.	Не создавайте пространство подкачки на диске с ОС. Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure. Следует отметить, что локальный диск ресурсов является временным диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) соответствующим образом измените следующие параметры в файле `/etc/waagent.conf`.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13.	Чтобы отменить регистрацию подписки, выполните следующую команду:

        # sudo subscription-manager unregister

14.	Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:
        
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15.	В диспетчере Hyper-V выберите **Действие -> Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.


##Подготовка образа из KVM 
###RHEL 6.7

1.	Загрузите KVM-образ RHEL 6.7 с веб-сайта Red Hat.

2.	Установите корневой пароль

    Создайте зашифрованный пароль и скопируйте результат выполнения команды:

        # openssl passwd -1 changeme
    Установите корневой пароль с помощью Guestfish:
       
        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit
    Во втором поле привилегированного пользователя измените значение "!!" на зашифрованный пароль.

3.	Создайте виртуальную машину в KVM из образа qcow2, выберите тип диска **qcow2** и установите модель устройства интерфейса виртуальной сети в **virtio**. Затем запустите виртуальную машину и войдите в систему как привилегированный пользователь.

4.	Создайте файл с именем **network** в каталоге `/etc/sysconfig/`. Файл должен содержать следующий текст:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	Создайте файл с именем **ifcfg-eth0** в каталоге `/etc/sysconfig/network-scripts/`. Файл должен содержать следующий текст:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	Переместите или удалите правила udev во избежание создания статических правил для интерфейса Ethernet. Эти правила приводят к появлению проблем при клонировании виртуальной машины в Microsoft Azure или Hyper-V:

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

        # chkconfig network on

8.	Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

        # subscription-manager register –auto-attach --username=XXX --password=XXX

9.	Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/boot/grub/menu.lst` в текстовом редакторе и убедитесь, что ядро по умолчанию содержит следующие параметры.

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Это приведет к отключению NUMA в связи с неисправностью версии ядра, используемого RHEL 6.

    Помимо вышесказанного, рекомендуется удалить следующие параметры:

        rhgb quiet crashkernel=auto

    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно настроить параметр crashkernel, однако учитывайте, что он сокращает объем доступной памяти в виртуальной машине на 128 МБ или более, что может оказаться проблемой в виртуальных машинах небольшого размера.

10.	Удаление cloud-init:

        # yum remove cloud-init

11.	Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки:
 
        # chkconfig sshd on

    Измените файл /etc/ssh/sshd\_config, включив в него следующие строки:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Перезапустите sshd:

		# service sshd restart

12.	Пакет WALinuxAgent `WALinuxAgent-<version>` был принудительно отправлен в репозиторий Fedora EPEL 6. Включите репозиторий epel, выполнив следующую команду:

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

13.	Установите агент Linux для Azure, выполнив следующую команду:

        # yum install WALinuxAgent
        # chkconfig waagent on

14.	Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure. Следует отметить, что локальный диск ресурсов является временным диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените следующие параметры в **/etc/waagent.conf** соответствующим образом:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	Отмените регистрацию подписки (при необходимости), выполнив следующую команду:
        
        # subscription-manager unregister

16.	Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17.	Завершите работу виртуальной машины в KVM.

18.	Конвертируйте образ qcow2 в формат VHD. Для начала конвертируйте образ в формат RAW:
         
         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Убедитесь, что размер образа в формате RAW соответствует 1 МБ; если это не так, округлите размер образа до 1 МБ: # MB=$((1024*1024)) # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \\ gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}') # rounded\_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    Конвертируйте диск в формате RAW в VHD фиксированного размера:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


###RHEL 7.1/7.2

1.	Загрузите KVM-образ RHEL 7.1(или 7.2) с веб-сайта Red Hat. Для примера мы используем RHEL 7.1.

2.	Установите корневой пароль

    Создайте зашифрованный пароль и скопируйте результат выполнения команды

        # openssl passwd -1 changeme

    Установите корневой пароль с помощью Guestfish:

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Во втором поле привилегированного пользователя измените значение "!!" на зашифрованный пароль.

3.	Создайте виртуальную машину в KVM из образа qcow2, выберите тип диска **qcow2** и установите модель устройства интерфейса виртуальной сети в **virtio**. Затем запустите виртуальную машину и войдите в систему как привилегированный пользователь.

4.	Создайте файл с именем **network** в каталоге `/etc/sysconfig/`. Файл должен содержать следующий текст:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	Создайте файл с именем **ifcfg-eth0** в каталоге `/etc/sysconfig/network-scripts/`. Файл должен содержать следующий текст:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

        # chkconfig network on

7.	Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

        # subscription-manager register –auto-attach --username=XXX --password=XXX

8.	Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/etc/default/grub` в текстовом редакторе и измените параметр **GRUB\_CMDLINE\_LINUX**, например:

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Помимо вышесказанного, рекомендуется удалить следующие параметры:

        rhgb quiet crashkernel=auto

    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно настроить параметр crashkernel, однако учитывайте, что он сокращает объем доступной памяти в виртуальной машине на 128 МБ или более, что может оказаться проблемой в виртуальных машинах небольшого размера.

9.	После внесения изменений в файл `/etc/default/grub`, как указано выше, выполните следующую команду для повторного создания конфигурации grub:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10.	Удаление cloud-init:

        # yum remove cloud-init

11.	Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки:

        # systemctl enable sshd

    Измените файл /etc/ssh/sshd\_config, включив в него следующие строки:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Перезапустите sshd:

        systemctl restart sshd	

12.	Пакет WALinuxAgent `WALinuxAgent-<version>` был принудительно отправлен в репозиторий Fedora EPEL 6. Включите репозиторий epel, выполнив следующую команду:

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

13.	Установите агент Linux для Azure, выполнив следующую команду:

        # yum install WALinuxAgent

    Включите службу waagent:

        # systemctl enable waagent.service

14.	Не создавайте пространство подкачки на диске с ОС. Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure. Следует отметить, что локальный диск ресурсов является временным диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) соответствующим образом измените следующие параметры в файле `/etc/waagent.conf`.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	Отмените регистрацию подписки (при необходимости), выполнив следующую команду:

        # subscription-manager unregister

16.	Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17.	Остановите работу виртуальной машины в KVM.

18.	Конвертируйте образ qcow2 в формат VHD.

    Для начала конвертируйте образ в формат RAW:

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    Убедитесь, что размер образа в формате RAW соответствует 1 МБ; если это не так, округлите размер образа до 1 МБ:

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    Конвертируйте диск в формате RAW в VHD фиксированного размера:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


##Подготовка образа из VMWare
###Предварительные требования
В этом разделе предполагается, что вы уже установили виртуальную машину RHEL в VMWare. Дополнительные сведения об установке операционной системы в VMWare см. в [руководстве по установке операционной системы на виртуальной машине VMWare](http://partnerweb.vmware.com/GOSIG/home.html).
 
- При установке системы Linux рекомендуется использовать стандартные разделы, а не LVM (как правило, значение по умолчанию во многих дистрибутивах). Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой ВМ в целях устранения неполадок. При желании LVM или RAID может использоваться на дисках данных.

- Не настраивайте раздел подкачки на диске с ОС. Можно настроить агент Linux для создания файла подкачки на временном диске ресурсов. Дополнительные сведения описаны далее.

- При создании виртуального жесткого диска выберите параметр **Хранить виртуальный диск как один файл**.

###RHEL 6.7
1.	Удалите NetworkManager, выполнив следующую команду:

         # sudo rpm -e --nodeps NetworkManager

    **Примечание.** Если пакет еще не установлен, эта команда завершится с сообщением об ошибке. Это ожидаемое поведение.

2.	Создайте файл с именем **network** в каталоге /etc/sysconfig/. Файл должен содержать следующий текст:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.	Создайте файл с именем **ifcfg-eth0** в каталоге /etc/sysconfig/network-scripts/. Файл должен содержать следующий текст:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.	Переместите или удалите правила udev во избежание создания статических правил для интерфейса Ethernet. Эти правила приводят к появлению проблем при клонировании виртуальной машины в Microsoft Azure или Hyper-V:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.	Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

        # sudo chkconfig network on

6.	Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	Пакет WALinuxAgent `WALinuxAgent-<version>` был принудительно отправлен в репозиторий Fedora EPEL 6. Включите репозиторий epel, выполнив следующую команду:

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

8.	Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл "/boot/grub/menu.lst" в текстовом редакторе и убедитесь, что ядро по умолчанию включает следующие параметры:

        console=ttyS0 
        earlyprintk=ttyS0 
        rootdelay=300 
        numa=off

    Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Это приведет к отключению NUMA в связи с неисправностью версии ядра, используемого RHEL 6. Помимо вышесказанного, рекомендуется удалить следующие параметры:

        rhgb quiet crashkernel=auto

    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно настроить параметр crashkernel, однако учитывайте, что он сокращает объем доступной памяти в виртуальной машине на 128 МБ или более, что может оказаться проблемой в виртуальных машинах небольшого размера.

9.	Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки. Обычно это сделано по умолчанию. Измените файл `/etc/ssh/sshd_config`, добавив в него следующую строку:

        ClientAliveInterval 180

10.	Установите агент Linux для Azure, выполнив следующую команду:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

11.	Не создавайте пространство подкачки на диске с ОС:
    
    Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure. Следует отметить, что локальный диск ресурсов является временным диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) соответствующим образом измените следующие параметры в файле `/etc/waagent.conf`.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	Отмените регистрацию подписки (при необходимости), выполнив следующую команду:

        # sudo subscription-manager unregister

13.	Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

        # sudo waagent -force -deprovision 
        # export HISTSIZE=0
        # logout

14.	Завершите работу виртуальной машины и конвертируйте файл VMDK в формат VHD.

    Для начала конвертируйте образ в формат RAW:

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    Убедитесь, что размер образа в формате RAW соответствует 1 МБ; если это не так, округлите размер образа до 1 МБ:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    Конвертируйте диск в формате RAW в VHD фиксированного размера:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd

###RHEL 7.1/7.2

1.	Создайте файл с именем **network** в каталоге /etc/sysconfig/. Файл должен содержать следующий текст:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.	Создайте файл с именем **ifcfg-eth0** в каталоге /etc/sysconfig/network-scripts/. Файл должен содержать следующий текст:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.	Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

        # sudo chkconfig network on

4.	Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.	Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/etc/default/grub` в текстовом редакторе и измените параметр **GRUB\_CMDLINE\_LINUX**, например:

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Помимо вышесказанного, рекомендуется удалить следующие параметры:

        rhgb quiet crashkernel=auto

    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно настроить параметр crashkernel, однако учитывайте, что он сокращает объем доступной памяти в виртуальной машине на 128 МБ или более, что может оказаться проблемой в виртуальных машинах небольшого размера.

6.	После внесения изменений в файл `/etc/default/grub`, как указано выше, выполните следующую команду для повторного создания конфигурации grub:

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.	Добавьте модули Hyper-V в initramfs:

    Измените файл `/etc/dracut.conf`, добавив в него следующее содержимое:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Выполните сборку initramfs заново:

        # dracut –f -v

8.	Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки. Обычно это сделано по умолчанию. Измените файл `/etc/ssh/sshd_config`, добавив в него следующую строку:

        ClientAliveInterval 180

9.	Пакет WALinuxAgent `WALinuxAgent-<version>` был принудительно отправлен в репозиторий Fedora EPEL 6. Включите репозиторий epel, выполнив следующую команду:


        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

10.	Установите агент Linux для Azure, выполнив следующую команду:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11.	Не создавайте пространство подкачки на диске с ОС. Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure. Следует отметить, что локальный диск ресурсов является временным диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) соответствующим образом измените следующие параметры в файле `/etc/waagent.conf`.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	Чтобы отменить регистрацию подписки, выполните следующую команду:

        # sudo subscription-manager unregister

13.	Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14.	Завершите работу виртуальной машины и конвертируйте файл VMDK в формат VHD.

    Для начала конвертируйте образ в формат RAW:

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    Убедитесь, что размер образа в формате RAW соответствует 1 МБ; если это не так, округлите размер образа до 1 МБ:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    Конвертируйте диск в формате RAW в VHD фиксированного размера:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


##Автоматическая подготовка ISO-образа с помощью kickstart-файла
###RHEL 7.1/7.2

1.	Создайте kickstart-файл, содержащий приведенный ниже код, и сохраните файл. Дополнительные сведения о начальной установке см. в [руководстве по начальной установке](https://access.redhat.com/documentation/ru-RU/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).


        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        # Primary Fedora repo
        repo --name="epel7" --baseurl="http://dl.fedoraproject.org/pub/epel/7/x86_64/"

        %packages
        @base
        @console-internet
        chrony
        sudo
        python-pyasn1
        parted
        ntfsprogs
        WALinuxAgent
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^(ResourceDisk\.EnableSwap)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^(ResourceDisk\.SwapSizeMB)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^(GRUB_CMDLINE_LINUX)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#(ClientAliveInterval).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

 

2.	Поместите kickstart-файл в место, доступное для системы установки.
 
3.	В диспетчере Hyper-V создайте новую виртуальную машину. На странице **Подключение виртуального жесткого диска** выберите параметр **Подключить виртуальный жесткий диск позднее** и выполните инструкции мастера создания виртуальной машины.

4.	Откройте параметры виртуальной машины:

    а. Подключите новый виртуальный жесткий диск к виртуальной машине и выберите параметры **Формат VHD** и **Фиксированный размер**.
    
    b. Подключите установочный ISO-образ к приводу DVD.

    c. В BIOS выберите загрузку с компакт-диска.

5.	Запустите виртуальную машину. Когда появится руководство по установке, нажмите клавишу **TAB**, чтобы настроить параметры загрузки.

6.	Введите `inst.ks=<the location of the Kickstart file>` в конце параметров загрузки и нажмите клавишу **ВВОД**.

7.	Дождитесь завершения установки. После этого виртуальная машина будет автоматически остановлена. Виртуальный жесткий диск Linux готов к передаче в Azure.

##Известные проблемы
Существуют две известные проблемы, которые могут возникнуть при использовании RHEL 7.1 в Hyper-V и Azure.

###Проблема: зависание дискового ввода-вывода 

Проблема может возникать при частых операциях ввода-вывода, связанных с диском хранилища RHEL 7.1 в Hyper-V и Azure.

Скорость воспроизведения:

Проблема возникает периодически; чаще встречается при частых операциях дискового ввода-вывода в Hyper-V и Azure.

    
[AZURE.NOTE]Эту проблему Red Hat уже устранила. Чтобы установить соответствующие исправления, выполните следующую команду:

    # sudo yum update


## Дальнейшие действия
Теперь виртуальный жесткий диск Red Hat Enterprise Linux можно использовать для создания новых виртуальных машин Azure. Чтобы получить дополнительные сведения о низкоуровневых оболочках, сертифицированных для запуска Red Hat Enterprise Linux, посетите [веб-сайт Red Hat](https://access.redhat.com/certified-hypervisors).

<!---HONumber=AcomDC_1210_2015-->