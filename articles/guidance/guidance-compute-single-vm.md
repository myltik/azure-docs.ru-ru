<properties
   pageTitle="Запуск виртуальной машины (Windows) | Шаблон | Microsoft Azure"
   description="Сведения о выполнении одной виртуальной машины в Azure с учетом требований к масштабируемости, устойчивости, управляемости и безопасности."
   services=""
   documentationCenter="na"
   authors="mikewasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="mikewasson"/>

# Запуск одной виртуальной машины Windows в Azure

В этой статье описаны проверенные методы для запуска одной виртуальной машины Windows в Azure. Основное внимание уделяется вопросам масштабируемости, обеспечения устойчивости, управляемости и безопасности.

> [AZURE.WARNING] Azure не предусматривает поддержку соглашения об уровне обслуживания с гарантией времени непрерывной работы при запуске одной виртуальной машины. Используйте эту конфигурацию для развертывания в средах разработки и тестирования, но не в рабочей среде.

Azure предоставляет две модели развертывания: модель с использованием [диспетчера ресурсов][resource-manager-overview] и классическая. В этой статье описывается использование модели развертывания c использованием диспетчера ресурсов, которую рекомендует применять для новых развертываний корпорация Майкрософт. Диспетчер ресурсов можно использовать несколькими способами, в частности с помощью [портала Azure][azure-portal], [Azure PowerShell][azure-powershell], команд [интерфейса командной строки Azure][azure-cli] или [шаблонов диспетчера ресурсов][arm-templates]. В этой статье описываются примеры с использованием интерфейса командной строки Azure.

![IaaS: одна виртуальная машина](media/guidance-compute-single-vm.png)

Для подготовки одной виртуальной машины в Azure нужно больше элементов, чем для создания виртуальной машины с ядром. К таким элементам относятся вычислительные, сетевые ресурсы и ресурсы хранения.

- **Группа ресурсов**. Создайте [группу ресурсов][resource-manager-overview] для хранения ресурсов виртуальной машины. _Группа ресурсов_ представляет собой контейнер, содержащий связанные ресурсы.

- **Виртуальная машина**. Виртуальную машину можно подготовить на основе списка опубликованных образов или VHD-файла, переданного в хранилище BLOB-объектов Azure.

- **Диск ОС**. Этот диск представляет собой VHD-файл, сохраненный в [службе хранилища Azure][azure-storage]. Это означает, что он сохранится, даже если хост-компьютер выйдет из строя.

- **Временный диск**. Виртуальная машина изначально создается с этим диском (в Windows это диск `D:`). Временный диск хранится на физическом диске хост-компьютера. Он _не_ сохраняется в службе хранилища Azure и может быть удален во время перезагрузки и других событий жизненного цикла виртуальной машины. Используйте этот диск только для временных данных, таких как данные страниц или файлы подкачки.

- **Диски данных**. [Диск данных][data-disk] — это постоянный виртуальный жесткий диск, используемый для хранения данных приложений. Диски данных, такие как диск ОС, хранятся в службе хранилища Azure.

- **Виртуальная сеть и подсеть**. Каждая виртуальная машина в Azure развертывается в виртуальной сети, которая затем разделяется на подсети.

- **Общедоступный IP-адрес**. Общедоступный IP-адрес используется для связи с виртуальной машиной. Например, через удаленный рабочий стол.

- **Группа безопасности сети**. [Группа безопасности сети][nsg] разрешает или запрещает сетевой трафик к виртуальной машине. Правила группы безопасности сети по умолчанию запрещают весь входящий сетевой трафик.

- **Сетевой адаптер**. Сетевой адаптер обеспечивает взаимодействие виртуальной машины и виртуальной сети.

- **Диагностика.** Ведение журналов диагностики очень важно для устранения неполадок виртуальной машины и управления ею.

## Рекомендации по виртуальным машинам

- При перемещении существующей рабочей нагрузки в Azure выберите [размер виртуальной машины][virtual-machine-sizes], который больше всего соответствует локальным серверам. Для интенсивных рабочих нагрузок ввода-вывода рекомендуется использовать виртуальные машины серии DS и GS, для которых доступно хранилище класса Premium.

    - Если для рабочих нагрузок не требуется доступ к высокопроизводительному диску с малой задержкой, рассмотрите другие виртуальные машины уровня Standard, например виртуальные машины серии А или D.

