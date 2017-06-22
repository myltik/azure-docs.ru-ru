
# Обзор
## [Что такое служба архивации Azure?](backup-introduction-to-azure-backup.md)

# Начало работы
## [Резервное копирование файлов и папок](backup-try-azure-backup-in-10-mins.md)
## [Архивация виртуальных машин Azure](backup-azure-vms-first-look.md)
## [Защита виртуальных машин Azure](backup-azure-vms-first-look-arm.md)

# Практическое руководство
## Использование PowerShell
### [Виртуальные машины Azure на портале Azure](backup-azure-vms-automation.md)
### [Виртуальные машины Azure на классическом портале](backup-azure-vms-classic-automation.md)
### [DPM на портале Azure](backup-dpm-automation.md)
### [DPM на классическом портале](backup-dpm-automation-classic.md)
### [Windows Server на портале Azure](backup-client-automation.md)
### [Windows Server на классическом портале](backup-client-automation-classic.md)

## Сервер службы архивации Azure
### [Матрица защиты сервера резервного копирования Azure](backup-mabs-protection-matrix.md)
### Установка и обновление
#### [Подготовка рабочих нагрузок сервера резервного копирования Azure на портале Azure](backup-azure-microsoft-azure-backup.md)
#### [Подготовка рабочих нагрузок сервера резервного копирования Azure на классическом портале](backup-azure-microsoft-azure-backup-classic.md)
#### [Добавление хранилища на сервер резервного копирования Azure](backup-mabs-add-storage.md)
#### [Обновление сервера резервного копирования Azure до версии 2](backup-mabs-upgrade-to-v2.md)
#### [Автоматическая установка сервера резервного копирования Azure](backup-mabs-unattended-install.md)
### Защита рабочих нагрузок
#### [Резервное копирование сервера VMware с помощью сервера резервного копирования Azure](backup-azure-backup-server-vmware.md)
#### [Резервное копирование Exchange Server с помощью сервера резервного копирования Azure](backup-azure-exchange-mabs.md)
#### [Резервное копирование фермы SharePoint с помощью сервера резервного копирования Azure](backup-azure-backup-sharepoint-mabs.md)
#### [Резервное копирование баз данных SQL Server с помощью сервера резервного копирования Azure](backup-azure-sql-mabs.md)
#### [Защита состояния системы и восстановление исходного состояния системы](backup-mabs-system-state-and-bmr.md)
### Устранение неполадок
#### [Устранение неполадок Сервера резервного копирования Azure](backup-azure-mabs-troubleshoot.md)


## Data Protection Manager
### [Подготовка рабочих нагрузок DPM на портале Azure](backup-azure-dpm-introduction.md)
### [Подготовка рабочих нагрузок DPM на классическом портале](backup-azure-dpm-introduction-classic.md)
### [Резервное копирование Exchange Server с помощью System Center DPM](backup-azure-backup-exchange-server.md)
### [Восстановление данных из хранилища резервных копий на альтернативный сервер DPM](backup-azure-alternate-dpm-server.md)
### [Резервное копирование рабочих нагрузок SQL Server с помощью DPM](backup-azure-backup-sql.md)
### [Резервное копирование фермы SharePoint с помощью DPM](backup-azure-backup-sharepoint.md)

