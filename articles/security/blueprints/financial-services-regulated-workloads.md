---
title: "Автоматизация Azure чертежом - финансовые услуги для рабочих нагрузок, регулируемые"
description: "Проект финансовых служб регулируемые рабочих нагрузок"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 17794288-9074-44b5-acc8-1dacceb3f56c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2017
ms.author: frasim
ms.openlocfilehash: 19e26c16866dada8dcff04a520ce4c208d67c365
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
---
# <a name="azure-blueprint-automation-financial-services-blueprint-for-regulated-workloads"></a>Azure план автоматизации: Регулируемые рабочих нагрузок проект финансовых служб

## <a name="overview"></a>Обзор

Финансовые службы проект регулируемые рабочих нагрузок помогает развернуть безопасность и соответствие платформы как услуги (PaaS) веб-приложения, предназначены для обработки конфиденциальных данных в облаке. Проект содержит автоматические скрипты и инструкции, демонстрирующие простой эталонной архитектуре и схема, которая позволяет упростить освоение решениях Microsoft Azure. Этот проект иллюстрирует-сквозного решения в соответствии с потребностями организаций, которым требуется способа уменьшить нагрузку и затраты на развертывание в облаке.

Этот проект предназначен для обеспечения соответствия требованиям строгой соответствует стандартам, заданным по американского института из Сертифицировано Public Бухгалтерия например - SOC 1, SOC 2, совета стандартам безопасности платежных карт отрасли данных DSS 3.2 и FFIEC для сбора, хранения и получения конфиденциальных финансовых данных. Он демонстрирует правильной обработки таких данных, развернув решение, которое управляет финансовых данных в безопасной совместимые, многоуровневые среды. Решение развертывается как узел узел решения на основе Azure PaaS. 

Проект предназначен для использования в качестве основы для клиентов, для построения и сведения о требованиях управления защиты данных в облаке. Решение не должны использоваться в рабочей среде, как-является, но чтобы понимания, проектирования и развертывания службы Azure; она должна за основу помочь пользователям использовать Microsoft Azure в режиме безопасность и соответствие.

Аудитор аккредитованное должен сертифицировать любого решения клиента производства, основанный на этот план. Решения может отличаться в зависимости от особенностей реализации каждого клиента» и «География.

