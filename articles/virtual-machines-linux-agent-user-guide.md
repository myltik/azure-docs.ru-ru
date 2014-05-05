<properties linkid="manage-linux-howto-linux-agent" urlDisplayName="Руководство по агенту Linux" pageTitle="Руководство пользователя агента Linux для Azure" metaKeywords="" description="Узнайте, как установить и настроить агент Linux (waagent) для управления взаимодействием виртуальной машины с Azure Fabric Controller." metaCanonical="" services="virtual-machines" documentationCenter="" title="Руководство пользователя агента Linux для Azure" authors="" solutions="" manager="" editor="" />





#Руководство пользователя агента Linux для Azure

##Введение

Агент Linux для Azure (waagent) управляет взаимодействием виртуальной машины с Azure Fabric Controller. Он предоставляет следующие функциональные возможности для развернутых приложений Linux IaaS:

* **Подготовка образа**
  - Создание учетной записи пользователя
  - Настройка типов аутентификации SSH
  - Развертывание открытых ключей SSH и пар ключей
  - Настройка имени узла
  - Публикация имени узла DNS платформы
  - Отчет с отпечатком ключа узла SSH для платформы
  - Управление диском ресурсов
  - Форматирование и подключение диска ресурсов
  - Настройка пространства подкачки
* **Работа в сети**
  - Управляет маршрутами для улучшения совместимости с DHCP-серверами платформы
  - Обеспечивает стабильность имени сетевого интерфейса
* **Ядро**
  - Настройка виртуальной NUMA
  - Использование энтропии Hyper-V для /dev/random
  - Настройка таймаутов SCSI для корневого устройства (может быть удаленным)
* **Диагностика**
  - Перенаправление консоли на последовательный порт
* **Развернутые приложения SCVMM**
    - Обнаружение и загрузка агента VMM для Linux при работе в системе
      Среда Center Virtual Machine Manager 2012 R2


Поток информации от платформы к агенту передается по двум каналам:

* Прилагаемый boot-time DVD для развернутых приложений IaaS. На этом DVD содержится OVF-совместимый файл конфигурации, включающий всю информацию для подготовки кроме самих пар ключей SSH.

* Для получения развертывания и настройки топологии используется конечная точка TCP с REST API.

###Получение агента Linux
Последнюю версию агента Linux можно напрямую получить:

