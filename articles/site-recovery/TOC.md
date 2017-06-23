# Обзор
## [Что такое Site Recovery?](site-recovery-overview.md)
## Как работает Site Recovery?
### [Архитектура: Azure — Azure](site-recovery-azure-to-azure-architecture.md)
### [Архитектура: VMware — Azure](site-recovery-architecture-vmware-to-azure.md)
### [Архитектура: Hyper-V — Azure](site-recovery-architecture-hyper-v-to-azure.md)
### [Архитектура: репликация на дополнительный сайт](site-recovery-architecture-to-secondary-site.md)
## [Какие рабочие нагрузки можно защитить?](site-recovery-workload.md)
## Матрица поддержки Site Recovery
### [Поддержка: Azure — Azure](site-recovery-support-matrix-azure-to-azure.md)
### [Поддержка: локальная среда —Azure](site-recovery-support-matrix-to-azure.md)
### [Поддержка: локальная среда — дополнительный сайт](site-recovery-support-matrix-to-sec-site.md)
## [Часто задаваемые вопросы](site-recovery-faq.md)
## [Просмотр введения](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)

# Начало работы
## [Репликация виртуальных машин Azure (предварительная версия)](site-recovery-azure-to-azure.md)
## [Репликация физических серверов в Azure](site-recovery-physical-servers-to-azure.md)
## [Репликация виртуальных машин Hyper-V в Azure (с использованием VMM)](site-recovery-vmm-to-azure.md)
## [Репликация виртуальных машин Hyper-V в Azure](site-recovery-hyper-v-site-to-azure.md)
## [Репликация виртуальных машин Hyper-V на дополнительный сайт с помощью VMM](site-recovery-vmm-to-vmm.md)
## [Репликация виртуальных машин и физических серверов VMware на дополнительный сайт](site-recovery-vmware-to-vmware.md)
## [Репликация виртуальных машин VMware в Azure при развертывании нескольких клиентов (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)

