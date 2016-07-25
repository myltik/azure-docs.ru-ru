В этой статье описаны проверенные методы для запуска виртуальной машины Windows в Azure. Основное внимание уделяется вопросам масштабируемости, доступности, управляемости и безопасности.

> [AZURE.NOTE] Azure предоставляет две модели развертывания: с использованием [Resource Manager][resource-manager-overview] и классическую. В этой статье описывается использование модели развертывания c использованием диспетчера ресурсов, которую рекомендует применять для новых развертываний корпорация Майкрософт.

Мы не рекомендуем использовать отдельную виртуальную машину для рабочих нагрузок рабочей среды, так как Azure не предусматривает поддержку соглашения об уровне обслуживания с гарантией времени непрерывной работы при запуске одной виртуальной машины. Чтобы воспользоваться соглашением об уровне обслуживания, необходимо развернуть несколько виртуальных машин в группе доступности. Дополнительные сведения см. в разделе [Running multiple VMs on Azure for scalability and availability][multi-vm] (Запуск нескольких виртуальных машин в Azure для обеспечения масштабируемости и доступности).

## Схема архитектуры

Для подготовки виртуальной машины в Azure нужно больше подвижных частей, чем просто для ее создания. К таким элементам относятся вычислительные, сетевые ресурсы и ресурсы хранения.

![IaaS: одна виртуальная машина](./media/guidance-blueprints/compute-single-vm.png)

- **Группа ресурсов.** [_Группа ресурсов_][resource-manager-overview] представляет собой контейнер, содержащий связанные ресурсы. Создайте группу ресурсов для хранения ресурсов виртуальной машины.

- **Виртуальная машина**. Виртуальную машину можно подготовить на основе списка опубликованных образов или VHD-файла, переданного в хранилище BLOB-объектов Azure.

- **Диск ОС.** Этот диск представляет собой VHD-файл, сохраненный в [службе хранилища Azure][azure-storage]. Это означает, что он сохранится, даже если хост-компьютер выйдет из строя.

- **Временный диск.** Виртуальная машина создается с временным диском (в Windows это диск `D:`). Временный диск хранится на физическом диске хост-компьютера. Он _не_ сохраняется в службе хранилища Azure и может быть удален во время перезагрузки и других событий жизненного цикла виртуальной машины. Используйте этот диск только для временных данных, таких как данные страниц или файлы подкачки.

- **Диски данных.** [Диск данных][data-disk] — это постоянный виртуальный жесткий диск, используемый для хранения данных приложений. Диски данных, такие как диск ОС, хранятся в службе хранилища Azure.

- **Виртуальная сеть и подсеть.** Каждая виртуальная машина в Azure развертывается в виртуальной сети, которая затем разделяется на подсети.

- **Общедоступный IP-адрес.** Общедоступный IP-адрес используется для связи с виртуальной машиной. Например, через удаленный рабочий стол.

- **Сетевой интерфейс (сетевая карта)**. Сетевой адаптер обеспечивает взаимодействие виртуальной машины и виртуальной сети.

- **Группа безопасности сети**. [Группа безопасности сети][nsg] разрешает или запрещает прохождение сетевого трафика к подсети. Группу безопасности сети можно связать с отдельной сетевой картой или подсетью. Если она связывается с подсетью, то правила этой группы безопасности сети применяются ко всем виртуальным машинам в подсети.
 
- **Диагностика.** Ведение журналов диагностики очень важно для устранения неполадок виртуальной машины и управления ею.

## Рекомендации

### Рекомендации по виртуальным машинам

- Рекомендуются серии GS и DS, если отсутствует специализированная рабочая нагрузка, например высокопроизводительные вычисления. Дополнительную информацию см. в статье [Размеры виртуальных машин][virtual-machine-sizes]. При перемещении существующей рабочей нагрузки в Azure выберите начальный размер виртуальной машины, который больше всего соответствует вашим локальным серверам. Затем измерьте производительность фактической рабочей нагрузки с точки зрения потребляемых ресурсов ЦП, памяти и операций ввода-вывода, после чего измените размер, если это необходимо. Кроме того, если требуется использовать несколько сетевых карт, то учтите ограничения на количество сетевых карт для каждого размера.