## Виртуальные машины Azure
### Подготовка виртуальной машины
#### [Подготовка виртуальных машин Azure](backup-azure-vms-prepare.md)
#### [Подготовка виртуальных машин, развернутых с помощью Resource Manager](backup-azure-arm-vms-prepare.md)
### Планирование среды
#### [Планирование инфраструктуры резервного копирования виртуальных машин](backup-azure-vms-introduction.md)
### Резервное копирование серверов и рабочих нагрузок
#### [Резервное копирование виртуальных машин Azure в резервное хранилище](backup-azure-vms.md)
#### [Резервное копирование виртуальных машин Azure в хранилище служб восстановления](backup-azure-arm-vms.md)
#### [Резервное копирование зашифрованных виртуальных машин](backup-azure-vms-encryption.md)
### Контроль и мониторинг виртуальных машин
#### [Управление резервным копированием виртуальных машин Azure на классическом портале](backup-azure-manage-vms-classic.md)
#### [Управлять резервным копированием виртуальных машин Azure на портале Azure](backup-azure-manage-vms.md)
#### [Мониторинг предупреждений о резервном копировании виртуальных машин Azure на портале Azure](backup-azure-monitor-vms.md)
### Восстановление данных с виртуальных машин
#### [Восстановление файлов из резервных копий виртуальных машин Azure](backup-azure-restore-files-from-vm.md)
#### [Восстановление виртуальных машин в Azure](backup-azure-restore-vms.md)
#### [Восстановление виртуальных машин, развернутых с помощью Azure Resource Manager, на портале Azure](backup-azure-arm-restore-vms.md)
#### [Восстановление ключа и секрета в Key Vault для зашифрованных виртуальных машин с помощью службы архивации Azure](backup-azure-restore-key-secret.md)
#### [Восстановление зашифрованных виртуальных машин](backup-azure-vms-encryption.md)

## База данных SQL Azure
### [Настройка долгосрочного хранения резервных копий](../sql-database/sql-database-configure-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Просмотр резервных копий в хранилище служб восстановления](../sql-database/sql-database-view-backups-in-vault.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Восстановление после долгосрочного хранения резервных копий](../sql-database/sql-database-restore-from-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Удаление резервных копий Azure SQL после долгосрочного хранения](../sql-database/sql-database-long-term-retention-delete.md?toc=%2fazure%2fbackup%2ftoc.json)

## Файлы и папки Windows
### [Windows Server: использование классической модели развертывания](backup-configure-vault-classic.md)
### [Windows Server: использование модели развертывания Resource Manager](backup-configure-vault.md)
### [Управление хранилищами службы архивации с помощью классической модели развертывания](backup-azure-manage-windows-server-classic.md)
### [Управление хранилищами служб восстановления и их мониторинг](backup-azure-manage-windows-server.md)
### [Восстановление файлов на Windows Server с использованием модели развертывания Resource Manager](backup-azure-restore-windows-server.md)
### [Восстановление файлов на Windows Server с использованием классической модели развертывания](backup-azure-restore-windows-server-classic.md)

## [Часто задаваемые вопросы](backup-azure-backup-faq.md)

## Устранение неполадок
### [Неполадки с резервным копированием виртуальных машин Azure на портале Azure](backup-azure-vms-troubleshoot.md)
### [Неполадки с резервным копированием виртуальных машин Azure на классическом портале](backup-azure-vms-troubleshoot-classic.md)
### [Ошибка резервного копирования виртуальной машины Azure: "Не удалось запросить состояние моментального снимка в агенте ВМ. Истекло время ожидания для подзадачи моментального снимка ВМ"](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Медленное резервное копирование файлов и папок в службе архивации Azure](backup-azure-troubleshoot-slow-backup-performance-issue.md)

# Основные понятия
## [Обзор хранилищ служб восстановления](backup-azure-recovery-services-vault-overview.md)
## [Обновление резервного хранилища до хранилища служб восстановления](backup-azure-upgrade-backup-to-recovery-services.md)
## [Удаление хранилища службы архивации Azure](backup-azure-delete-vault.md)
## [Контроль доступа на основе ролей](backup-rbac-rs-vault.md)
## [Безопасность гибридных резервных копий](backup-azure-security-feature.md)
## [Настройка автономного резервного копирования](backup-azure-backup-import-export.md)
## [Замена ленточным библиотекам](backup-azure-backup-cloud-as-tape.md)
## [Согласованное с приложениями резервное копирование виртуальных машин Linux](backup-azure-linux-app-consistent.md)

# Справочные материалы
## [PowerShell](/powershell/module/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# Ресурсы
## [Цены](https://azure.microsoft.com/pricing/details/backup/)
## [Форум MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=backup)
## [Обновления службы](https://azure.microsoft.com/updates/?product=backup)