- При подготовке виртуальной машины и других ресурсов необходимо указать расположение. Как правило, необходимо выбрать расположение как можно ближе к внутренним пользователям или клиентам. Однако не для всех расположений доступны все варианты SKU виртуальной машины. Дополнительные сведения см. в разделе [Службы по региону][services-by-region].

- Дополнительные сведения о выборе опубликованных образов виртуальной машины см. в статье [Просмотр и выбор образов виртуальных машин Azure с помощью оболочки Windows PowerShell и инфраструктуры Azure CLI][select-vm-image].

## Рекомендации по дискам и хранилищам

Чтобы добиться максимальной производительности дисковых операций ввода-вывода, рекомендуется использовать [хранилище класса Premium][premium-storage]. Однако это хранилище поддерживает только виртуальные машины серии DS и GS.

- Цена на хранилище класса Premium зависит от размера подготовленного диска. Скорость выполнения операций ввода-вывода и пропускная способность (т. е. скорость передачи данных) также зависят от размера диска. Поэтому во время подготовки диска следует учитывать все эти факторы.

- Цена на стандартное хранилище зависит от объема данных, записываемых на диск. Таким образом, рекомендуется подготовить для хранилища максимальный размер (1023 ГБ). Обязательно используйте для дисков быстрое форматирование. При форматировании переполненного диска на диск записываются нули, что приводит к использованию фактической емкости хранилища. Сведения о ценах см. в разделе [Цены на хранилища Azure][storage-price].

- Если вам нужно стандартное хранилище, обратите внимание на геоизбыточное хранилище, в котором данные будут устойчивы даже в случае отключения электричества во всем регионе или аварии, при которой основной регион не может быть восстановлен.

- Добавьте один или несколько дисков данных. При создании виртуальный машины жесткий диск не форматируется. Чтобы отформатировать диск, войдите в систему виртуальной машины.

- При наличии большого количества дисков данных учитывайте общие ограничения на количество операций ввода-вывода для учетной записи хранения. Дополнительные сведения см. в разделе [Ограничения для дисков виртуальной машины][vm-disk-limits].

- Для повышения производительности храните журналы диагностики в отдельной учетной записи хранения. Учетной записи локально избыточного хранилища достаточно для хранения журналов диагностики.

## Рекомендации по сети

- Для одной виртуальной машины создайте одну виртуальную сеть и подсеть, а также группу безопасности сети и общедоступный IP-адрес.

- Общедоступный IP-адрес может быть динамическим или статическим. По умолчанию используется динамический IP-адрес.

    - Зарезервируйте [статический IP-адрес][static-ip], если вам нужен фиксированный IP-адрес. Например, для создания записи А в DNS или добавления IP-адреса в список разрешенных.

    - По умолчанию у IP-адреса нет полного доменного имени. Дополнительные сведения см. в статье [Создание полного доменного имени на портале Azure][fqdn].

- Выделите сетевой адаптер и свяжите его с IP-адресом, подсетью и группой безопасности сети.

- Правила группы безопасности сети по умолчанию запрещают доступ по протоколу RDP. Чтобы включить доступ по протоколу RDP, добавьте в группу безопасности сети правило, которое разрешает входящий трафик через TCP-порт 3389.

## Масштабируемость

Размер виртуальной машины можно увеличивать и уменьшать. Выполните следующую команду интерфейса командной строки Azure, чтобы изменить размер виртуальной машины.

```text
azure vm set -g <<resource-group>> --vm-size <<new-vm-size>
    --boot-diagnostics-storage-uri <<storage-account-uri>> <<vm-name>>
```

При изменении размера виртуальной машины сначала система перезагрузится, а потом переназначит существующие диски данных и операционной системы. Данные на временном диске будут утеряны. Параметр `--boot-diagnostics-storage-uri` позволяет включить [диагностику загрузки][boot-diagnostics] для регистрации ошибок запуска.

Скорее всего, вы не сможете переходить с одной группы SKU к другой (например, от серии А к серии G). Чтобы получить список доступных размеров для виртуальной машины, используйте следующую команду интерфейса командной строки:

```text
azure vm sizes -g <<resource-group>> --vm-name <<vm-name>>
```

Чтобы использовать для виртуальной машины размер, который отсутствует в списке, удалите ее экземпляр и создайте новый. При удалении виртуальной машины виртуальные жесткие диски остаются.

## Доступность