- [У различных поставщиков дистрибутивов, работающих с Linux в Azure](http://support.microsoft.com/kb/2805216)
- или из репозитория [Github Open Source для Linux-агента Azure](https://github.com/WindowsAzure/WALinuxAgent)


###Поддерживаемые дистрибутивы Linux
* CentOS 6.2+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP2+
* Oracle Linux 6.4+

Другие поддерживаемые системы:

* FreeBSD 9+ (WALinuxAgent v2.0.0+)


###Требования

Для правильного функционирования Waagent требуются определенные пакеты системы:

* Python 2.5+
* Openssl 1.0+
* Openssh 5.3+
* Утилиты файловой системы: sfdisk fdisk, mkfs
* Средства работы с паролями: chpasswd sudo
* Инструменты обработки текста: sed, grep
* Сетевые средства: ip-route

##Установка

Установка с помощью RPM или DEB пакета из репозитория пакета дистрибутива является предпочтительным способом установки и обновления Linux Azure Windows Azure.

При установке вручную следует скопировать waagent в /usr/sbin/waagent и произвести установку, выполнив: 

	# sudo chmod 755 /usr/sbin/waagent
	# /usr/sbin/waagent -install -verbose

Файл журнала агента хранится в /var/log/waagent.log.


##Параметры командной строки

###Флаги

- verbose: расширить указанную команду
- force: пропустить интерактивные подтверждения для некоторых команд

###Команды

- help: список поддерживаемых команд и флагов.

- install: ручная установка агента
 * Проверяет систему на наличие обязательных зависимостей

 * Создает сценарий инициализации SysV (/etc/init.d/waagent), файл конфигурации logrotate (/etc/logrotate.d/waagent и настраивает образ, чтобы запустить сценарий инициализации во время загрузки

 * Записывает шаблон файла конфигурации в /etc/waagent.conf

 * Существующий файл конфигурации перемещается в /etc/waagent.conf.old

 * Определяет версию ядра и при необходимости применяет метод обхода VNUMA

 * Перемещает правила udev, которые могут конфликтовать сетью (/lib/udev/rules.d/75-persistent-net-generator.rules, /etc/udev/rules.d/70-persistent-net.rules) в /var/lib/waagent/  

- uninstall: удаляет waagent и связанные файлы
 * Отменяет регистрацию сценария инициализации в системе и удаляет его

 * Удаляет конфигурацию logrotate и файл конфигурации waagent в /etc/waagent.conf

 * Восстанавливает все udev-правила, которые были перемещены во время установки

 * Автоматический возврат обходного пути VNUMA не поддерживается, необходимо изменить файлы конфигурации GRUB вручную, чтобы при необходимости повторно включить NUMA.

- deprovision: попытка очистки системы и выполнение действий для ее повторной подготовки. При выполнении этой операции были удалены следующие компоненты:
 * Все ключи узла SSH (если в файле конфигурации для Provisioning.RegenerateSshHostKeyPair указано значение "y")

 * Настройка имени сервера в /etc/resolv.conf

 * Корневой пароль из /etc/shadow (если в файле конфигурации для Provisioning.DeleteRootPassword указано значение "y")

 * Кэшированные аренды DHCP-клиентов

 * Возвращает имя узла localhost.localdomain

 **Предупреждение.** Отмена подготовки не гарантирует, что из образа будет удалена вся конфиденциальная информация и что он будет готов к повторному распространению.

- deprovision+user: выполняет все действия, указанные для команды -deprovision (см. выше), а также удаляет последнюю подготовленную учетную запись пользователя (полученную в /var/lib/waagent) и связанные с ней данные. Этот параметр используется для отмены подготовки ранее подготовленного образа в Azure с целью его записи и повторного использования.

- version: отображает версию waagent

- serialconsole: настройка GRUB для маркировки ttyS0 (первый последовательный порт) в качестве консоли загрузки. Это гарантирует, что журналы загрузки ядра отправляются на последовательный порт и становятся доступными для отладки.

- daemon: запуск waagent в качестве управляющей программы для контроля взаимодействия с платформой.
   Этот аргумент указывается для waagent в сценарии инициализации waagent.

##Конфигурация

Файл конфигурации (/ etc/waagent.conf) определяет действия waagent. 
Ниже приводится пример файла конфигурации:
	
	#
	# Azure Linux Agent Configuration	
	#
	Role.StateConsumer=None 
	Role.ConfigurationConsumer=None 
	Role.TopologyConsumer=None
	Provisioning.Enabled=y
	Provisioning.DeleteRootPassword=n
	Provisioning.RegenerateSshHostKeyPair=y
	Provisioning.SshHostKeyPairType=rsa
	Provisioning.MonitorHostName=y
	ResourceDisk.Format=y
	ResourceDisk.Filesystem=ext4
	ResourceDisk.MountPoint=/mnt/resource 
	ResourceDisk.EnableSwap=n 
	ResourceDisk.SwapSizeMB=0
	LBProbeResponder=y
	Logs.Verbose=n
	OS.RootDeviceScsiTimeout=300
	OS.OpensslPath=None

Ниже подробно описаны различные параметры конфигурации. 
Параметры конфигурации относятся к одному из трех типов: логическое значение, строка или целое число. 
Логические параметры конфигурации могут принимать значение "y" или "n". 
Для некоторых записей конфигурации типа "строка", описанных ниже, может использоваться специальное ключевое слово "None".

**Role.StateConsumer:**

Тип: строка  
По умолчанию: нет

Если указан путь к исполняемой программе, он вызывается при подготовке образа waagent и при подготовке подтверждения состояния "Готов" для структуры. В программе будет указан аргумент "Готов". Агент не будет ожидать возврата для продолжения программы.

**Role.ConfigurationConsumer:**

Тип: строка  
По умолчанию: нет

Если указан путь к исполняемой программе, программа вызывается в том случае, когда структура указывает, что доступен файл конфигурации для виртуальной машины. Путь к XML-файлу конфигурации предоставляется в качестве аргумента в исполняемый файл. Он может вызываться несколько раз при каждом изменении файла конфигурации. Образец файла приведен в приложении. Текущий путь к этому файлу — /var/lib/waagent/HostingEnvironmentConfig.xml.

**Role.TopologyConsumer:**

Тип: строка  
По умолчанию: нет

Если указан путь к исполняемой программе, программа вызывается в том случае, когда структура указывает, что доступен новый макет топологии сети для виртуальной машины. Путь к XML-файлу конфигурации предоставляется в качестве аргумента в исполняемый файл. Он может вызываться несколько раз при каждом изменении топологии сети (например, из-за восстановления службы). Образец файла приведен в приложении. Текущее размещение файла — /var/lib/waagent/SharedConfig.xml.

**Provisioning.Enabled:**

Тип: логический  
По умолчанию: y

Он позволяет пользователю включить или отключить функцию подготовки в агенте. Допустимые значения: "y" или "n". Если подготовка отключена, узел SSH и пользовательские ключи в образе сохраняются, а любые настройки, указанные в API для подготовки Azure, игнорируются.

**Provisioning.DeleteRootPassword:**

Тип: логический  
По умолчанию: n

Удаляет корневой пароль в файле /etc/shadow в процессе подготовки.

**Provisioning.RegenerateSshHostKeyPair:**

Тип: логический  
По умолчанию: y

Удаляет все пары ключей узла SSH (ecdsa, dsa и rsa) из /etc/ssh/ в процессе подготовки. При этом генерируется одна новая пара ключей.

Тип шифрования для новой пары ключей настраивается с помощью операции Provisioning.SshHostKeyPairType. Обратите внимание, что некоторые дистрибутивы повторно создают пары ключей SSH для любых недостающих типов шифрования при перезапуске управляющей программы SSH (например, после перезагрузки).

**Provisioning.SshHostKeyPairType:**

Тип: строка  
По умолчанию: rsa

Задается тип алгоритма шифрования, поддерживаемый управляющей программой SSH на виртуальной машине. Обычно поддерживаются значения "rsa", "dsa" и "ecdsa". Обратите внимание, что "putty.exe" в Windows не поддерживает "ecdsa". Таким образом, если вы планируете использовать putty.exe в Windows для подключения к развертыванию Linux, используйте "rsa" или "dsa".

**Provisioning.MonitorHostName:**

Тип: логический  
По умолчанию: y

Если задано, waagent будет отслеживать виртуальную машину Linux на предмет изменений имени узла (как значение, возвращаемое командой "hostname") и автоматически обновлять сетевую конфигурацию образа в соответствии с изменениями. Для передачи изменения имени на DNS-серверы сеть на виртуальной машине будет перезапущена. Это приведет к кратковременной потере подключения к Интернету.

**ResourceDisk.Format:**

Тип: логический  
По умолчанию: y

Если задано, диск ресурсов, предоставленный платформой, будет отформатирован и смонтирован с использованием waagent, если тип файловой системы, запрошенный пользователем в "ResourceDisk.Filesystem", отличается от "ntfs". На диске будет доступен один раздел типа Linux (83). Обратите внимание, что этот раздел не будет отформатирован, если он может быть успешно подключен.

**ResourceDisk.Filesystem:**

Тип: строка  
По умолчанию: ext4

Указывает тип файловой системы для диска ресурса. Допустимые значения зависят от дистрибутива Linux. Если используется строка X, в образе Linux должна присутствовать строка mkfs.X. Для образов SLES 11 обычно используется значение "ext3". Для образов FreeBSD здесь следует использовать "ufs2".

**ResourceDisk.MountPoint:**

Тип: строка  
По умолчанию: /mnt/resource 

Указывает путь, по которому подключен диск ресурсов.

**ResourceDisk.EnableSwap:**

Тип: логический  
По умолчанию: n 

Если задано, на диске ресурсов создается файл подкачки (/swapfile), который добавляется к пространству подкачки в системе.

**ResourceDisk.SwapSizeMB:**

Тип: целое число  
По умолчанию: 0

Размер файла подкачки в мегабайтах.

**LBProbeResponder:**

Тип: логический  
По умолчанию: y

Если задано, waagent отвечает на запросы зондов средства балансировки нагрузки, имеющихся на платформе (при наличии).

**Logs.Verbose:**

Тип: логический  
По умолчанию: n

Если задано, то файл журнала расширяется. Waagent записывает данные в /var/log/waagent.log, а также использует системную функцию logrotate для ротации журналов.

**OS.RootDeviceScsiTimeout:**

Тип: целое число  
По умолчанию: 300

Настраивает время ожидания SCSI в секундах на дисках операционной системы и на дисках с данными. Если не установлено, будут использоваться системные значения по умолчанию.

**OS.OpensslPath:**

Тип: строка  
По умолчанию: нет

Можно указать альтернативный путь для двоичного файла openssl, используемого при выполнении шифрования.

##Приложение

###Образец файла конфигурации роли

	<?xml version="1.0" encoding="utf-8"?>
	<HostingEnvironmentConfig version="1.0.0.0" goalStateIncarnation="1">
	  <StoredCertificates>
	    <StoredCertificate name="Stored0Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" certificateId="sha1:C093FA5CD3AAE057CB7C4E04532B2E16E07C26CA" storeName="My" configurationLevel="System" />
	  </StoredCertificates>
	  <Deployment name="a99549a92e38498f98cf2989330cd2f1" guid="{374ef9a2-de81-4412-ac87-e586fc869923}" incarnation="14">
	    <Service name="LinuxDemo1" guid="{00000000-0000-0000-0000-000000000000}" />
	    <ServiceInstance name="a99549a92e38498f98cf2989330cd2f1.4" guid="{250ac9df-e14c-4c5b-9cbc-f8a826ced0e7}" />
	  </Deployment>
	  <Incarnation number="1" instance="LinuxVM_IN_2" guid="{5c87ab8b-2f6a-4758-9f74-37e68c3e957b}" />
	  <Role guid="{47a04da2-d0b7-26e2-f039-b1f1ab11337a}" name="LinuxVM" hostingEnvironmentVersion="1" software="" softwareType="ApplicationPackage" entryPoint="" parameters="" settleTimeSeconds="10" />
	  <HostingEnvironmentSettings name="full" Runtime="rd_fabric_stable.111026-1712.RuntimePackage_1.0.0.9.zip">
	    <CAS mode="full" />
	    <PrivilegeLevel mode="max" />
	    <AdditionalProperties><CgiHandlers></CgiHandlers></AdditionalProperties></HostingEnvironmentSettings>
	    <ApplicationSettings>
	      <Setting name="__ModelData" value="&lt;m role=&quot;LinuxVM&quot; xmlns=&quot;urn:azure:m:v1&quot;>&lt;r name=&quot;LinuxVM&quot;>&lt;e name=&quot;HTTP&quot; />&lt;e name=&quot;Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp&quot; />&lt;e name=&quot;Microsoft.WindowsAzure.Plugins.RemoteForwarder.RdpInput&quot; />&lt;e name=&quot;SSH&quot; />&lt;/r>&lt;/m>" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="..." />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2015-11-06T23:59:59.0000000-08:00" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="rdos" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
	      <Setting name="startpage" value="Hello World!" />
	      <Setting name="Certificate|Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" value="sha1:C093FA5CD3AAE057CB7C4E04532B2E16E07C26CA" />
	    </ApplicationSettings>
	    <ResourceReferences>
	      <Resource name="DiagnosticStore" type="directory" request="Microsoft.Cis.Fabric.Controller.Descriptions.ServiceDescription.Data.Policy" sticky="true" size="1" path="a99549a92e38498f98cf2989330cd2f1.LinuxVM.DiagnosticStore\" disableQuota="false" />
	    </ResourceReferences>
	  </HostingEnvironmentConfig>

###Образец файла топологии роли

	<?xml version="1.0" encoding="utf-8"?>
	<SharedConfig version="1.0.0.0" goalStateIncarnation="2">
	  <Deployment name="a99549a92e38498f98cf2989330cd2f1" guid="{374ef9a2-de81-4412-ac87-e586fc869923}" incarnation="14">
	    <Service name="LinuxDemo1" guid="{00000000-0000-0000-0000-000000000000}" />
	    <ServiceInstance name="a99549a92e38498f98cf2989330cd2f1.4" guid="{250ac9df-e14c-4c5b-9cbc-f8a826ced0e7}" />
	  </Deployment>
	  <Incarnation number="1" instance="LinuxVM_IN_1" guid="{a7b94774-db5c-4007-8707-0b9e91fd808d}" />
	  <Role guid="{47a04da2-d0b7-26e2-f039-b1f1ab11337a}" name="LinuxVM" settleTimeSeconds="10" />
	  <LoadBalancerSettings timeoutSeconds="32" waitLoadBalancerProbeCount="8">
	    <Probes>
	      <Probe name="LinuxVM" />
	      <Probe name="03F7F19398C4358108B7ED059966EEBD" />
	      <Probe name="47194D0E3AB3FCAD621CAAF698EC82D8" />
	    </Probes>
	  </LoadBalancerSettings>
	  <OutputEndpoints>
	    <Endpoint name="LinuxVM:Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" type="SFS">
	      <Target instance="LinuxVM_IN_0" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	      <Target instance="LinuxVM_IN_1" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	      <Target instance="LinuxVM_IN_2" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	    </Endpoint>
	  </OutputEndpoints>
	  <Instances>
	    <Instance id="LinuxVM_IN_1" address="10.115.38.202">
	      <FaultDomains randomId="1" updateId="1" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="HTTP" address="10.115.38.202:80" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:80" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="80" to="80" />
	          </LocalPorts>
	        </Endpoint>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.38.202:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	          <RemoteInstances>
	            <RemoteInstance instance="LinuxVM_IN_0" />
	            <RemoteInstance instance="LinuxVM_IN_2" />
	          </RemoteInstances>
	        </Endpoint>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.RdpInput" address="10.115.38.202:20000" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:3389" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="20000" to="20000" />
	          </LocalPorts>
	        </Endpoint>
	        <Endpoint name="SSH" address="10.115.38.202:22" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:22" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="22" to="22" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	    <Instance id="LinuxVM_IN_0" address="10.115.58.82">
	      <FaultDomains randomId="0" updateId="0" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.58.82:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	    <Instance id="LinuxVM_IN_2" address="10.115.58.148">
	      <FaultDomains randomId="0" updateId="2" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.58.148:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	  </Instances>
	</SharedConfig>

