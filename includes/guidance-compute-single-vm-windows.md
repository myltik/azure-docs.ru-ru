В этой статье описаны проверенные методы для запуска виртуальной машины Windows в Azure. Основное внимание уделяется вопросам масштабируемости, доступности, управляемости и безопасности.

> [AZURE.NOTE] Azure предоставляет две модели развертывания: с использованием [Azure Resource Manager][resource-manager-overview] и классическую. В этой статье описывается использование модели развертывания c использованием диспетчера ресурсов, которую рекомендует применять для новых развертываний корпорация Майкрософт.

Мы не рекомендуем использовать отдельную виртуальную машину для рабочих нагрузок, так как Azure не поддерживает соглашение об уровне обслуживания (SLA) с гарантией времени непрерывной работы для одной виртуальной машины. Чтобы воспользоваться соглашением об уровне обслуживания, необходимо развернуть несколько виртуальных машин в [группе доступности][availability-set]. Дополнительные сведения см. в статье [Running multiple VMs on Azure for scalability and availability][multi-vm] \(Запуск нескольких виртуальных машин в Azure для обеспечения масштабируемости и доступности).

## Схема архитектуры

Для подготовки виртуальной машины в Azure нужно больше подвижных частей, чем просто для ее создания. К таким элементам относятся вычислительные, сетевые ресурсы и ресурсы хранения.

![[0]][0]

- **Группа ресурсов.** [_Группа ресурсов_][resource-manager-overview] представляет собой контейнер, содержащий связанные ресурсы. Создайте группу ресурсов для хранения ресурсов виртуальной машины.

- **Виртуальная машина**. Виртуальную машину можно подготовить на основе списка опубликованных образов или файла виртуального жесткого диска (VHD), передав его в хранилище BLOB-объектов Azure.

- **Диск ОС.** Этот диск представляет собой VHD-файл, сохраненный в [службе хранилища Azure][azure-storage]. Это означает, что он сохранится, даже если хост-компьютер выйдет из строя.

- **Временный диск.** Виртуальная машина создается с временным диском (в Windows это диск `D:`). Временный диск хранится на физическом диске хост-компьютера. Он _не_ сохраняется в службе хранилища Azure и может быть удален во время перезагрузки и других событий жизненного цикла виртуальной машины. Используйте этот диск только для временных данных, таких как данные страниц или файлы подкачки.

- **Диски данных.** [Диск данных][data-disk] — это постоянный виртуальный жесткий диск, используемый для хранения данных приложений. Диски данных, такие как диск ОС, хранятся в службе хранилища Azure.

- **Виртуальная сеть и подсеть.** Каждая виртуальная машина в Azure развертывается в виртуальной сети, а виртуальные сети разделяются на подсети.

- **Общедоступный IP-адрес.** Общедоступный IP-адрес используется для связи с виртуальной машиной. Например, через удаленный рабочий стол.

- **Сетевой интерфейс (сетевой адаптер)**. Сетевой адаптер обеспечивает взаимодействие виртуальной машины и виртуальной сети.

- **Группа безопасности сети**. [Группа безопасности сети][nsg] разрешает или запрещает прохождение сетевого трафика к подсети. Группу безопасности сети можно связать с отдельной сетевой картой или подсетью. Если она связывается с подсетью, то правила этой группы безопасности сети применяются ко всем виртуальным машинам в подсети.
 
- **Диагностика.** Ведение журналов диагностики очень важно для устранения неполадок виртуальной машины и управления ею.

## Рекомендации

### Рекомендации по виртуальным машинам

- Рекомендуются серии GS и DS, если отсутствует специализированная рабочая нагрузка, например высокопроизводительные вычисления. Дополнительную информацию см. в статье [Размеры виртуальных машин в Azure][virtual-machine-sizes]. При перемещении существующей рабочей нагрузки в Azure выберите начальный размер виртуальной машины, который больше всего соответствует вашим локальным серверам. Затем измерьте производительность фактической рабочей нагрузки по таким ресурсам, как потребление ЦП, памяти и дисковых операций ввода-вывода в секунду, и при необходимости измените размер виртуальной машины. Кроме того, если требуется использовать несколько сетевых карт, то учтите ограничения на количество сетевых карт для каждого размера.