- Для одной виртуальной машины соглашение об уровне обслуживания по доступности не предусмотрено. Чтобы добиться доступности, гарантированной соглашением об уровне обслуживания, разверните несколько виртуальных машин в группе доступности.

- На виртуальную машину могут влиять процедуры [планового][planned-maintenance] и [внепланового][manage-vm-availability] технического обслуживания. Чтобы определить, вызвана ли перезагрузка плановым техническим обслуживанием, используйте [журналы перезагрузки виртуальной машины][reboot-logs].

- Не помещайте одну виртуальную машину в группу доступности. Если вы разместите виртуальную машину в группе доступности, Azure будет рассматривать ее как часть группы с несколькими экземплярами, вследствие чего вы не будете получать предупреждения или уведомления о плановых технических перезагрузках.

- [Служба хранилища Azure][azure-storage] поддерживает виртуальные жесткие диски, которые реплицируются для обеспечения надежности и доступности.

- Чтобы избежать случайной потери данных во время обычной работы (например, из-за ошибки пользователя), реализуйте резервное копирование на определенный момент времени с помощью [моментальных снимков больших двоичных объектов][blob-snapshot] или других инструментов.

## Управляемость

- Выполните следующую команду интерфейса командной строки, чтобы включить диагностику виртуальной машины.
    
    ```text
    azure vm enable-diag <<resource-group>> <<vm-name>>
    ```
    
    Эта команда позволяет включить отслеживание базовых метрик работоспособности, а также ведение журналов инфраструктуры диагностики и диагностику загрузки. Дополнительные сведения см. в статье [Включение мониторинга и диагностики][enable-monitoring].

- Используйте [коллекцию журналов Azure][log-collector] для сбора и отправки журналов в службу хранилища Azure.

- Azure проводит разграничение между состояниями "Остановлена" и "Освобождена". Если виртуальная машина пребывает в состоянии "Остановлена", с вас будет взиматься плата. Если виртуальная машина пребывает в состоянии "Освобождена", плата не взимается. (См. [часто задаваемые вопросы о виртуальной машине Azure][vm-faq].)

    Используйте следующую команду интерфейса командной строки, чтобы освободить виртуальную машину.
    
    ```text
    azure vm deallocate <<resource-group>> <<vm-name>>
    ```
    
    Примечание. Виртуальную машину можно освободить с помощью кнопки **Остановить** на портале Azure. Однако если вы завершите работу в системе Windows (через удаленный рабочий стол), виртуальная машина остановится, но _не_ перейдет в состояние "Освобождена", поэтому с вас по-прежнему будет взиматься плата.

- Если вы удалите виртуальную машину, виртуальные жесткие диски останутся. Это означает, что вы можете удалить виртуальную машину без потери данных. Тем не менее плата за хранение по-прежнему будет взиматься. Чтобы удалить виртуальный жесткий диск, удалите его файл из [хранилища BLOB-объектов][blog-storage].

- Чтобы изменить размер диска ОС, скачайте VHD-файл и воспользуйтесь инструментом [Resize-VHD][Resize-VHD], чтобы изменить размер виртуального жесткого диска. Передайте измененный виртуальный жесткий диск в хранилище BLOB-объектов, а затем удалите экземпляр виртуальной машины и подготовьте новый экземпляр для виртуального жесткого диска с измененным размером.


## Безопасность

- Благодаря [центру безопасности Azure][security-center] можно получить полное представление о состоянии безопасности ваших ресурсов Azure. Центр безопасности отслеживает потенциальные проблемы безопасности (например, в отношении обновления системы, защиты от вредоносных программ и списков управления доступом для конечной точки), а также предоставляет полное представление о состоянии системы безопасности развертывания. **Примечание**. На момент написания статьи центр безопасности все еще находится на стадии предварительной версии.

- [Контроль доступа на основе ролей][rbac] позволяет определить участников команды разработчиков, которые могут управлять развертываемыми ресурсами Azure (виртуальной машиной, сетью, и т. д.).

- Рассмотрите возможность установки [модулей безопасности][security-extensions].

- [Шифрование дисков Azure][disk-encryption] позволяет шифровать диски ОС и данных. **Примечание**. На момент написания статьи шифрование дисков Azure все еще находится на стадии предварительной версии.

## Устранение неполадок

- Чтобы сбросить пароль локального администратора, запустите команду интерфейса командной строки Azure `vm reset-access`.
    
    ```text
    azure vm reset-access -u <<user>> -p <<new-password>> <<resource-group>> <<vm-name>>
    ```
    