# Практическое руководство
## План
### [Необходимые условия для репликации Azure](site-recovery-azure-to-azure-prereq.md)
### Планирование сетей
#### [Планирование сетей для репликации: Azure — Azure (предварительная версия)](site-recovery-azure-to-azure-networking-guidance.md)
#### [Планирование сетей для репликации локальных виртуальных машин](site-recovery-network-design.md)
#### [Планирование сопоставления сетей для репликации виртуальных машин Azure](site-recovery-network-mapping-azure-to-azure.md)
#### [Планирование сопоставления сетей для репликации виртуальных машин Hyper-V](site-recovery-network-mapping.md)
### Планирование производительности и масштабируемости
#### [Планирование ресурсов для репликации VMware в Azure](site-recovery-plan-capacity-vmware.md)
#### [Использование планировщика развертывания для репликации VMware в Azure](site-recovery-deployment-planner.md)
#### [Использование планировщика ресурсов для репликации Hyper-V](site-recovery-capacity-planner.md)
### [Планирование доступа на основе ролей для репликации виртуальных машин](site-recovery-role-based-linked-access-control.md)
## Развернуть
### [Репликация виртуальных машин VMware в Azure](vmware-walkthrough-overview.md)
#### [Шаг 1. Общие сведения об архитектуре](vmware-walkthrough-architecture.md)
#### [Шаг 2. Проверка предварительных условий и ограничений](vmware-walkthrough-prerequisites.md)
#### [Шаг 3. Планирование ресурсов](vmware-walkthrough-capacity.md)
#### [Шаг 4. Планирование сетей](vmware-walkthrough-network.md)
#### [Шаг 5. Подготовка Azure](vmware-walkthrough-prepare-azure.md)
#### [Шаг 6. Подготовка VMware](vmware-walkthrough-prepare-vmware.md)
#### [Шаг 7. Создание хранилища](vmware-walkthrough-create-vault.md)
#### [Шаг 8. Настройка исходной и целевой среды](vmware-walkthrough-source-target.md)
#### [Шаг 9. Настройка политики репликации](vmware-walkthrough-replication.md)
#### [Шаг 10. Установка службы Mobility Service](vmware-walkthrough-install-mobility.md)
#### [Шаг 11. Включение репликации](vmware-walkthrough-enable-replication.md)
#### [Шаг 12. Запуск тестовой отработки отказа](vmware-walkthrough-test-failover.md)
## Настройка
### Настройка исходной среды
#### [Исходная среда: VMware — Azure](site-recovery-set-up-vmware-to-azure.md)
#### [Исходная среда: физическое устройство — Azure](site-recovery-set-up-physical-to-azure.md)
### Настройка целевой среды
#### [Целевая среда: VMware — Azure](site-recovery-prepare-target-vmware-to-azure.md)
#### [Целевая среда: физическое устройство — Azure](site-recovery-prepare-target-physical-to-azure.md)
### [Настройка параметров репликации](site-recovery-setup-replication-settings-vmware.md)
### [Развертывание службы Mobility Service для репликации VMware](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Развертывание службы Mobility Service с помощью System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
#### [Развертывание службы Mobility Service с помощью Azure Automation DSC](site-recovery-automate-mobility-service-install.md)
### Включение репликации
#### [Включение репликации: Azure — Azure](site-recovery-replicate-azure-to-azure.md)
#### [Включение репликации: VMware — Azure](site-recovery-replicate-vmware-to-azure.md)
## Отработка отказа и восстановление размещения
### [Настройка планов восстановления](site-recovery-create-recovery-plans.md)
#### [Добавление модуля Runbook Azure в планы восстановления](site-recovery-runbook-automation.md)
### Запуск тестовой отработки отказа
#### [Запуск тестовой отработки отказа в Azure](site-recovery-test-failover-to-azure.md)
#### [Запуск тестовой отработки отказа между облаками VMM](site-recovery-test-failover-vmm-to-vmm.md)
### [Отработка отказа защищенных виртуальных машин](site-recovery-failover.md)
### Повторное включение защиты виртуальных машин после отработки отказа
#### [Повторное включение защиты: дополнительный регион Azure — основной регион Azure](site-recovery-how-to-reprotect-azure-to-azure.md)
#### [Повторное включение защиты: Azure — локальная среда](site-recovery-how-to-reprotect.md)
### Восстановление размещения из Azure
#### [Восстановление размещения из Azure в VMware](site-recovery-failback-azure-to-vmware.md)
#### [Восстановление размещения из Azure в Hyper-V](site-recovery-failback-from-azure-to-hyper-v.md)
## Миграция
### [Миграция в облако Azure](site-recovery-migrate-to-azure.md)
### [Миграция между регионами Azure](site-recovery-migrate-azure-to-azure.md)
### [Перенос экземпляров AWS Windows в Azure](site-recovery-migrate-aws-to-azure.md)
### [Репликация перенесенных компьютеров в другой регион Azure](site-recovery-azure-to-azure-after-migration.md)
## Рабочие нагрузки
### [Active Directory и DNS](site-recovery-active-directory.md)
### [Репликация SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-sharepoint.md)
### [центр IoT Azure](site-recovery-dynamicsax.md)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [Веб-приложения на базе IIS](site-recovery-iis.md)
### [Citrix XenApp и XenDesktop](site-recovery-citrix-xenapp-and-xendesktop.md)
### [Другие рабочие нагрузки](site-recovery-workload.md#workload-summary)
## Автоматизация репликации
### [Автоматизация репликации Hyper-V в Azure (без VMM)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Автоматизация репликации Hyper-V в Azure (с VMM)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Автоматизация репликации Hyper-V на дополнительный сайт (с VMM)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Управление
### [Управление серверами обработки в Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [Управление сервером конфигурации](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [Управление масштабируемыми серверами обработки](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [Управление серверами vCenter](site-recovery-vmware-to-azure-manage-vCenter.md)
### [Удаление серверов и отключение защиты](site-recovery-manage-registration-and-protection.md)

## Мониторинг и устранение неполадок
### [Проблемы с репликацией: Azure — Azure](site-recovery-azure-to-azure-troubleshoot-errors.md)
### [Проблемы репликации из локальной среды в Azure](site-recovery-vmware-to-azure-protection-troubleshoot.md)
### [Сбор журналов и устранение неполадок в локальной среде](site-recovery-monitoring-and-troubleshooting.md)

# Справочные материалы
## [PowerShell](/powershell/module/azurerm.siterecovery)
## [PowerShell — классическая модель](/powershell/module/azure/?view=azuresmps-3.7.0)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Сопутствующие материалы
## [Служба автоматизации Azure](/azure/automation/)

# Ресурсы
## [Схема обучения](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Форум](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Блог](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Цены](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=site-recovery)
