# Обзор
## [Что такое Site Recovery?](site-recovery-overview.md)
## [Как работает Site Recovery?](site-recovery-components.md)
## [Какие рабочие нагрузки можно защитить?](site-recovery-workload.md)
## [Таблица поддержки Site Recovery](site-recovery-support-matrix.md)
## [Часто задаваемые вопросы](site-recovery-faq.md)
## [Просмотр введения](https://www.youtube.com/watch?v=eOOwMQPBKfM)

# Начало работы
## [Репликация виртуальных машин VMware в Azure](site-recovery-vmware-to-azure.md)
## [Репликация виртуальных машин VMware в Azure при развертывании нескольких клиентов (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)
## [Репликация виртуальных машин Hyper-V в Azure (с использованием VMM)](site-recovery-vmm-to-azure.md)
## [Репликация виртуальных машин Hyper-V в Azure](site-recovery-hyper-v-site-to-azure.md)
## [Репликация виртуальных машин и физических серверов VMware на дополнительный сайт](site-recovery-vmware-to-vmware.md)
## [Репликация виртуальных машин Hyper-V на дополнительный сайт с помощью VMM](site-recovery-vmm-to-vmm.md)

# Практическое руководство
## План
### [Предварительные условия для развертывания](site-recovery-prereq.md)
### [Рекомендации по сетевой инфраструктуре](site-recovery-network-design.md)
### [Использование планировщика ресурсов Site Recovery](site-recovery-capacity-planner.md)
### [Планирование ресурсов и масштабирование репликации VMware в Azure](site-recovery-plan-capacity-vmware.md)
## Настройка
### [Настройка среды исходной репликации](site-recovery-set-up-vmware-to-azure.md)
### [Настройка параметров репликации](site-recovery-setup-replication-settings-vmware.md)
### [Развертывание службы Mobility Service для репликации VMware](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Развертывание службы Mobility Service с помощью System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
#### [Развертывание службы Mobility Service с использованием Azure Automation DSC](site-recovery-automate-mobility-service-install.md)
### [Удаление серверов и отключение защиты](site-recovery-manage-registration-and-protection.md)
## Миграция
### [Миграция в облако Azure](site-recovery-migrate-to-azure.md)
### [Миграция между регионами Azure](site-recovery-migrate-azure-to-azure.md)
### [Перенос экземпляров AWS Windows в Azure](site-recovery-migrate-aws-to-azure.md)
## Рабочие нагрузки
### [Active Directory и DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-workload.md#protect-sharepoint)
### [центр IoT Azure](site-recovery-workload.md#protect-dynamics-ax)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [Другие рабочие нагрузки](site-recovery-workload.md#workload-summary)
## Автоматизация репликации
### [Автоматизация репликации Hyper-V в Azure (без VMM)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Автоматизация репликации Hyper-V в Azure (с VMM)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Автоматизация репликации Hyper-V на дополнительный сайт (с VMM)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Отработка отказа и восстановление размещения
### [Отработка отказа в Site Recovery](site-recovery-failover.md)
### [Настройка планов восстановления](site-recovery-create-recovery-plans.md)
#### [Добавление модуля Runbook Azure в планы восстановления](site-recovery-runbook-automation.md)
### [Запуск тестовой отработки отказа из VMware в Azure](site-recovery-test-failover-to-azure.md)
### [Запуск тестовой отработки отказа между двумя сайтами VMM](site-recovery-test-failover-vmm-to-vmm.md)
### [Восстановление размещения виртуальных машин VMware и физических серверов](site-recovery-failback-azure-to-vmware.md)
## [Мониторинг и устранение неполадок](site-recovery-monitoring-and-troubleshooting.md)

# Справочные материалы
## [PowerShell](/powershell/resourcemanager/azurerm.siterecovery/v3.2.0/azurerm.siterecovery)
## [PowerShell — классическая модель](/powershell/servicemanagement/azure.siterecovery/v3.1.0/azure.siterecovery)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Сопутствующие материалы
## [Служба автоматизации Azure](/azure/automation/)

# Ресурсы
## [Схема обучения](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Форум](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Блог](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Цены](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=site-recovery)


<!--HONumber=Feb17_HO3-->


