# [Документация по облачным службам](index.md)

# Обзор
## [Что такое облачные службы](cloud-services-choose-me.md)
## [Упаковка и файлы конфигурации облачной службы](cloud-services-model-and-package.md)

# Начало работы
## [Пример облачной службы .NET](cloud-services-dotnet-get-started.md)
## [Пример создания облачной службы на языке Python в Visual Studio](cloud-services-python-ptvs.md)
## [Настройка гибридного кластера HPC с помощью пакета Microsoft HPC](cloud-services-setup-hybrid-hpcpack-cluster.md)

# Практическое руководство
## План
### [Размеры виртуальных машин](cloud-services-sizes-specs.md)
### [Обновления](cloud-services-update-azure-service.md)

## Разработка
### [Создание веб-ролей и рабочих ролей PHP](../cloud-services-php-create-web-role.md)
### [Сборка и развертывание приложения Node.js](cloud-services-nodejs-develop-deploy-app.md)
### [Сборка веб-приложения Node.js с использованием Express](cloud-services-nodejs-develop-deploy-express-app.md)
### Хранилище и Visual Studio
#### [Хранилище BLOB-объектов и подключенные службы](../visual-studio/vs-storage-cloud-services-getting-started-blobs.md)
#### [Хранилище очередей и подключенные службы](../visual-studio/vs-storage-cloud-services-getting-started-queues.md)
#### [Хранилище таблиц и подключенные службы](../visual-studio/vs-storage-cloud-services-getting-started-tables.md)
### [Настройка правил трафика для роли](cloud-services-enable-communication-role-instances.md)
### [Обработка событий жизненного цикла облачной службы](cloud-services-role-lifecycle-dotnet.md)
### [Socket.IO (Node.js)](cloud-services-nodejs-chat-app-socketio.md)
### [Использование Twilio для телефонных звонков (.NET)](../partner-twilio-cloud-services-dotnet-phone-call-web-role.md)

### Настройка задач запуска
#### [Создание задач запуска](cloud-services-startup-tasks.md)
#### [Распространенные задачи запуска](cloud-services-startup-tasks-common.md)
#### [Установка .NET в роли облачной службы с помощью задачи](cloud-services-dotnet-install-dotnet.md)

### Настройка удаленного рабочего стола
#### [Портал](cloud-services-role-enable-remote-desktop-new-portal.md)
#### [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
#### [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

## Развертывание
### [Создание и развертывание облачной службы на портале](cloud-services-how-to-create-deploy-portal.md)
### [Создание пустого контейнера облачной службы в PowerShell](cloud-services-powershell-create-cloud-container.md)
### [Настройка пользовательского имени домена](cloud-services-custom-domain-name-portal.md)
### [Подключение к пользовательскому контроллеру домена](cloud-services-connect-to-custom-domain.md)

## Управление службой
### [Общие задачи управления](cloud-services-how-to-manage-portal.md)
### [Настройка облачной службы](cloud-services-how-to-configure-portal.md)
### [Управление облачной службой с помощью службы автоматизации Azure](automation-manage-cloud-services.md)
### [Настройка автоматического масштабирования](cloud-services-how-to-scale-portal.md)
### [Использование языка Python для управления ресурсами Azure](cloud-services-python-how-to-use-service-management.md)
### [Устранение уязвимостей, связанных со спекулятивным выполнением команд](mitigate-se.md)

### [Исправления гостевой ОС](cloud-services-guestos-msrc-releases.md)
### Прекращение использования гостевой ОС
#### [Политика прекращения использования ](cloud-services-guestos-retirement-policy.md)
#### [Уведомление о прекращении использования семейства версий 1](cloud-services-guestos-family1-retirement.md)
### [Новости о выпуске гостевой ОС](cloud-services-guestos-update-matrix.md)
### [Памятка по конфигурации XPath для роли облачных служб](cloud-services-role-config-xpath.md)

## Управление сертификатами
### [Облачные службы и сертификаты управления](cloud-services-certs-create.md)
### [Настройка SSL](cloud-services-configure-ssl-certificate-portal.md)

## Мониторинг
### [Мониторинг облачной службы](cloud-services-how-to-monitor.md)
### [Использование счетчиков производительности](diagnostics-performance-counters.md)
### [Тестирование производительности](../vs-azure-tools-performance-profiling-cloud-services.md)
#### [Тестирование с помощью профилировщика Visual Studio](cloud-services-performance-testing-visual-studio-profiler.md)
### Включение диагностики
#### [Azure PowerShell](cloud-services-diagnostics-powershell.md)
#### [.NET](cloud-services-dotnet-diagnostics.md)
#### [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
### [Хранение и просмотр диагностических данных в службе хранилища Azure](cloud-services-dotnet-diagnostics-storage.md)
### [Трассировка облачной службы с помощью системы диагностики](cloud-services-dotnet-diagnostics-trace-flow.md)

## Устранение неполадок
### Отладка 
#### [Варианты отладки для облачной службы](../vs-azure-tools-debugging-cloud-services-overview.md)
#### [Отладка локальной облачной службы с помощью Visual Studio](../vs-azure-tools-debug-cloud-services-virtual-machines.md)
#### [Отладка опубликованной облачной службы с помощью Visual Studio](../vs-azure-tools-intellitrace-debug-published-cloud-services.md)
### [Сбой выделения ресурсов для облачной службы](cloud-services-allocation-failures.md)
### [Распространенные причины перезапуска ролей облачной службы](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)
### [Используемый по умолчанию размер папки TEMP слишком мал для роли](cloud-services-troubleshoot-default-temp-folder-size-too-small-web-worker-role.md)
### [Распространенные проблемы с развертыванием](cloud-services-troubleshoot-deployment-problems.md)
### [Роль не запускается](cloud-services-troubleshoot-roles-that-fail-start.md)
### [Инструкции по восстановлению](cloud-services-disaster-recovery-guidance.md)
### Часто задаваемые вопросы об облачных службах
#### [Часто задаваемые вопросы о доступности приложений и служб](cloud-services-application-and-service-availability-faq.md)
#### [Часто задаваемые вопросы о настройке и управлении](cloud-services-configuration-and-management-faq.md)
#### [Часто задаваемые вопросы о подключениях и сетях](cloud-services-connectivity-and-networking-faq.md)
#### [Часто задаваемые вопросы о развертывании](cloud-services-deployment-faq.md)

# Справочные материалы
## [Примеры кода](https://azure.microsoft.com/resources/samples/?service=cloud-services)
## [Схема XML (CSDEF-файл)](schema-csdef-file.md)
### [Схема LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
### [Схема WebRole](schema-csdef-webrole.md)
### [Схема WorkerRole](schema-csdef-workerrole.md)
### [Схема NetworkTrafficRules](schema-csdef-networktrafficrules.md)
## [Схема XML (CSCFG-файл)](schema-cscfg-file.md)
### [Схема Role](schema-cscfg-role.md)
### [Схема NetworkConfiguration](schema-cscfg-networkconfiguration.md)
## [REST](/rest/api/compute/cloudservices/)

# Ресурсы
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/?category=compute)
## [Схема обучения](https://azure.microsoft.com/documentation/learning-paths/cloud-services/)
## [Форум MSDN](https://social.msdn.microsoft.com/Forums/en-us/home?forum=windowsazuredevelopment)
## [Цены](https://azure.microsoft.com/pricing/details/cloud-services/)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=cloud-services&updatetype=&platform=)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=cloud-services)