- Если виртуальную машину невозможно загрузить, для обнаружения неисправностей можно использовать [диагностику загрузки][boot-diagnostics].

- Просматривать действия подготовки и другие действия виртуальных машин можно с помощью [журналов аудита][audit-logs].

## Выполнение команд интерфейса командной строки Azure (пример)

Следующий пакетный сценарий Windows выполняет команду [интерфейса командной строки Azure][azure-cli], чтобы развернуть один экземпляр виртуальной машины и связанную с ней сеть и ресурсы хранения, как показано на схеме выше.

```bat
ECHO OFF
SETLOCAL

IF "%~1"=="" (
    ECHO Usage: %0 subscription-id
    EXIT /B
    )

:: Set up variables to build out the naming conventions for deploying
:: the cluster

SET LOCATION=eastus2
SET APP_NAME=app1
SET ENVIRONMENT=dev
SET USERNAME=testuser
SET PASSWORD=AweS0me@PW

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1

:: Set up the names of things using recommended conventions
SET RESOURCE_GROUP=%APP_NAME%-%ENVIRONMENT%-rg
SET VM_NAME=%APP_NAME%-vm0

SET IP_NAME=%APP_NAME%-pip
SET NIC_NAME=%VM_NAME%-0nic
SET NSG_NAME=%APP_NAME%-nsg
SET SUBNET_NAME=%APP_NAME%-subnet
SET VNET_NAME=%APP_NAME%-vnet
SET VHD_STORAGE=%VM_NAME:-=%st0
SET DIAGNOSTICS_STORAGE=%VM_NAME:-=%diag

:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see...
SET VM_SIZE=Standard_DS1

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --resource-group %RESOURCE_GROUP% ^
  --subscription %SUBSCRIPTION%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% %POSTFIX%

:: Create the NIC
CALL azure network nic create --network-security-group-name %NSG_NAME% ^
  --public-ip-name %IP_NAME% --subnet-name %SUBNET_NAME% --subnet-vnet-name ^
  %VNET_NAME%  --name %NIC_NAME% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS %POSTFIX% %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS %POSTFIX% %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --nic-name %NIC_NAME% --vnet-name %VNET_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new -g %RESOURCE_GROUP% --vm-name %VM_NAME% ^
  --size-in-gb 128 --vhd-name data1.vhd --storage-account-name %VHD_STORAGE%

:: Allow RDP
CALL azure network nsg rule create -g %RESOURCE_GROUP% --nsg-name %NSG_NAME% ^
  --direction Inbound --protocol Tcp --destination-port-range 3389 ^
  --source-port-range * --priority 100 --access Allow RDPAllow
```

<!-- links -->

[arm-templates]: ../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md
[audit-logs]: https://azure.microsoft.com/ru-RU/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../virtual-machines/virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-storage]: ../storage/storage-introduction.md
[blob-snapshot]: ../storage/storage-blob-snapshots.md
[blog-storage]: ../storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/ru-RU/blog/boot-diagnostics-for-virtual-machines-v2/
[data-disk]: ../virtual-machines/virtual-machines-disks-vhds.md
[disk-encryption]: ../azure-security-disk-encryption.md
[enable-monitoring]: ../azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../virtual-machines/virtual-machines-create-fqdn-on-portal.md
[log-collector]: https://azure.microsoft.com/ru-RU/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../virtual-machines/virtual-machines-manage-availability.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[password-reset]: ../virtual-machines/virtual-machines-windows-reset-password.md
[planned-maintenance]: ../virtual-machines/virtual-machines-planned-maintenance.md
[premium-storage]: ../storage/storage-premium-storage-preview-portal.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[reboot-logs]: https://azure.microsoft.com/ru-RU/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/ru-RU/library/hh848535.aspx
[resource-manager-overview]: ../resource-group-overview.md
[security-center]: https://azure.microsoft.com/ru-RU/services/security-center/
[security-extensions]: ../virtual-machines/virtual-machines-extensions-features.md#security-and-protection
[select-vm-image]: ../virtual-machines/resource-groups-vm-searching.md
[services-by-region]: https://azure.microsoft.com/ru-RU/regions/#services
[static-ip]: ../virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../virtual-machines/virtual-machines-size-specs.md
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-faq]: ../virtual-machines/virtual-machines-questions.md

<!---HONumber=AcomDC_0302_2016-->