- При подготовке виртуальной машины и других ресурсов необходимо указать расположение. Как правило, необходимо выбрать расположение как можно ближе к внутренним пользователям или клиентам. Однако не для всех расположений доступны все размеры виртуальной машины. Дополнительные сведения см. на странице [Службы по региону][services-by-region]. Чтобы получить список размеров виртуальных машин, доступных в нужном расположении, выполните следующую команду в интерфейсе командной строки Azure (Azure CLI).

    ```
    azure vm sizes --location <location>
    ```

- Дополнительные сведения о выборе опубликованных образов виртуальных машин см. в статье [Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки][select-vm-image].

### Рекомендации по дискам и хранилищам

- Для лучшей производительности дисковых операций ввода-вывода мы рекомендуем использовать [хранилище класса Premium][premium-storage], в котором данные хранятся на твердотельных накопителях (SSD). Цена зависит от размера подготовленного диска. Скорость выполнения операций ввода-вывода и пропускная способность также зависят от размера диска. Поэтому во время подготовки диска следует учитывать все эти факторы.

- Одна учетная запись хранения может поддерживать от 1 до 20 виртуальных машин.

- Добавьте один или несколько дисков данных. При создании виртуальный машины жесткий диск не форматируется. Чтобы отформатировать диск, войдите в систему виртуальной машины.

- При наличии большого количества дисков данных учитывайте общие ограничения на количество операций ввода-вывода для учетной записи хранения. Дополнительные сведения см. в разделе [Ограничения для дисков виртуальной машины][vm-disk-limits].

- Для повышения производительности храните журналы диагностики в отдельной учетной записи хранения. Учетной записи локально избыточного хранилища достаточно для хранения журналов диагностики.

- Если это возможно, устанавливайте приложения на диск данных, а не на диск операционной системы. Однако некоторым устаревшим приложениям может потребоваться установить компоненты на диск C:. В этом случае вы можете [изменить размер диска операционной системы][resize-os-disk] с помощью PowerShell.

### Рекомендации по сети

- Общедоступный IP-адрес может быть динамическим или статическим. По умолчанию используется динамический IP-адрес.

    - Зарезервируйте [статический IP-адрес][static-ip], если вам нужен постоянный IP-адрес (например, для создания записи А в DNS или добавления IP-адреса в список разрешений).

    - Можно также создать полное доменное имя для IP-адреса. Затем вы сможете зарегистрировать в DNS [запись CNAME][cname-record], которая указывает на полное доменное имя. Дополнительные сведения см. в статье [Создание полного доменного имени на портале Azure][fqdn].

- Все группы безопасности сети содержат набор [правил по умолчанию][nsg-default-rules], включая правило, которое блокирует весь входящий интернет-трафик. Правила по умолчанию нельзя удалить, но их можно переопределить другими правилами. Чтобы разрешить трафик Интернета, создайте правила, разрешающие входящий трафик для определенных портов. Например, это может быть порт 80 для протокола HTTP.

- Чтобы включить доступ по протоколу RDP, добавьте правило группы безопасности сети, которое разрешает входящий трафик через TCP-порт 3389.

## Вопросы масштабируемости

- Размер виртуальной машины можно [увеличивать и уменьшать][vm-resize].

- Для горизонтального развертывания поместите две или больше виртуальных машин в группу доступности, регулируемую балансировщиком нагрузки. Дополнительные сведения см. в статье [о запуске нескольких виртуальных машин Windows в Azure][multi-vm].

## Вопросы доступности

- Как отмечалось выше, для одной виртуальной машины не действует соглашение об уровне обслуживания. Чтобы воспользоваться соглашением об уровне обслуживания, необходимо развернуть несколько виртуальных машин в группе доступности.

- На виртуальную машину могут влиять процедуры [планового][planned-maintenance] и [внепланового][manage-vm-availability] технического обслуживания. Чтобы определить, вызвана ли перезагрузка плановым техническим обслуживанием, изучите [журналы перезагрузки виртуальной машины][reboot-logs].

- Виртуальные жесткие диски хранятся в [службе хранилища Azure][azure-storage], которая реплицируется для обеспечения надежности и доступности.

- Чтобы избежать случайной потери данных во время обычной работы (например, из-за ошибки пользователя), следует реализовать создание резервных копий на определенный момент времени с помощью [моментальных снимков больших двоичных объектов][blob-snapshot] или других инструментов.

## Вопросы управляемости