- При подготовке виртуальной машины и других ресурсов необходимо указать расположение. Как правило, необходимо выбрать расположение как можно ближе к внутренним пользователям или клиентам. Однако не для всех расположений доступны все размеры виртуальной машины. Дополнительные сведения см. в разделе [Службы по региону][services-by-region]. Чтобы получить список размеров виртуальной машины, доступных в заданном расположении, выполните следующую команду в интерфейсе командной строки Azure (Azure CLI).

    ```
    azure vm sizes --location <location>
    ```

- Дополнительные сведения о выборе опубликованных образов виртуальной машины см. в статье [Просмотр и выбор образов виртуальных машин Azure с помощью оболочки Windows PowerShell и инфраструктуры Azure CLI][select-vm-image].

### Рекомендации по дискам и хранилищам

- Для наилучшей производительности дисковых операций ввода-вывода мы рекомендуем [хранилище класса Premium][premium-storage], в котором для хранения данных используются твердотельные накопители (SSD). Цена зависит от размера подготовленного диска. Скорость выполнения операций ввода-вывода и пропускная способность (т. е. скорость передачи данных) также зависят от размера диска. Поэтому во время подготовки диска следует учитывать все эти факторы.

- Добавьте один или несколько дисков данных. При создании виртуальный машины жесткий диск не форматируется. Чтобы отформатировать диск, войдите в систему виртуальной машины.

- При наличии большого количества дисков данных учитывайте общие ограничения на количество операций ввода-вывода для учетной записи хранения. Дополнительные сведения см. в разделе [Ограничения для дисков виртуальной машины][vm-disk-limits].

- Для повышения производительности храните журналы диагностики в отдельной учетной записи хранения. Учетной записи локально избыточного хранилища достаточно для хранения журналов диагностики.

- Если это возможно, устанавливайте приложения на диск данных, а не на диск операционной системы. Однако некоторым устаревшим приложениям может потребоваться установить компоненты на диск C:. В этом случае вы можете [изменить размер диска ОС][resize-os-disk] с помощью PowerShell.

### Рекомендации по сети

- Общедоступный IP-адрес может быть динамическим или статическим. По умолчанию используется динамический IP-адрес.

    - Зарезервируйте [статический IP-адрес][static-ip], если вам нужен фиксированный IP-адрес. Например, для создания записи А в DNS или добавления IP-адреса в список разрешений.

    - Можно также создать полное доменное имя для IP-адреса. Затем можно зарегистрировать в DNS [запись CNAME][cname-record], которая указывает на полное доменное имя. Дополнительные сведения см. в статье [Создание полного доменного имени на портале Azure][fqdn].

- Все группы безопасности сети содержат набор [правил по умолчанию][nsg-default-rules], включая правило, которое блокирует весь входящий трафик Интернета. Правила по умолчанию нельзя удалить, но их можно переопределить другими правилами. Чтобы разрешить трафик Интернета, создайте правила, разрешающие входящий трафик для определенных портов. Например, это может быть порт 80 для протокола HTTP.

- Чтобы включить доступ по протоколу RDP, добавьте правило группы безопасности сети, которое разрешает входящий трафик через TCP-порт 3389.

## Вопросы масштабируемости

- Размер виртуальной машины можно [увеличивать и уменьшать][vm-resize].

- Для горизонтального развертывания поместите две или больше виртуальных машин в группу доступности, регулируемую балансировщиком нагрузки. Дополнительные сведения см. в разделе [Running multiple VMs on Azure for scalability and availability][multi-vm] (Запуск нескольких виртуальных машин в Azure для обеспечения масштабируемости и доступности).

## Вопросы доступности

- Как отмечалось выше, для одной виртуальной машины не действует соглашение об уровне обслуживания. Чтобы воспользоваться соглашением об уровне обслуживания, необходимо развернуть несколько виртуальных машин в группе доступности.

- На виртуальную машину могут влиять процедуры [планового][planned-maintenance] и [внепланового][manage-vm-availability] технического обслуживания. Чтобы определить, вызвана ли перезагрузка плановым техническим обслуживанием, используйте [журналы перезагрузки виртуальной машины][reboot-logs].

- [Служба хранилища Azure][azure-storage] поддерживает виртуальные жесткие диски, которые реплицируются для обеспечения надежности и доступности.

- Чтобы избежать случайной потери данных во время обычной работы (например, из-за ошибки пользователя), следует реализовать создание резервных копий на определенный момент времени с помощью [моментальных снимков больших двоичных объектов][blob-snapshot] или других инструментов.