Краткий обзор того, как работает это решение, просмотрите этот [видео](https://aka.ms/fsiblueprintvideo) , поясняет и демонстрирует его развертывания.

## <a name="solution-components"></a>Компоненты решения

Архитектура состоит из следующих компонентов и использует возможности развертывания из решения соответствия требованиям Azure PCI DSS.

- **Схема архитектуры**. На схеме показана эталонная архитектура, используемая для решения интернет-магазина Contoso.
- **Шаблоны развертывания**. В этом развертывании [шаблоны Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) используются для автоматического развертывания компонентов архитектуры в Microsoft Azure путем указания параметров конфигурации во время установки.
- **Автоматизированное развертывание скриптов**. Эти скрипты помогают развернуть комплексное решение. Скрипты состоят из:
    - Скрипт установки модуля и настройки [глобального администратора](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) позволяет установить и проверить правильность настройки необходимых модулей PowerShell и ролей глобального администратора. 
    - Сценарий установки PowerShell используется для развертывания комплексного решения, предоставляемого в виде ZIP-файла и BACPAC-файла, которые содержат предварительно созданную демонстрационную версию веб-приложения с [примером содержимого базы данных SQL](https://github.com/Microsoft/azure-sql-security-sample) . Исходный код этого решения доступен для изучения в [репозитории кода схемы обработки платежей][code-repo]. 

## <a name="architectural-diagram"></a>Схема архитектуры

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>Пользовательский сценарий

Следующие адреса проект с вариантом использования.

> В этом сценарии показано перемещение вымышленной веб-хранилища, конфиденциальные данные в PaaS облака решение на основе Azure. Пример решения иллюстрирует обработку и набор данных обычного пользователя и выбранных конфиденциальных данных. Эта работа заимствует план автоматизации Azure: обработки платежей для совместимых PCI DSS сред для обработки платежных карт. Дополнительные сведения о развертывании осуществляется [«Просмотрите и рекомендации для реализации»](https://aka.ms/pciblueprintprocessingoverview) документ предоставляет обзор сред, совместимое с PCI DSS.

### <a name="use-case"></a>Вариант использования 
Вызывается для небольших веб-хранилища *веб-хранилища Contoso* можно переместить финансовых данных, которые включают сведения о платеже клиента в облаке. 

Администратор веб-хранилища в Contoso требуется решение, которое можно быстро развернуть для достижения целей. Он будет использовать этот-экспериментальной (проверки Концепции) для обсуждения своей заинтересованными лицами, как Azure можно использовать для сбора, хранения и извлечения финансовые данные в соответствии с требованиями строгой соответствия.

> Вы будете нести ответственность за проведение соответствующих проверок безопасности и соответствия любого решения, созданного на основе архитектуры, используемой этим POC, поскольку требования могут варьироваться в зависимости от специфики реализации и вашего географического расположения. 

### <a name="elements-of-the-foundational-architecture"></a>Элементы базовой архитектуры

Базовая архитектура разработана со следующими фиктивными элементами:

Сайт домена `contosowebstore.com`

Роли пользователей используются для иллюстрации варианта использования и дают представление о пользовательском интерфейсе.

#### <a name="role-site-and-subscription-admin"></a>Роль: администратор сайта и подписки

|Элемент      |Пример|
|----------|------|
|Имя пользователя: |`adminXX@contosowebstore.com`|
| Имя: |`Global Admin Azure PCI Samples`|
|Тип пользователя:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- Учетная запись администратора не может прочитать немаскированные финансовых данных. Все действия регистрируются.
- Пользователь с учетной записью администратора не может управлять базой данных SQL или входить в нее.
- Active Directory и подписки, можно управлять учетной записи администратора.

#### <a name="role-sql-administrator"></a>Роль: администратор SQL

|Элемент      |Пример|
|----------|------|
|Имя пользователя: |`sqlAdmin@contosowebstore.com`|
| Имя: |`SQLADAdministrator PCI Samples`|
| Имя: |`SQL AD Administrator`|
|Фамилия: |`PCI Samples`|
|Тип пользователя:| `Administrator`|

- Учетной записи sqladmin нельзя просмотреть нефильтрованные финансовой информации. Все действия регистрируются.
- Учетная запись sqladmin можно управлять базы данных SQL.

#### <a name="role-clerk"></a>Роль: клерк

|Элемент      |Пример|
|----------|------|
|Имя пользователя:| `receptionist_EdnaB@contosowebstore.com`|
| Имя: |`Edna Benson`|
| Имя:| `Edna`|
|Фамилия:| `Benson`|
| Тип пользователя: |`Member`|

Эдна Бенсон — секретарь и бизнес-менеджер. Она отвечает за выставление счетов и точность сведений о клиенте. Это пользователь, который выполняет вход для всех действий с демо-сайтом интернет-магазина Contoso. Эдна имеет следующие права: 

- Edna можно создавать и считывать информацию о клиенте.
- изменение информации о клиентах;
- Edna можно перезаписать финансовой информации.
- Edna учетной записи не может просмотреть нефильтрованные финансовой информации.

> В веб-Contoso хранилища, он автоматически **Edna** пользователя для проверки возможности развернутой среды.

### <a name="contoso-webstore---estimated-pricing"></a>Интернет-магазин Contoso — примерная стоимость

Эта базовая архитектура и пример веб-приложения имеют ежемесячную структуру оплаты и стоимость использования в час, которые необходимо учитывать при определении размера решения. Эти затраты можно рассчитать с помощью [калькулятора расценок Azure](https://azure.microsoft.com/pricing/calculator/). Начиная с сентября 2017 года расчетная месячная стоимость этого решения составляет примерно 2500 долл. США, включая 1000 долл. США в месяц за использование ASE версии 2. Эти затраты будут зависеть от объема использования и могут быть изменены. Клиенту необходимо рассчитать свои ожидаемые ежемесячные затраты на момент развертывания для более точной оценки. 

Это решение использует приведенные ниже службы Azure. Подробные сведения об архитектуре развертывания см. в [этом разделе](#deployment-architecture).

>- Шлюз приложений
>- Azure Active Directory
>- Среда службы приложений версии 2
>- OMS Log Analytics.
>- Хранилище ключей Azure
>- группы сетевой безопасности;
>- База данных Azure SQL
>- Azure Load Balancer
>- Application Insights
>- Центр безопасности Azure
>- Веб-приложение Azure
>- Служба автоматизации Azure
>- Модули Runbook со службой автоматизации Azure
>- Azure DNS
>- Виртуальная сеть Azure
>- Виртуальная машина Azure
>- Группа ресурсов и политики Azure
>- Хранилище больших двоичных объектов Azure
>- Управление Azure Active Directory доступ на основе ролей (RBAC)

## <a name="deployment-architecture"></a>Архитектура развертывания

В следующем разделе подробно описываются компоненты разработки и реализации.

### <a name="network-segmentation-and-security"></a>Сегментация сети и безопасность

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>Шлюз приложений

Основная архитектура снижает риск возникновения уязвимостей системы безопасности с помощью шлюза приложения с брандмауэр веб-приложения (WAF) и набор правил OWASP включена. Доступны следующие дополнительные возможности:

- [сквозное SSL-подключение](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell);
- [Разгрузка SSL](/azure/application-gateway/application-gateway-ssl-portal) включена
- [Протокол TLS версий 1.0 и 1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) отключена
- [брандмауэр веб-приложения](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (режим WAF);
- [режим предотвращения](/azure/application-gateway/application-gateway-web-application-firewall-portal) с набором правил OWASP 3.0.
- [Ведение журнала диагностики](/azure/application-gateway/application-gateway-diagnostics) включена
- [пользовательские пробы работоспособности](/azure/application-gateway/application-gateway-create-gateway-portal);
- [Центр безопасности Azure](https://azure.microsoft.com/services/security-center) и [Azure Advisor](/azure/advisor/advisor-security-recommendations), которые обеспечивают дополнительную защиту и уведомления. Центр безопасности Azure также предоставляет систему репутации.

#### <a name="virtual-network"></a>Виртуальная сеть

Базовая архитектура определяет частную виртуальную сеть с пространством адресов 10.0.0.0/16.

#### <a name="network-security-groups"></a>Группы безопасности сети

Каждый из уровней сети имеет выделенную группу безопасности сети (NSG):

- группа безопасности сети периметра для брандмауэра и WAF шлюза приложения;
- группа безопасности сети для среды управления Jumpbox (узел-бастион);
- группа безопасности сети для среды службы приложений.

В каждой группе безопасности сети (NSG) открыты определенные порты и протоколы для безопасной и правильной работы решения. Дополнительные сведения см. в разделе [Группы безопасности сети](#network-security-groups).

Кроме того, для каждой NSG настроены следующие конфигурации:

- включенные [журналы диагностики и события](/azure/virtual-network/virtual-network-nsg-manage-log) хранятся в учетной записи хранения; 
- OMS Log Analytics подключена к [диагностике NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

 
#### <a name="subnets"></a>Подсети
 Убедитесь, что каждая подсеть связана с соответствующей NSG.

#### <a name="custom-domain-ssl-certificates"></a>SSL-сертификаты личного домена
 Трафик HTTPS разрешен с использованием SSL-сертификата личного домена.

### <a name="data-at-rest"></a>Неактивные данные

Архитектура защищает неактивные данные, используя шифрование, проверку базы данных и другие меры.

#### <a name="azure-storage"></a>Хранилище Azure

Для выполнения требований шифрования неактивных данных все [хранилища Azure](https://azure.microsoft.com/services/storage/) используют [шифрование службы хранения](/azure/storage/storage-service-encryption).

#### <a name="azure-sql-database"></a>Базы данных SQL Azure

В экземпляре базы данных Azure SQL используются следующие меры безопасности базы данных:

- [аутентификация и авторизация AD](/azure/sql-database/sql-database-aad-authentication);
- [аудит баз данных SQL](/azure/sql-database/sql-database-auditing-get-started);
- [Transparent Data Encryption (Прозрачное шифрование данных)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- [правила брандмауэра](/azure/sql-database/sql-database-firewall-configure) для обеспечения пулов рабочих ролей ASE и управления IP клиента;
- [обнаружение угроз SQL](/azure/sql-database/sql-database-threat-detection-get-started);
- [столбцы Always Encrypted](/azure/sql-database/sql-database-always-encrypted-azure-key-vault);
- [динамическое маскирование данных базы данных SQL Azure](/azure/sql-database/sql-database-dynamic-data-masking-get-started) с использованием скрипта PowerShell после развертывания.

### <a name="logging-and-auditing"></a>Ведение журналов и аудит

[Operations Management Suite (OMS)](/azure/operations-management-suite/) можно предоставить веб-хранилища Contoso подробных сведений в журнал всех системных и пользовательских действий, включения ведения журнала финансовые данные. Здесь можно просмотреть все изменения и проверить их правильность. 

- **Журналы действий.**  [Журналы действий](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) дают представление об операции, которые были выполнены с ресурсами в вашей подписке.
- **Журналы диагностики.**  [Журналы диагностики](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) всех журналов, создаваемых каждый ресурс. В этих журналах содержатся системные журналы событий Windows, журналы хранения больших двоичных объектов Azure, таблицы и очереди журналы.
- **Журналы брандмауэра.**  Шлюз приложений предоставляет полной диагностики и доступа к журналам. Журналы брандмауэра доступны для ресурсов шлюза приложений с включенным WAF.
- **Архивация журнала.**  Все журналы диагностики настраиваются для записи в централизованной и зашифрованные учетная запись хранилища Azure для архивации с помощью заданного срока хранения (2 дня). Затем журналы подключаются к Azure Log Analytics для обработки, хранения и отображения данных на панелях мониторинга. [Log Analytics](https://azure.microsoft.com/services/log-analytics) — это служба OMS, которая помогает собирать и анализировать данные, создаваемые ресурсами в облачных и локальных средах.

### <a name="encryption-and-secrets-management"></a>Шифрование и управление секретами

Веб-хранилища Contoso шифрует все конфиденциальные данные и использует хранилище ключей Azure для управления ключами и предотвратить извлечение CHD.

- [Хранилище ключей Azure](https://azure.microsoft.com/services/key-vault/) помогает защитить криптографические ключи и секреты, используемые облачными приложениями и службами. 
- [Прозрачное шифрование данных SQL](/sql/relational-databases/security/encryption/transparent-data-encryption) используется для шифрования всех финансовых данных клиента.
- Данные хранятся на диске с использованием [шифрования дисков Azure](/azure/security/azure-security-disk-encryption) и BitLocker.

### <a name="identity-management"></a>Управление удостоверениями

Следующие технологии обеспечивают возможности управления удостоверениями в среде Azure.

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) является мультитенантной облачной каталогами и удостоверениями служба управления Microsoft. Все пользователи решения создаются в Azure Active Directory, включая пользователей, обращающихся к базе данных SQL.
- Аутентификация приложения выполняется с использованием Azure AD. Дополнительные сведения см. в статье [Интеграция приложений с Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications). Кроме того шифрования столбцов базы данных также использует Azure AD для проверки подлинности приложения с базой данных SQL Azure. Дополнительные сведения см. в статье [Always Encrypted: защита конфиденциальных данных в Базе данных SQL и хранение ключей шифрования в хранилище ключей Azure](/azure/sql-database/sql-database-always-encrypted-azure-key-vault). 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) обнаруживает потенциальных уязвимостей, которые могут повлиять на удостоверения организации, настраивает автоматизированных ответов на обнаруженных подозрительных действий, связанных с удостоверениями вашей организации и исследует подозрительные инцидентов и выполняет соответствующее действие по их устранению.
- [Контроль доступа на основе ролей (RBAC) Azure](/azure/active-directory/role-based-access-control-configure) обеспечивает точное управление доступом для Azure. Доступ к подписке ограничен администратором подписки, а доступ к Azure Key Vault запрещен для всех пользователей.

Подробнее об использовании функций безопасности базы данных SQL Azure см. в примере [демонстрационного приложения Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample).
   
### <a name="web-and-compute-resources"></a>Веб-ресурсы и вычислительные ресурсы

#### <a name="app-service-environment"></a>Среда службы приложений

[Служба приложений Azure](/azure/app-service/) является управляемой службой для развертывания веб-приложений. Приложение интернет-магазина Contoso развертывается в качестве [веб-приложения службы приложений](/azure/app-service-web/app-service-web-overview).

[Среда службы приложений Azure (ASE версии 2)](/azure/app-service/app-service-environment/intro) — это компонент службы приложений Azure, предоставляющий полностью изолированную и выделенную среду для безопасного выполнения приложений службы приложений в большом масштабе. Это тарифный план уровня "Премиум", используемый этой базовой архитектурой для обеспечения соответствия PCI DSS.

Среды ASE изолированы, позволяют запускать приложения только одного клиента и всегда развернуты в виртуальной сети. Клиенты могут детально управлять входящим и исходящим сетевым трафиком приложений, а приложения могут создавать высокоскоростные безопасные подключения к локальным корпоративным ресурсам через виртуальные сети.

Использование сред ASE для этой архитектуры позволило использовать следующие средства управления и конфигурации:

- Разместить в защищенной виртуальной сети и правила безопасности сети
- ASE настроен самозаверяющий сертификат ILB для HTTPS-соединения
- [внутренняя балансировка нагрузки](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (режим 3);
- [Протокол TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings) отключена
- [Шифров TLS](/azure/app-service-web/app-service-app-service-environment-custom-settings) изменен
- управление [портами N/W для входящего трафика](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic); 
- [WAF - ограничения доступа к данным](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [База данных SQL трафика](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview) разрешено


#### <a name="jumpbox-bastion-host"></a>Jumpbox (узел-бастион)

Так как блокировки и защищается среды службы приложений, необходим механизм, позволяющий для всех выпусков DevOps или изменения, которые могут быть необходимы, такие как возможность наблюдения за веб-приложения с помощью Kudu. Виртуальная машина защищена позади подсистемы балансировки нагрузки NAT, который обеспечивает возможность подключения к виртуальной машине на порт, отличный от TCP 3389. 

Виртуальная машина создана как jumpbox (узел-бастион) со следующими конфигурациями:

-   [расширение защиты от вредоносных программ](/azure/security/azure-security-antimalware);
-   [расширение OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms);
-   [Расширение системы диагностики Azure](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure шифрование диска](/azure/security/azure-security-disk-encryption) с помощью хранилища ключей Azure 
-   [Политики автоматического завершения работы](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) уменьшить степень использования ресурсов виртуальных машин в случае использования

### <a name="security-and-malware-protection"></a>Безопасность и защита от вредоносных программ

[Центр безопасности Azure](https://azure.microsoft.com/services/security-center/) предоставляет централизованное представление состояния безопасности всех ресурсов Azure. Вы можете убедиться, что все необходимые настройки заданы правильно, а также найти ресурсы, которым нужно уделить внимание.  

[Azure Advisor](/azure/advisor/advisor-overview) — это персонализированный облачный консультант, который поможет следовать рекомендациям по оптимизации развернутых служб Azure. Выполняется анализ конфигурации ресурсов и данные телеметрии использования и затем рекомендует решения, которые могут помочь повысить экономичности, производительность, высокий уровень доступности и безопасности ресурсов Azure.

[Microsoft Antimalware](/azure/security/azure-security-antimalware) для виртуальных машин и облачных служб Azure возможности защиты в реальном времени, которая помогает обнаружения и удаления вирусов, шпионских и других вредоносных программ, можно настроить оповещения, когда известен вредоносными или нежелательными программное обеспечение пытается установиться или запуститься на Azure систем.

### <a name="operations-management"></a>Operations Management

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://azure.microsoft.com/services/application-insights/) позволяет получать практическую информацию с помощью средств управления производительностью приложений и мгновенной аналитики.

#### <a name="log-analytics"></a>Служба Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) — это служба в Operations Management Suite (OMS), которая помогает собирать и анализировать данные, создаваемые ресурсами в облачных и локальных средах.

#### <a name="oms-solutions"></a>Решения OMS

Следует учесть и настроить следующие дополнительные решения OMS: 
- [Анализ журнала действий](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [Анализ сетевой активности Azure](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Azure SQL Analytics](/azure/log-analytics/log-analytics-azure-sql)
- [Отслеживание изменений](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Анализ хранилища ключей](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Схема услуги](/azure/operations-management-suite/operations-management-suite-service-map)
- [Безопасность и аудит](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Защита от вредоносных программ](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Управление обновлениями](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>Интеграция центра безопасности

По умолчанию развертывания предназначена для предоставления базовых показателей рекомендаций центра обеспечения безопасности, которые показывают состояние работоспособности и безопасной конфигурации. Узнайте, как включать сбор данных в Центре безопасности Azure. Дополнительные сведения см. в статье [Краткое руководство по центру безопасности Azure](/azure/security-center/security-center-get-started).

## <a name="deploy-the-solution"></a>Развертывание решения

Компоненты для развертывания этого решения доступны в [репозиторий кода план обработки платежей][code-repo]. Для развертывания базовой архитектуры требуется несколько шагов, выполняемых с помощью Microsoft PowerShell версии 5. Чтобы подключиться к веб-сайту, вы должны предоставить имя личного домена (например, contoso.com). Его нужно указать с помощь параметра `-customHostName` на шаге 2. Дополнительные сведения см. в статье [Приобретение имени личного домена для веб-приложений Azure](/azure/app-service-web/custom-dns-web-site-buydomains-web-app). Имя личного домена не требуется для успешного развертывания и запуска решения, но вы не сможете подключиться к веб-сайту в демонстрационных целях.

Скрипты добавляют пользователей домена в указанный вами клиент Azure AD. Майкрософт рекомендует создание новых Azure AD клиента для использования в качестве теста.

Если возникли проблемы во время развертывания, см. страницу [вопросов и ответов и сведения об устранении неполадок](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md).

Корпорация Майкрософт настоятельно рекомендует использование чистой установки PowerShell для развертывания решения. Кроме того, убедитесь, что вы используете последние модули, необходимые для правильного выполнения скриптов установки. В этом примере входит jumpbox (бастионе) и выполняет следующие команды. Обратите внимание, что это позволяет выполнять команду личного домена.


1. Установите необходимые модули и правильно настройте роли администратора.
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    Подробные сведения об использовании см. в статье [с описанием настройки учетной записи администратора и разрешений](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md).
    
2. Установка решения управления обновлениями. 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    Инструкции по использованию см. в статье [о развертывании и настройке ресурсов Azure](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md).
    
3. Ведение журналов OMS и мониторинг. При развертывании решения [Microsoft Operations Management Suite (OMS)](/azure/operations-management-suite/operations-management-suite-overview) рабочей области можно открыть и образец шаблонов, имеющихся в хранилище решений можно использовать для иллюстрации того, как могут настраиваться информационной панели мониторинга . Образцы шаблонов OMS, см. в разделе [omsDashboards папки](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md). Обратите внимание на то, что для правильного развертывания шаблонов должны собираться данные в OMS. Это может занять один час или больше, в зависимости от активности сайта.
 
    При настройке ведения журналов OMS рассмотрите возможность включения этих ресурсов:
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverfarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/Vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>Модель рисков

Схема потока данных (DFD) и пример модели угроз для [модели угроз для схемы обработки платежей](https://aka.ms/pciblueprintthreatmodel) интернет-магазина Contoso.

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>Таблица обязанностей клиента

Клиенты несут ответственность за хранение копии [таблица Сводка ответственности](https://aka.ms/fsiblueprintcrm), которой приведены требования к FFIEC, которые отвечают клиента и те, которые отвечают Microsoft Azure.



## <a name="disclaimer-and-acknowledgments"></a>Заявление об отказе и подтверждений

*Сентябрь 2017 г.*

- Этот документ является исключительно информационным. МАЙКРОСОФТ И AVYAN НЕ ПРЕДОСТАВЛЯЕТ НИКАКИХ ГАРАНТИЙ, ЯВНЫХ, КОСВЕННЫХ ИЛИ ПРЕДУСМОТРЕННЫХ ЗАКОНОМ, В ОТНОШЕНИИ ИНФОРМАЦИИ В ЭТОМ ДОКУМЕНТЕ. Данный документ предоставляется "как есть". Сведения и мнения, содержащиеся в этом документе, включая URL-адреса и другие ссылки на веб-сайта в Интернете, могут изменяться без предварительного уведомления. Клиенты, читающие этот документ, берут ответственность за его использование на себя.  
- Настоящий документ не предоставляет клиентам юридических прав на интеллектуальную собственность в отношении продуктов или решений корпорации Майкрософт или Avyan.  
- Клиенты могут скопировать и использовать данный документ для внутренних справочных целей.  

  > [!NOTE]
  > Некоторые рекомендации в данной статье может привести к повышенной данных, сети или использования вычислительных ресурсов в Azure и может увеличить расходы лицензию или подписку Azure клиента.  

- Решение, описанное в этой статье, предназначено для использования в качестве базовой архитектуры и не должно использоваться в рабочей среде "как есть". Обеспечение соответствия требует, что клиентам необходимо проконсультироваться с их Аудитор для проверки решения конечного потребителя.  
- Все имена клиентов, записи транзакций и любые связанные с ними данные на этой странице являются фиктивными, созданы для базовой архитектуры и предоставлены только для иллюстрации. Любое сходство с реальными ситуациями случайно.  
- Это решение было разработано совместно корпорацией Майкрософт и Avyan Consulting и поставляется с [лицензией MIT](https://opensource.org/licenses/MIT).

### <a name="document-authors"></a>Авторы документа

* *Фрэнк Симорджей (Frank Simorjay) (Майкрософт)*  

[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "Репозиторий кода"