- **Группы ресурсов.** Тесно связанные ресурсы с одинаковым жизненным циклом следует помещать в одну [группу ресурсов][resource-manager-overview]. Группы ресурсов позволяют развертывать и отслеживать несколько ресурсов как единое целое, а также консолидировать счета по группам ресурсов. Можно также удалить ресурсы в виде набора, что очень удобно для тестирования развернутых служб. Присваивайте ресурсам информативные имена. Это упростит поиск определенного ресурса и понимание его роли. См. статью [Recommended naming conventions for Azure resources][naming conventions] \(Рекомендуемые соглашения об именовании для ресурсов Azure).

- **Диагностика виртуальной машины.** Включите отслеживание и диагностику, в том числе базовые метрики работоспособности, а также ведение журналов инфраструктуры диагностики и [диагностику загрузки][boot-diagnostics]. Если виртуальную машину невозможно загрузить, для обнаружения неисправностей можно использовать диагностику загрузки. Дополнительные сведения см. в статье [Включение мониторинга и диагностики][enable-monitoring]. Используйте расширение [Коллекция журналов Azure][log-collector], чтобы собирать журналы платформы Azure и отправлять их в службу хранилища Azure.

    Выполните следующую команду интерфейса командной строки, чтобы включить диагностику.

    ```text
    azure vm enable-diag <resource-group> <vm-name>
     ```

- **Остановка виртуальной машины.** Azure различает состояния "Остановлена" и "Освобождена". Если виртуальная машина пребывает в состоянии "Остановлена", с вас будет взиматься плата. Если виртуальная машина находится в состоянии "Освобождена", плата не взимается.

    Используйте следующую команду интерфейса командной строки, чтобы освободить виртуальную машину.

    ```text
    azure vm deallocate <resource-group> <vm-name>
    ```

    Также это можно сделать с помощью кнопки **Остановить** на портале Azure. Но если вы войдете в виртуальную машину и завершите работу операционной системы, виртуальная машина будет остановлена, а _не_ освобождена, поэтому с вас по-прежнему будет взиматься плата.

- **Удаление виртуальной машины.** Если вы удалите виртуальную машину, виртуальные жесткие диски останутся. Это означает, что вы можете удалить виртуальную машину без потери данных. Тем не менее плата за хранение по-прежнему будет взиматься. Чтобы удалить виртуальный жесткий диск, удалите соответствующий файл из [хранилища BLOB-объектов][blob-storage].

  Для предотвращения случайного удаления используйте [блокировку ресурсов][resource-lock], чтобы заблокировать всю группу или отдельные ресурсы (например, виртуальную машину).

## Вопросы безопасности

- Благодаря [центру безопасности Azure][security-center] можно получить полное представление о состоянии безопасности ваших ресурсов Azure. Центр безопасности отслеживает потенциальные проблемы безопасности (например, в отношении обновлений системы и защиты от вредоносных программ), а также обеспечивает полное представление о состоянии системы безопасности развертывания.

    - Центр безопасности настраивается на уровне подписки Azure. Включите сбор данных безопасности, как описано в разделе [об использовании центра безопасности].

    - Когда сбор данных включен, центр безопасности автоматически проверяет все виртуальные машины, созданные для этой подписки.

- **Управление исправлениями.** Если эта функция включена, то Центр безопасности проверяет, отсутствуют ли какие-либо обновления для системы безопасности и критические обновления. Установите для виртуальной машины [параметры групповой политики][group-policy], разрешающие автоматическое обновление системы.

- **Защита от вредоносных программ.** Если эта функция включена, то Центр безопасности проверяет, установлена ли антивредоносное ПО. Центр безопасности позволяет также установить антивредоносное ПО с помощью портала Azure.

- Используйте [управление доступом на основе ролей][rbac] \(RBAC) для управления доступом к развертываемым ресурсам Azure. RBAC позволяет назначить роли авторизации участникам команды DevOps. Например, роль "Читатель" позволяет просматривать ресурсы Azure, но не позволяет создавать и удалять их или управлять ими. Некоторые роли относятся к определенным типам ресурсов Azure. Например, роль "Участник виртуальных машин" позволяет перезапустить виртуальную машину или отменить ее выделение, сбросить пароль администратора, создать новую виртуальную машину и т. д. Для этой архитектуры могут оказаться полезными и другие [встроенные роли RBAC][rbac-roles], например [Пользователь DevTest Labs][rbac-devtest] и [Участник сетей][rbac-network]. Пользователю можно назначить несколько ролей. Можно также создать пользовательские роли, чтобы более детально настроить разрешения.

    > [AZURE.NOTE] RBAC не ограничивает действия, которые может выполнять пользователь, вошедший в виртуальную машину. Эти разрешения определяются типом учетной записи в гостевой ОС.

