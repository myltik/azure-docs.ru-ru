# Обзор
## [Начало работы](get-started.md)
## [Сертификаты](sap-certifications.md)
# SAP HANA в Azure (большие экземпляры)
## [Обзор и архитектура](hana-overview-architecture.md)
## [Инфраструктура и подключение](hana-overview-infrastructure-connectivity.md)
## [Установка SAP HANA](hana-installation.md)
## [Высокий уровень доступности и аварийное восстановление](hana-overview-high-availability-disaster-recovery.md)
## [Устранение неполадок и мониторинг](troubleshooting-monitoring.md)
## Практическое руководство
### [Организация высокой доступности с помощью STONITH](ha-setup-with-stonith.md)
### [Резервное копирование ОС для номеров SKU типа II](os-backup-type-ii-skus.md)
# SAP HANA на виртуальных машинах Azure
## [Установка SAP HANA с одним экземпляром](hana-get-started.md)
## [Руководство по развертыванию S/4 HANA или BW/4 HANA SAP CAL](cal-s4h.md)
## [Руководство по использованию SAP HANA в Azure](hana-vm-operations.md)
## Доступность SAP HANA в Виртуальных машинах Azure
### [Общие сведения о доступности SAP HANA в Azure](sap-hana-availability-overview.md)
### [Доступность SAP HANA в Azure в пределах одного региона Azure](sap-hana-availability-one-region.md)
### [Доступность SAP HANA в Azure в разных регионах Azure](sap-hana-availability-across-regions.md)
## [Настройка репликации системы SAP HANA в виртуальные машины Azure](sap-hana-high-availability.md)
## [Обзор резервного копирования SAP HANA](sap-hana-backup-guide.md)
## [Резервное копирование SAP HANA на уровне файлов](sap-hana-backup-file-level.md)
## [Резервные копии моментальных снимков хранилища SAP HANA](sap-hana-backup-storage-snapshots.md)
# SAP NetWeaver на виртуальных машинах Azure
## [Руководство по развертыванию интегрированных сред разработки SAP и SAP CAL в Windows или SQL Server](cal-ides-erp6-erp7-sp3-sql.md)
## [SAP NetWeaver на виртуальных машинах Azure под управлением Linux](suse-quickstart.md)
## [SAP NetWeaver в Azure: планирование и реализация](planning-guide.md)
## Высокий уровень доступности в Windows и Linux
### [Обзор](sap-high-availability-guide-start.md)
### Архитектура высокого уровня доступности
#### [Архитектура и сценарии высокого уровня доступности](sap-high-availability-architecture-scenarios.md)
#### [Повышение уровня доступности архитектуры и сценариев](sap-higher-availability-architecture-scenarios.md)
#### [Высокий уровень доступности в Windows с общим диском для экземпляра (A)SCS](sap-high-availability-guide-wsfc-shared-disk.md)
#### [Высокий уровень доступности в Windows с общей папкой SOFS для экземпляра (A)SCS](sap-high-availability-guide-wsfc-file-share.md)
#### [Высокий уровень доступности в SUSE Linux для экземпляра (A)SCS](high-availability-guide-suse.md)
### Подготовка инфраструктуры Azure
#### [В Windows с общим диском для экземпляра (A)SCS](sap-high-availability-infrastructure-wsfc-shared-disk.md)
#### [В Windows с общей папкой SOFS для экземпляра (A)SCS](sap-high-availability-infrastructure-wsfc-file-share.md)
#### [Высокий уровень доступности для NFS на виртуальных машинах Azure на SLES](high-availability-guide-suse-nfs.md)
#### [Pacemaker на SLES](high-availability-guide-suse-pacemaker.md)
### Установка SAP
#### [В Windows с общим диском для экземпляра (A)SCS](sap-high-availability-installation-wsfc-shared-disk.md)
#### [В Windows с общей папкой SOFS для экземпляра (A)SCS](sap-high-availability-installation-wsfc-file-share.md)
#### [В SUSE Linux с NFS для экземпляра (A)SCS](high-availability-guide-suse.md)
### Использование нескольких SID в SAP
#### [В Windows с общим диском для экземпляра (A)SCS](sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
#### [В Windows с общей папкой SOFS для экземпляра (A)SCS](sap-ascs-ha-multi-sid-wsfc-file-share.md)
## [Руководство по развертыванию](deployment-guide.md)
## [Руководство по развертыванию СУБД](dbms-guide.md)
## [Azure Site Recovery для аварийного восстановления SAP](../../../site-recovery/site-recovery-workload.md#protect-sap)
# Интеграция AAD с приложением для управления удостоверениями SAP и единый вход
## [Интеграция с облаком SAP](../../../active-directory/active-directory-saas-sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Интеграция AAD с приложением SAP Cloud Platform Identity Authentication](../../../active-directory/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Настройка единого входа при помощи облачной платформы SAP](../../../active-directory/active-directory-saas-sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Интеграция AAD с SAP NetWeaver](../../../active-directory/active-directory-saas-sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Интеграция AAD с SAP Business ByDesign](../../../active-directory/active-directory-saas-sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Интеграция AAD с СУБД SAP HANA](../../../active-directory/active-directory-saas-saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
##[Единый вход SAML с панели запуска SAP Fiori при помощи Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad)
# Ресурсы
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/)