## Вопросы управляемости

- **Группы ресурсов**. Поместите тесно связанные ресурсы с одинаковым жизненным циклом в одну [группу ресурсов][resource-manager-overview]. Группы ресурсов позволяют развертывать и отслеживать ресурсы в виде группы и получать сводные счета за затраты на группу ресурсов. Можно также удалить ресурсы в виде набора, что очень удобно для тестирования развернутых служб. Присваивайте ресурсам информативные имена. Это упростит поиск определенного ресурса и понимание его роли. Ознакомьтесь с разделом [Recommended naming conventions for Azure resources][naming conventions] (Рекомендуемые соглашения об именовании ресурсов Azure).

- **Диагностика виртуальной машины**. Включите отслеживание и диагностику, включая базовые метрики работоспособности, а также ведение журналов инфраструктуры диагностики и [диагностику загрузки][boot-diagnostics]. Если виртуальную машину невозможно загрузить, для обнаружения неисправностей можно использовать диагностику загрузки. Дополнительные сведения см. в статье [Включение мониторинга и диагностики][enable-monitoring]. Используйте расширение [для сбора журналов Azure][log-collector], чтобы собирать журналы платформы Azure и отправлять их в службу хранилища Azure.

    Выполните следующую команду интерфейса командной строки, чтобы включить диагностику.

    ```text
    azure vm enable-diag <resource-group> <vm-name>
     ```

- **Остановка виртуальной машины**. Azure проводит разграничение между состояниями "Остановлена" и "Освобождена". Если виртуальная машина пребывает в состоянии "Остановлена", с вас будет взиматься плата. Если виртуальная машина пребывает в состоянии "Освобождена", плата не взимается.

    Используйте следующую команду интерфейса командной строки, чтобы освободить виртуальную машину.

    ```text
    azure vm deallocate <resource-group> <vm-name>
    ```

    Отменить выделение виртуальной машины можно с помощью кнопки **Остановить** на портале Azure. Однако если вы завершите работу в операционной системе, то виртуальная машина остановится, но ее выделение _не_ будет отменено, поэтому с вас по-прежнему будет взиматься плата.

- **Удаление виртуальной машины**. Если вы удалите виртуальную машину, виртуальные жесткие диски останутся. Это означает, что вы можете удалить виртуальную машину без потери данных. Тем не менее плата за хранение по-прежнему будет взиматься. Чтобы удалить виртуальный жесткий диск, удалите его файл из [хранилища BLOB-объектов][blob-storage].

  Чтобы предотвратить случайное удаление, используйте [блокировку ресурсов][resource-lock], чтобы заблокировать всю группу ресурсов или отдельные ресурсы, например виртуальную машину.



## Вопросы безопасности

- Благодаря [центру безопасности Azure][security-center] можно получить полное представление о состоянии безопасности ваших ресурсов Azure. Центр безопасности отслеживает потенциальные проблемы безопасности (например, в отношении обновлений системы и защиты от вредоносных программ), а также обеспечивает полное представление о состоянии системы безопасности развертывания.

    - Центр безопасности настраивается на уровне подписки Azure. Включите сбор данных безопасности, как описано в разделе [Использование Центра безопасности].
    - После включения сбора данных Центр безопасности автоматически проверяет все виртуальные машины, созданные для этой подписки.

- **Управление исправлениями**. Если эта функция включена, то Центр безопасности проверяет, отсутствуют ли какие-либо обновления для системы безопасности и критические обновления. Используйте [параметры групповой политики][group-policy] виртуальной машины, чтобы включить автоматическое обновление системы.

- **Защита от вредоносных программ**. Если эта функция включена, то Центр безопасности проверяет, установлена ли антивредоносное ПО. Центр обеспечения безопасности также позволяет установить антивредоносное ПО с помощью портала Azure.