- Чтобы сбросить пароль локального администратора, выполните команду Azure CLI `vm reset-access`.

    ```text
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
    ```

- Просматривать действия по подготовке и другие события для виртуальной машины можно с помощью [журналов аудита][audit-logs].

- Если нужно шифровать диски ОС и диски данных, рассмотрите возможность применения [шифрования дисков Azure][disk-encryption].

## Компоненты решения

Воспользуйтесь примером сценария [Deploy-ReferenceArchitecture.ps1][solution-script], чтобы реализовать архитектуру согласно рекомендациям, приведенным в этой статье. В этом сценарии используются шаблоны [Resource Manager][ARM-Templates]. Они предоставляются в виде набора фундаментальных стандартных блоков, каждый из которых выполняет определенное действие, например создает виртуальную сеть или настраивает группу безопасности сети. Задача сценария — координировать развертывание шаблона.

В шаблонах используются параметры, содержащихся в отдельных JSON-файлах. Можно изменить параметры в этих файлах, чтобы настроить развертывание в соответствии со своими требованиями. При этом нет необходимости изменять сами шаблоны. Обратите внимание, что не следует изменять схемы объектов в файлах параметров.

При изменении шаблонов создавайте объекты, которые следуют соглашениям об именовании, описанным в статье [Recommended naming conventions for Azure resources][naming conventions] \(Рекомендуемые соглашения об именовании для ресурсов Azure).

Для создания виртуальной машины и окружающей инфраструктуры сценарий ссылается на следующие файлы параметров.

- **[virtualNetwork.parameters.json][vnet-parameters]**. Этот файл определяет параметры виртуальной сети, например имя, адресное пространство, подсети и адреса всех необходимых DNS-серверов. Обратите внимание, что адреса подсети должны быть включены в адресное пространство виртуальной сети.

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/windows/virtualNetwork.parameters.json#L4-L21 -->
	```json
  "parameters": {
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg",
        "addressPrefixes": [
          "172.17.0.0/16"
        ],
        "subnets": [
          {
            "name": "ra-single-vm-sn",
            "addressPrefix": "172.17.0.0/24"
          }
        ],
        "dnsServers": [ ]
      }
    }
  }
	```

- **[networkSecurityGroups.parameters.json][nsg-parameters]**. Этот файл содержит определения групп безопасности сети и их правил. Параметр `name` в блоке `virtualNetworkSettings` задает виртуальную сеть, к которой подключена группа безопасности сети. Параметр `subnets` в блоке `networkSecurityGroupSettings` идентифицирует все подсети в виртуальной сети, к которым применяются правила группы безопасности сети. Это должны быть элементы, определенные в файле **virtualNetwork.parameters.json**.

	Обратите внимание, что правило безопасности по умолчанию, показанное в примере, позволяет пользователю подключаться к виртуальной машине через подключение к удаленному рабочему столу. Можно открыть дополнительные порты (или запретить доступ через определенные порты), добавив дополнительные элементы в массив `securityRules`.

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/windows/networkSecurityGroups.parameters.json#L4-L36 -->
	```json
  "parameters": {
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg"
      }
    },
    "networkSecurityGroupsSettings": {
      "value": [
        {
          "name": "ra-single-vm-nsg",
          "subnets": [
            "ra-single-vm-sn"
          ],
          "networkInterfaces": [
          ],
          "securityRules": [
            {
              "name": "RDPAllow",
              "direction": "Inbound",
              "priority": 100,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "access": "Allow",
              "protocol": "Tcp"
            }
          ]
        }
      ]
    }
  }
	```

