<properties 
	pageTitle="Общие сведения об SQL Server на виртуальных машинах Azure"
	description="В этой статье приводятся общие сведения о размещении SQL Server на виртуальных машинах Azure (IaaS). Также включены ссылки на более подробное содержимое."
	services="virtual-machines"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/24/2015"
	ms.author="jroth"/>

# Общие сведения об SQL Server на виртуальных машинах Azure

## Приступая к работе
Вы можете разместить [SQL Server на виртуальных машинах Azure](http://azure.microsoft.com/services/virtual-machines/sql-server/) в различных конфигурациях, от одного сервера базы данных до мультикомпьютерных конфигураций с использованием групп доступности AlwaysOn и виртуальной сети Azure.

>[AZURE.NOTE]Выполнение SQL Server на виртуальной машине Azure — один из вариантов хранения реляционных данных в Azure. Можно также использовать службу базы данных SQL Azure. Дополнительную информацию см. в разделе [Общее представление о базе данных SQL Azure и SQL Server в виртуальных машинах Azure](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

Для создания виртуальной машины SQL Server в Azure сначала необходимо получить подписку на платформу Azure. Приобрести подписку Azure можно в разделе [Варианты приобретения](http://azure.microsoft.com/pricing/purchase-options/). Чтобы попробовать ее бесплатно, зайдите на страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

### Развертывание экземпляра SQL Server на одной виртуальной машине

После получения подписки самый простой способ развернуть виртуальную машину SQL Server в Azure — [подготовить образ коллекции машин SQL Server на портале управления Azure](virtual-machines-provision-sql-server.md). Эти образы содержат лицензии на использование SQL Server, стоимость которых включена в цену виртуальной машины.

Вы можете выполнять общие рабочие нагрузки SQL Server на виртуальных машинах Azure. Для SQL Server есть несколько оптимизированных образов виртуальных машин, которые доступны в коллекции. Дополнительные сведения о конкретных рабочих нагрузках приведены в следующих статьях.

- [SQL Server Business Intelligence на виртуальных машинах Azure](virtual-machines-sql-server-business-intelligence.md)
- [Хранение данных SQL Server и рабочих нагрузок операций на виртуальных машинах Azure](virtual-machines-sql-server-dw-and-oltp-workloads.md)

Следующая таблица содержит матрицу доступных образов SQL Server в коллекции виртуальных машин.

|Версия SQL Server|операционная система|Выпуск SQL Server|
|---|---|---|
|SQL Server 2008 R2 SP2|Windows Server 2008 R2|Enterprise, Standard и Web|
|SQL Server 2008 R2 SP3|Windows Server 2008 R2|Enterprise, Standard и Web|
|SQL Server 2012 SP2|Windows Server 2012|Enterprise, Standard, Web, OLTP (Enterprise) и хранилища данных (Enterprise)|
|SQL Server 2012 SP2|Windows Server 2012 R2|Enterprise, Standard, Web, OLTP (Enterprise) и хранилища данных (Enterprise)|
|SQL Server 2014|Windows Server 2012 R2|Enterprise, Standard, Web, OLTP (Enterprise) и хранилища данных (Enterprise)|
|SQL Server 2014 SP1|Windows Server 2012 R2|Enterprise, Standard, Web, OLTP (Enterprise) и хранилища данных (Enterprise)|
|SQL Server 2016 CTP|Windows Server 2012 R2|Оценка|

Наряду с использованием этих предварительно настроенных образов можно [создать виртуальную машину Azure](virtual-machines-windows-tutorial.md) без предустановленного SQL Server. Можно установить любой экземпляр SQL Server с действующей лицензией. Перенесите лицензию в Azure для запуска SQL Server в виртуальной машине Azure, воспользовавшись инструкциями из статьи [Перемещение лицензий в рамках программы Software Assurance в Azure](http://azure.microsoft.com/pricing/license-mobility/). В этом сценарии вы оплачиваете только [затраты](http://azure.microsoft.com/pricing/details/virtual-machines) на вычислительные ресурсы и ресурсы хранения Azure, связанные с виртуальной машиной.

На этих ранних этапах подготовки и настройки выполняются следующие общие задачи:

- [анализ рекомендаций по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-sql-server-performance-best-practices.md);
- [анализ рекомендаций по безопасности SQL Server в виртуальных машинах Azure](virtual-machines-sql-server-security-considerations.md);
- [настройка соединения](virtual-machines-sql-server-connectivity.md).

### Перенос данных

После запуска виртуальной машины SQL Server может потребоваться перенести существующие базы данных на нее. Существует несколько методов, но для большинства сценариев хорошо подходит мастер развертывания в SQL Server Management Studio. Учебник по мастеру и обсуждение сценариев см. в статье [Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-migrate-onpremises-database.md).

## высокую доступность;

Если требуется высокий уровень доступности, попробуйте настроить группы доступности AlwaysOn SQL Server. Это подразумевает использование нескольких виртуальных машин Azure в виртуальной сети. На портале предварительной версии Azure есть шаблон, который настраивает эту конфигурацию. Дополнительную информацию см. в разделе [Предложения AlwaysOn SQL Server в коллекции портала Microsoft Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

Если вы хотите вручную настроить группу доступности и связанный прослушиватель, см. следующие статьи.

- [Настройка групп доступности AlwaysOn в Azure (графический пользовательский интерфейс)](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [Настройка прослушивателя внутренней подсистемы балансировки нагрузки для группы доступности AlwaysOn в Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [Расширение локальных групп доступности AlwaysOn в Azure](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

Дополнительные рекомендации по обеспечению высокого уровня доступности см. в разделе [Высокий уровень доступности и аварийное восстановление для SQL Server в виртуальных машинах Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Архивация и восстановление
Для локальных баз данных Azure может действовать как дополнительный центр обработки данных для хранения файлов резервных копий SQL Server. Обзор параметров резервного копирования и восстановления см. в статье [Резервное копирование и восстановление SQL Server в виртуальных машинах Azure](virtual-machines-sql-server-backup-and-restore.md).

[Резервное копирование SQL Server на URL-адрес](https://msdn.microsoft.com/library/dn435916.aspx) сохраняет файлы резервных копий Azure в хранилище больших двоичных объектов. [Управляемое резервное копирование SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) позволяет планировать резервное копирование и хранение данных в Azure. Эти службы могут использоваться с локальными экземплярами SQL Server или SQL Server на виртуальных машинах Azure. Виртуальные машины Azure могут также использовать преимущества [автоматического резервного копирования](virtual-machines-sql-server-automated-backup.md) и [автоматического обновления путем частичной замены](virtual-machines-sql-server-automated-patching.md) для SQL Server.

## Сведения о настройке образа виртуальной машины SQL Server

В следующих таблицах описана конфигурация предоставляемых платформой образов виртуальных машин SQL Server.

### Windows Server

Установка Windows Server в образе платформы содержит следующие параметры конфигурации и компоненты.

|Функция|Конфигурация
|---|---|
|удаленный рабочий стол;|Включен для учетной записи администратора.|
|Обновл. Windows|Включено|
|Учетные записи пользователей|По умолчанию учетная запись пользователя, указанная во время подготовки, является членом группы локальных администраторов. Эта учетная запись администратора также является представителем серверной роли sysadmin SQL Server.|
|Рабочие группы|Виртуальная машина является членом рабочей группы WORKGROUP.|
|Учетная запись гостя|Отключено|
|Брандмауэр Windows в режиме повышенной безопасности|С|
|.NET Framework|Версия 4|
|диски;|Выбранный размер виртуальной машины ограничивает количество дисков данных, которые можно настроить. См. [Размеры виртуальных машин в Azure](virtual-machines-size-specs.md).|

### SQL Server

Установка SQL Server в образе платформы содержит следующие параметры конфигурации и компоненты.

|Функция|Конфигурация|
|---|---|
|Ядро СУБД|Установленные|
|службы Analysis Services|Установленные|
|Службы интеграции|Установленные|
|Службы Reporting Services|В собственном режиме|
|Группы доступности AlwaysOn|Доступны в SQL Server 2012 или более поздних версий. Требуется [дополнительная настройка](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).
|Репликация|Установленные|
|Полнотекстовые и семантические извлечения для поиска|Установлены (семантические извлечения присутствуют только в SQL Server 2012 и более поздних версиях).|
|Data Quality Services|Установлены (только в SQL Server 2012 и более поздних версиях).|
|Master Data Services|Установлены (только в SQL Server 2012 и более поздних версиях). Требуются [дополнительная настройка и дополнительные компоненты](https://msdn.microsoft.com/library/ee633752.aspx).
|PowerPivot для SharePoint|Доступен (только в SQL Server 2012 и более поздних версиях). Требуются дополнительная настройка и дополнительные компоненты (в том числе SharePoint).|
|Клиент распределенного воспроизведения|Доступен (только в SQL Server 2012 и более поздних версиях), но не установлен. См. [Запуск установки SQL Server на предоставленном платформой образе SQL Server](#running-sql-server-setup-from-the-platform-provided-sql-server-image).|
|Средства|Все средства, включая SQL Server Management Studio, диспетчер конфигурации SQL Server, Business Intelligence Development Studio, программу установки SQL Server, средства связи клиентских средств, пакет SDK клиентских средств, пакет SDK для подключения клиента SQL и средства обновления и миграции, такие как приложения уровня данных (DAC), средства резервного копирования, восстановления, присоединения и отсоединения|
|Электронная документация по SQL Server|Установлена, но требуется настройка с помощью средства просмотра справки.|

### Настройка ядра СУБД

Настраиваются следующие параметры ядра базы данных. Для сведений о дополнительных параметрах изучите экземпляр SQL Server.

|Функция|Конфигурация|
|---|---|
|Экземпляр|Содержит экземпляр по умолчанию SQL Server Database Engine (безымянный), прослушивает только протокол общей памяти.|
|Проверка подлинности|По умолчанию во время настройки виртуальной машины SQL Server в Azure выбирается проверка подлинности Windows. Если вы хотите использовать имя пользователя sa или создать новую учетную запись SQL Server, необходимо изменить режим проверки подлинности. Дополнительные сведения см. в статье [Рекомендации по безопасности SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-security-considerations.md).|
|sysadmin|Пользователь Azure, который установил виртуальную машину, первоначально является единственным членом фиксированной серверной роли sysadmin SQL Server.|
|Память|В настройках памяти ядра СУБД задается динамическая конфигурация памяти.|
|Проверка подлинности автономной базы данных|Отключить|
|Язык по умолчанию|Английский|
|Цепочки владения между базами данных|Отключить|

[Программа улучшения качества программного обеспечения (CEIP)](https://technet.microsoft.com/library/cc730757.aspx) включена. CEIP можно отключить с помощью утилиты создания отчетов об ошибках и использовании SQL Server. Для запуска утилиты создания отчетов об ошибках и использовании SQL Server в меню "Пуск" щелкните "Все программы", "Версия Microsoft SQL Server" "Средства настройки", а затем "Создание отчетов об ошибках и использовании SQL Server". Если вы не хотите использовать экземпляр SQL Server с включенной CEIP, вы также можете развернуть образ собственной виртуальной машины в Azure. Дополнительные сведения см. в статье [Создание и загрузка виртуального жесткого диска с операционной системой Windows Server](virtual-machines-create-upload-vhd-windows-server.md).

### Запуск установки SQL Server на предоставленном платформой образе SQL Server

При создании виртуальной машины с помощью предоставленного платформой образа SQL Server вы можете найти установочный носитель SQL Server, сохраненный в виртуальной машине, в каталоге **C:\\SqlServer\_SQLMajorVersion.SQLMinorVersion\_Full**. Для выполнения всех операций по установке, в том числе добавления или удаления компонентов, добавления нового экземпляра или восстановления экземпляра, при наличии достаточного пространства на диске можно запустить установку из этого каталога.

>[AZURE.NOTE]На портале Azure представлено несколько версий образов SQL Server. Предоставленные платформой образы SQL Server с датой выпуска 15 мая 2014 г. или позднее по умолчанию содержат ключ продукта. При подготовке виртуальной машины с помощью предоставленного платформой образа SQL Server, который был опубликован до этой даты, эта виртуальная машина не содержит ключ продукта. Рекомендуется всегда выбирать последнюю версию образа при подготовке новой виртуальной машины.

## Ресурсы

- [Подготовка виртуальной машины SQL Server в Azure](virtual-machines-provision-sql-server.md)
- [Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-migrate-onpremises-database.md)
- [Высокий уровень доступности и аварийное восстановление для SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)
- [Шаблоны приложений и стратегии разработки для SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-application-patterns-and-development-strategies.md)
- [Виртуальные машины Azure](virtual-machines-about.md) 

<!---HONumber=August15_HO9-->