- Используйте [управление доступом на основе ролей][rbac] (RBAC) для управления доступом к разворачиваемым ресурсам Azure. RBAC позволяет назначить роли авторизации участникам команды DevOps. Например, роль "Читатель" позволяет просматривать ресурсы Azure, но не позволяет создавать и удалять их или управлять ими. Некоторые роли относятся к определенным типам ресурсов Azure. Например, роль "Участник виртуальных машин" позволяет перезапустить виртуальную машину или отменить ее выделение, сбросить пароль администратора, создать новую виртуальную машину и т. д. К другим [встроенным ролям RBAC][rbac-roles], которые могут быть полезными в этой эталонной архитектуре, относятся [Пользователь лаборатории для разработки и тестирования][rbac-devtest] и [Участник сети][rbac-network]. Пользователю можно назначить несколько ролей. Можно также создать пользовательские роли, чтобы более детально настроить разрешения.

    > [AZURE.NOTE] RBAC не ограничивает действия, которые может выполнять пользователь, вошедший в виртуальную машину. Эти разрешения определяются типом учетной записи в гостевой ОС.

- Чтобы сбросить пароль локального администратора, выполните команду интерфейса командной строки Azure (Azure CLI) `vm reset-access`.

    ```text
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
    ```

- Просматривать действия по подготовке и другие события для виртуальной машины можно с помощью [журналов аудита][audit-logs].

- Если нужно шифровать диски ОС и диски данных, рассмотрите применение [шифрования дисков Azure][disk-encryption].

## Пример сценария развертывания

Следующий пакетный сценарий Windows выполняет команды [Azure CLI][azure-cli], чтобы развернуть один экземпляр виртуальной машины и связанную с ним сеть и ресурсы хранения, как показано на схеме выше.

Этот сценарий использует соглашения об именовании, описанные в разделе [Recommended naming conventions for Azure resources][naming conventions] (Рекомендуемые соглашения об именовании для ресурсов Azure).

```bat
ECHO OFF
SETLOCAL

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Set up variables for deploying resources to Azure.
:: Change these variables for your own deployment.

:: The APP_NAME variable must not exceed 4 characters in size.
:: If it does the 15 character size limitation of the VM name may be exceeded.

SET APP_NAME=app1
SET LOCATION=eastus2
SET ENVIRONMENT=dev
SET USERNAME=testuser


:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see:
::   https://azure.microsoft.com/documentation/articles/virtual-machines-size-specs/
:: To see the VM sizes available in a region:
:: 	azure vm sizes --location <location>
SET VM_SIZE=Standard_DS1

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

IF "%2"=="" (
    ECHO Usage: %0 subscription-id admin-password
    EXIT /B
    )

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1
SET PASSWORD=%2

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

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% --location %LOCATION% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% --location %LOCATION% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --network-security-group-name %NSG_NAME% ^
  %POSTFIX%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% --location %LOCATION% %POSTFIX%

:: Create the NIC
CALL azure network nic create --public-ip-name %IP_NAME% --subnet-name ^
  %SUBNET_NAME% --subnet-vnet-name %VNET_NAME%  --name %NIC_NAME% --location ^
  %LOCATION% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS --location %LOCATION% %POSTFIX% ^
  %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS --location %LOCATION% %POSTFIX% ^
  %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --vnet-name %VNET_NAME% --nic-name %NIC_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" --location %LOCATION% ^
  %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new --vm-name %VM_NAME% --size-in-gb 128 --vhd-name ^
  data1.vhd --storage-account-name %VHD_STORAGE% %POSTFIX%

:: Allow RDP
CALL azure network nsg rule create --nsg-name %NSG_NAME% --direction Inbound ^
  --protocol Tcp --destination-port-range 3389 --source-port-range * ^
  --priority 100 --access Allow RDPAllow %POSTFIX%
```

## Дальнейшие действия

Чтобы [соглашение об уровне обслуживания для виртуальных машин][vm-sla] вступило в силу, необходимо развернуть два или более экземпляров в группе доступности. Дополнительные сведения см. в разделе [Running multiple VMs on Azure for scalability and availability][multi-vm] (Запуск нескольких виртуальных машин в Azure для обеспечения масштабируемости и доступности).

<!-- links -->

[arm-templates]: ../articles/virtual-machines/virtual-machines-windows-cli-deploy-templates.md
[audit-logs]: https://azure.microsoft.com/ru-RU/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/ru-RU/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[group-policy]: https://technet.microsoft.com/ru-RU/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/ru-RU/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/ru-RU/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/ru-RU/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/ru-RU/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[security-center]: https://azure.microsoft.com/ru-RU/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/ru-RU/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[Использование Центра безопасности]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/ru-RU/support/legal/sla/virtual-machines/v1_0/

<!---HONumber=AcomDC_0713_2016-->