- **[virtualMachineParameters.json][vm-parameters]**. Этот файл определяет параметры виртуальной машины, включая ее имя и размер, учетные данные безопасности для пользователя-администратора, создаваемые диски и учетные записи хранения для хранения этих дисков.

	В разделе `imageReference` необходимо указать образ. Значения, приведенные ниже, позволяют создать виртуальную машину с помощью последней сборки Windows Server 2012 R2 Datacenter. Для получения списка всех доступных образов Windows в регионе (в примере используется регион "западная часть США") можно использовать следующую команду в интерфейсе командной строки Azure.

	```text
	azure vm image list westus MicrosoftWindowsServer WindowsServer
	```

	Параметр `subnetName` в разделе `nics` задает подсеть для виртуальной машины. Точно так же параметр `name` в разделе `virtualNetworkSettings` определяет используемую виртуальную сеть. Эти значения должны быть именами подсети и виртуальной сети, которые были определены в файле **virtualNetwork.parameters.json**.

	Можно создать несколько виртуальных машин в одной учетной записи хранения или указать для каждой отдельную учетную запись, изменив параметры в разделе `buildingBlockSettings`. При создании нескольких виртуальных машин необходимо также указать в разделе `availabilitySet` имя группы доступности, которую вы хотите использовать или создать.

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/windows/virtualMachine.parameters.json#L4-L64 -->
	```json
   "parameters": {
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-single-vm",
        "computerNamePrefix": "cn",
        "size": "Standard_DS1_v2",
        "osType": "windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "sshPublicKey": "",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "true",
            "subnetName": "ra-single-vm-sn",
            "privateIPAllocationMethod": "dynamic",
            "publicIPAllocationMethod": "dynamic",
            "enableIPForwarding": false,
            "dnsServers": [
            ],
            "isPrimary": "true"
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 2,
          "properties": {
            "diskSizeGB": 128,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [ ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": ""
        }
      }
    },
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg"
      }
    },
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 1,
        "vmCount": 1,
        "vmStartIndex": 0
      }
    }
  }
	```

## Развертывание решения

Перед использованием этого решения предполагается, что выполнены следующие предварительные условия.

- У вас есть подписка Azure, в которой можно создать группы ресурсов.

- Вы скачали и установили последнюю сборку Azure PowerShell. Соответствующие инструкции см. [здесь][azure-powershell-download].

Чтобы выполнить сценарий, который развертывает решение, сделайте следующее.

1. Создайте папку, которая содержит подпапки с именами `Scripts` и `Templates`.

2. В папке "Шаблоны" создайте вложенную папку с именем Windows.

3. Скачайте файл [Deploy-ReferenceArchitecture.ps1][solution-script] в папку "Сценарии".

4. Скачайте приведенные ниже файлы в папку Шаблоны/Windows:

	- [virtualNetwork.parameters.json;][vnet-parameters]

	- [networkSecurityGroups.parameters.json][nsg-parameters]

	- [virtualMachineParameters.json.][vm-parameters]

5. Откройте файл Deploy-ReferenceArchitecture.ps1 в папке "Сценарии" для редактирования и измените следующую строку, чтобы указать группу ресурсов, которую следует создать или использовать для хранения виртуальных машин и ресурсов, созданных с помощью сценария.

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/Deploy-ReferenceArchitecture.ps1#L37 -->
	```powershell
	$resourceGroupName = "ra-single-vm-rg"
	```
6. Измените каждый из JSON-файлов в папке Шаблоны/Windows, задав параметры для виртуальной сети, группы безопасности сети и виртуальной машины, как описано выше в разделе "Компоненты решения".

	>[AZURE.NOTE] Обязательно присвойте параметру `resourceGroup` в разделе `virtualNetworkSettings` файла virtualMachineParameters.json то же значение, которое было указано в файле сценария Deploy-ReferenceArchitecture.ps1.

7. Откройте окно PowerShell, перейдите в папку "Сценарии" и выполните следующую команду.

	```powershell
	.\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows
	```

	Замените `<subscription id>` идентификатором своей подписки Azure.

	Для `<location>` укажите регион Azure, например `eastus` или `westus`.

8. После выполнения сценария с помощью портала Azure убедитесь, что сеть, группа безопасности сети и виртуальная машина были успешно созданы.

## Дальнейшие действия

Чтобы [соглашение об уровне обслуживания для виртуальных машин][vm-sla] вступило в силу, необходимо развернуть не менее двух экземпляров в группе доступности. Дополнительные сведения см. в статье [Running multiple VMs on Azure for scalability and availability][multi-vm] \(Запуск нескольких виртуальных машин в Azure для обеспечения масштабируемости и доступности).

<!-- links -->

[audit-logs]: https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[group-policy]: https://technet.microsoft.com/ru-RU/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
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
[reboot-logs]: https://azure.microsoft.com/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/ru-RU/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[security-center]: https://azure.microsoft.com/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[об использовании центра безопасности]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/Deploy-ReferenceArchitecture.ps1
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/parameters/windows/virtualNetwork.parameters.json
[nsg-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/parameters/windows/networkSecurityGroups.parameters.json
[vm-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/parameters/windows/virtualMachine.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Архитектура с одной виртуальной машиной Windows в Azure"

<!---HONumber=AcomDC_0831_2016-->