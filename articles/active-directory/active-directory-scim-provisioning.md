---
title: Автоматизация подготовки приложений с помощью SCIM в Azure Active Directory | Документация Майкрософт
description: Azure Active Directory может выполнять автоматическую подготовку пользователей и групп для любого приложения или хранилища удостоверений, представляющим собой веб-службу с интерфейсом, определенным в спецификации протокола SCIM.
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: mtillman
editor: ''
ms.assetid: 4d86f3dc-e2d3-4bde-81a3-4a0e092551c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.openlocfilehash: 3b7f2f104046313e7d60cea4ef296f265d204aec
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/20/2018
---
# <a name="using-system-for-cross-domain-identity-management-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Использование SCIM для автоматической подготовки пользователей и групп Azure Active Directory для приложений

## <a name="overview"></a>Обзор
Azure Active Directory (Azure AD) может выполнять автоматическую подготовку пользователей и групп для любого приложения или хранилища удостоверений, представляющего собой веб-службу с интерфейсом, определенным в [спецификации протокола SCIM 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). Azure Active Directory может отправлять веб-службе запросы на создание, изменение или удаление назначенных пользователей и групп. Веб-служба может преобразовывать эти запросы в операции с целевым хранилищем удостоверений. 

![][0]
*Рисунок 1. Подготовка из Azure Active Directory в хранилище удостоверений через веб-службу*

Эта функция в сочетании с возможностью использовать сторонние приложения (BYOA) в Azure AD позволяет организовать режим единого входа и автоматической подготовки пользователей для приложений, которые предоставляют веб-службу SCIM или используют интерфейс на ее основе.

Существуют два варианта использования SCIM в Azure Active Directory.

* **Подготовка пользователей и групп в приложениях, поддерживающих SCIM**. Приложения, которые поддерживают SCIM 2.0 и используют токены носителя OAuth для аутентификации, работают с Azure AD без дополнительной настройки.
* **Сборка собственного решения подготовки для приложений, поддерживающих другую подготовку на основе API**. Для приложений, не поддерживающих SCIM, можно создать конечную точку SCIM для преобразования данных между конечной точкой SCIM Azure AD и любым API, поддерживаемым приложением для подготовки пользователей. Чтобы помочь вам в разработке конечной точки SCIM, мы предоставляем библиотеки Common Language Infrastructure (CLI), а также примеры кода, демонстрирующие подготовку конечной точки SCIM и преобразование сообщений SCIM.  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Подготовка пользователей и групп для приложений, поддерживающих SCIM
В Azure AD можно настроить автоматическую подготовку назначенных пользователей и групп для приложений, реализующих веб-службу [SCIM 2](https://tools.ietf.org/html/draft-ietf-scim-api-19) и принимающих токены носителя OAuth для аутентификации. В спецификации SCIM 2.0 приложения должны удовлетворять следующим требованиям.

* Поддержка создания пользователей и групп, как описано в разделе 3.3 протокола SCIM.  
* Поддержка изменения пользователей и групп с помощью запросов на исправление, как описано в разделе 3.5.2 протокола SCIM.  
* Поддержка получения известного ресурса, как описано в разделе 3.4.1 протокола SCIM.  
* Поддержка запросов пользователей и групп, как описано в разделе 3.4.2 протокола SCIM  (по умолчанию пользователи запрашиваются по атрибуту externalI, а группы по атрибуту displayName).  
* Поддержка запросов пользователей по идентификатору и диспетчеру, как описано в разделе 3.4.2 протокола SCIM.  
* Поддержка запросов групп по идентификатору и члену, как описано в разделе 3.4.2 протокола SCIM.  
* Принятие токенов носителя OAuth для авторизации, как описано в разделе 2.1 протокола SCIM.

Рекомендуется связаться с поставщиком приложения или ознакомиться с документацией поставщика для получения подтверждения соответствия этим требованиям.

### <a name="getting-started"></a>Приступая к работе
Описанные ранее в этой статье приложения, поддерживающие профиль SCIM, можно подключать к Azure Active Directory с помощью функции "Приложение не из коллекции", доступной в коллекции приложений Azure AD. После подключения Azure AD запускает процесс синхронизации каждые 20 минут, запрашивая конечную точку SCIM приложения для получения назначенных пользователей и групп, и создает или изменяет их согласно сведениям о назначении.

**Подключение приложения, поддерживающего SCIM:**

1. Войдите на [портал Azure](https://portal.azure.com). 
2. Выберите **"Azure Active Directory > Корпоративные приложения", затем выберите **Новое приложение > Все > Приложение не из коллекции**.
3. Введите имя приложения и щелкните значок **Добавить**, чтобы создать объект приложения.
    
  ![][1]
  *Рисунок 2. Использование коллекции приложений Azure AD*
    
4. На отобразившемся экране выберите вкладку **Подготовка** в левом столбце.
5. В меню **Режим подготовки к работе** выберите **Автоматический**.
    
  ![][2]
  *Рисунок 3. Настройка автоматической подготовки пользователей на портале Azure*
    
6. В поле **URL-адрес клиента** введите URL-адрес конечной точки SCIM приложения. Пример: https://api.contoso.com/scim/v2/
7. Если конечной точке SCIM требуется токен носителя OAuth от издателя, отличного от Azure AD, скопируйте необходимый токен носителя OAuth в необязательное для заполнения поле **Секретный токен**. Если оставить это поле пустым, то Azure AD будет добавлять в каждый запрос токен носителя OAuth, выданный Azure AD. Приложения, использующие Azure AD в качестве поставщика удостоверений, могут проверить этот выданный Azure AD токен.
8. Нажмите кнопку **Проверить подключение**. Azure Active Directory попробует подключиться к конечной точке SCIM. Если подключиться не удастся, отобразятся сведения об ошибке.  
9. Если подключиться к приложению удалось, нажмите кнопку **Сохранить**, чтобы сохранить учетные данные администратора.
10. В разделе **Сопоставления** доступны на выбор два набора сопоставлений атрибутов: для объектов-пользователей и для объектов групп. Выберите каждый из них, чтобы просмотреть атрибуты, которые синхронизируются из Azure Active Directory с вашим приложением. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления пользователей и групп в вашем приложении для операций обновления. Нажмите кнопку "Сохранить", чтобы подтвердить все изменения.

    >[!NOTE]
    >При необходимости можно отключить синхронизацию объектов групп, отключив сопоставление групп. 

11. Поле **Область** в разделе **Параметры** определяет, какие пользователи или группы синхронизируются. Если выбрать параметр "Sync only assigned users and groups" (Синхронизировать только назначенных пользователей и группы) (рекомендуется), то будут синхронизированы только пользователи и группы, назначенные на вкладке **Пользователи и группы**.
12. После завершения настройки измените значение параметра **Состояние подготовки** для **Включено**.
13. Нажмите кнопку **Сохранить**, чтобы запустить службу подготовки Azure AD. 
14. Если используется синхронизация только назначенных пользователей и групп (рекомендуется), не забудьте перейти на вкладку **Пользователи и группы** и назначить пользователей и (или) группы, которые требуется синхронизировать.

После запуска начальной синхронизации для отслеживания хода выполнения можно использовать вкладку **Журналы аудита**. На ней отображаются все действия, выполняемые службой подготовки с приложением. Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

>[!NOTE]
>Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Последующие операции синхронизации выполняются примерно каждые 20 минут, при условии, что служба запущена. 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>Создание собственного решения подготовки для любого приложения
Создав веб-службу SCIM, взаимодействующую с Azure Active Directory, можно включить единый вход и автоматическую подготовку пользователей практически для любого приложения, которое предоставляет API подготовки пользователей REST или SOAP.

Вот как это работает

1. Azure AD предоставляет библиотеку CLI с именем [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Системные интеграторы и разработчики на основе этой библиотеки могут создавать и развертывать SCIM-совместимые конечные точки, через которые Azure AD сможет подключаться к хранилищу удостоверений любого приложения.
2. Веб-служба реализует механизмы сопоставления стандартной пользовательской схемы c пользовательской схемой и протоколом, которые использует приложение.
3. Azure AD хранит URL-адрес конечной точки как часть информации о пользовательском приложении в коллекции приложений.
4. Администратор назначает для этого приложения пользователей и группы в Azure AD. Azure AD помещает назначенных пользователей в очередь для синхронизации с целевым приложением. Процесс синхронизации каждые 20 минут выполняет задания из этой очереди.

### <a name="code-samples"></a>Примеры кода
Чтобы вам было проще разобраться, мы предлагаем [примеры кода](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) , которые создают конечную точку веб-службы SCIM и демонстрируют автоматическую подготовку пользователей. В одном примере поставщик службы использует файл, в котором пользователи и группы представлены строками значений, разделенных запятыми.  Во втором примере поставщик использует службу идентификации и управления доступом Amazon Web Services.  

**Предварительные требования**

* Visual Studio 2013 или более поздней версии
* [Пакет Azure SDK для .NET](https://azure.microsoft.com/downloads/)
* Компьютер под управлением Windows, поддерживающий платформу ASP.NET 4.5, на котором будет размещена конечная точка SCIM. Этот компьютер должен быть доступен из облака.
* [Подписка Azure с пробной или лицензированной версией Azure AD Premium.](https://azure.microsoft.com/services/active-directory/)
* Для примера с Amazon AWS нужны библиотеки из [набора средств AWS для Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Дополнительная информация есть в файле сведений, включенном в пример.

### <a name="getting-started"></a>Приступая к работе
Начнем с самого простого способа реализации конечной точки SCIM. Этот пример кода принимает запросы на подготовку от Azure AD и сохраняет пользователей в файл формата CSV (значения, разделенные запятыми).

**Создание конечной точки SCIM из этого примера:**

1. Скачайте пакет с примером кода со страницы [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Распакуйте пакет и поместите его в любую удобную папку на компьютере Windows, например C:\AzureAD-BYOA-Provisioning-Samples.
3. Запустите в Visual Studio решение FileProvisioningAgent из этой папки.
4. Выберите **Инструменты > Library Package Manager (Диспетчер пакетов библиотеки) > Консоль диспетчера пакетов** и выполните приведенные ниже команды, чтобы разрешить ссылки на решения в проекте FileProvisioningAgent.
  ```` 
   Install-Package Microsoft.SystemForCrossDomainIdentityManagement
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   Install-Package Microsoft.Owin.Diagnostics
   Install-Package Microsoft.Owin.Host.SystemWeb
  ````
5. Соберите проект FileProvisioningAgent.
6. Запустите в Windows приложение командной строки (с правами администратора) и с помощью команды **cd** перейдите в каталог **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug**.
7. Выполните приведенную ниже команду, заменив <ip-address> IP-адресом или доменным именем компьютера Windows.
  ````   
   FileAgnt.exe http://<ip-address>:9000 TargetFile.csv
  ````
8. В Windows откройте раздел **Параметры Windows > Параметры сети и Интернета**, выберите **Брандмауэр Windows > Дополнительные параметры** и создайте **правило для входящего трафика**, разрешающее входящий доступ к порту 9000.
9. Если компьютер Windows подключен через маршрутизатор, то на этом маршрутизаторе нужно настроить преобразование сетевых адресов между внешним портом 9000 и портом 9000 на компьютере Windows. Это необходимо, чтобы служба Azure AD могла получить доступ к этой конечной точке в облаке.

**Регистрация примера конечной точки SCIM в Azure AD:**

1. Войдите на [портал Azure](https://portal.azure.com). 
2. Выберите **"Azure Active Directory > Корпоративные приложения", затем выберите **Новое приложение > Все > Приложение не из коллекции**.
3. Введите имя приложения и щелкните значок **Добавить**, чтобы создать объект приложения. Созданный объект приложения будет представлять не только конечную точку SCIM, но и все целевое приложение, для которого вы настраиваете подготовку пользователей и единый вход.
4. На отобразившемся экране выберите вкладку **Подготовка** в левом столбце.
5. В меню **Режим подготовки к работе** выберите **Автоматический**.
    
  ![][2]
  *Рисунок 4. Настройка автоматической подготовки пользователей на портале Azure*
    
6. В поле **URL-адрес клиента** введите доступные в Интернете URL-адрес и порт конечной точки SCIM. Это будет строка типа http://testmachine.contoso.com:9000 или http://<IP-адрес>:9000 /, где <IP-адрес> является IP-адресом, доступным в Интернете.  
7. Если конечной точке SCIM требуется токен носителя OAuth от издателя, отличного от Azure AD, скопируйте необходимый токен носителя OAuth в необязательное для заполнения поле **Секретный токен**. Если оставить это поле пустым, то Azure AD будет добавлять в каждый запрос токен носителя OAuth, выданный Azure AD. Приложения, использующие Azure AD в качестве поставщика удостоверений, могут проверить этот выданный Azure AD токен.
8. Нажмите кнопку **Проверить подключение**. Azure Active Directory попробует подключиться к конечной точке SCIM. Если подключиться не удастся, отобразятся сведения об ошибке.  
9. Если подключиться к приложению удалось, нажмите кнопку **Сохранить**, чтобы сохранить учетные данные администратора.
10. В разделе **Сопоставления** доступны на выбор два набора сопоставлений атрибутов: для объектов-пользователей и для объектов групп. Выберите каждый из них, чтобы просмотреть атрибуты, которые синхронизируются из Azure Active Directory с вашим приложением. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления пользователей и групп в вашем приложении для операций обновления. Нажмите кнопку "Сохранить", чтобы подтвердить все изменения.
11. Поле **Область** в разделе **Параметры** определяет, какие пользователи или группы синхронизируются. Если выбрать параметр "Sync only assigned users and groups" (Синхронизировать только назначенных пользователей и группы) (рекомендуется), то будут синхронизированы только пользователи и группы, назначенные на вкладке **Пользователи и группы**.
12. После завершения настройки измените значение параметра **Состояние подготовки** для **Включено**.
13. Нажмите кнопку **Сохранить**, чтобы запустить службу подготовки Azure AD. 
14. Если используется синхронизация только назначенных пользователей и групп (рекомендуется), не забудьте перейти на вкладку **Пользователи и группы** и назначить пользователей и (или) группы, которые требуется синхронизировать.

После запуска начальной синхронизации для отслеживания хода выполнения можно использовать вкладку **Журналы аудита**. На ней отображаются все действия, выполняемые службой подготовки с приложением. Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

Наконец, последний этап проверки примера. Откройте файл TargetFile.csv в папке \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug на компьютере Windows. Когда процесс подготовки будет запущен, в этом файле отобразятся сведения обо всех пользователях и группах, которые были назначены и подготовлены.

### <a name="development-libraries"></a>Библиотеки для разработчика
Чтобы разработать свою веб-службу, которая соответствует спецификации SCIM, ознакомьтесь с этими библиотеками Майкрософт, которые помогут ускорить процесс разработки. 

1. Библиотеки Common Language Infrastructure (CLI) для использования с языками, основанными на этой инфраструктуре, например C#. Одна из этих библиотек, [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), объявляет интерфейс, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, показанный на следующем рисунке. Разработчик, использующий библиотеки, будет реализовывать этот интерфейс с помощью класса, на который в общем случае можно ссылаться как на поставщик. Эти библиотеки позволяют разработчику развернуть веб-службу, которая соответствует спецификации SCIM. Эта веб-служба может размещаться в службах IIS или любой исполняемой сборке Common Language Infrastructure. Запрос преобразовывается в вызовы методов поставщика, которые программирует разработчик для управления хранилищем удостоверений.
  
  ![][3]
  
2. [Обработчики ExpressRoute](http://expressjs.com/guide/routing.html) используются для анализа объектов запроса Node.js, которые представляют собой вызовы (как определено в спецификации SCIM), направленные к веб-службе Node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Создание пользовательской конечной точки SCIM
Используя библиотеки CLI, разработчики могут размещать свои службы в любой исполняемой сборке Common Language Infrastructure или в службах IIS. Ниже приведен пример кода для размещения службы в исполняемой сборке по адресу http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Эта служба должна иметь HTTP-адрес и сертификат проверки подлинности сервера одного из следующих корневых центров сертификации: 

* CNNIC;
* Comodo;
* CyberTrust;
* DigiCert;
* GeoTrust;
* GlobalSign;
* Go Daddy;
* VeriSign;
* WoSign.

Сертификат проверки подлинности сервера можно привязать к порту на узле Windows с помощью служебной программы сетевой оболочки. 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Здесь в качестве значения аргумента certhash предоставляется отпечаток сертификата, а в качестве значения аргумента appid — произвольный глобальный уникальный идентификатор.  

Чтобы разместить службу в службах IIS, разработчик должен создать сборку библиотеки кода CLA и определить класс Startup в пространстве имен по умолчанию для этой сборки.  Ниже приведен пример такого класса. 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Обработка аутентификации на конечной точке
Запросы от Azure Active Directory содержат токен носителя OAuth 2.0.   Любая веб-служба, которая получит запрос, должна убедиться, что токен выдала служба Azure Active Directory от имени клиента Azure Active Directory для доступа к веб-службе Azure Active Directory Graph.  В токене издатель идентифицируется с помощью утверждения iss, например "iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  В этом примере базовый адрес утверждения со значением https://sts.windows.net подтверждает, что издателем является Azure Active Directory. Сегмент относительного адреса (cbb1a5ac-f33b-45fa-9bf5-f37db0fed422) — это уникальный идентификатор клиента Azure Active Directory, от имени которого выдан токен.  Если токен выпущен для доступа к веб-службе Azure Active Directory Graph, то утверждение aud такого токена должно иметь значение 00000002-0000-0000-c000-000000000000, то есть значение идентификатора этой веб-службы.  

Разработчики, которые используют для создания службы SCIM предоставленные корпорацией Майкрософт библиотеки CLA, могут аутентифицировать запросы от Azure Active Directory с помощью пакета Microsoft.Owin.Security.ActiveDirectory. Для этого нужно выполнить следующие действия. 

1. Реализуйте в поставщике свойство Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior, которое будет возвращать метод, вызываемый при каждом запуске службы. 

  ````
    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }
  ````

2. Добавьте в этот метод приведенный ниже код. Для всех запросов к любой из конечных точек службы этот метод будет проверять, является ли отправителем запроса служба Azure Active Directory, выпустившая токен от имени указанного клиента для доступа к веб-службе Azure AD Graph. 

  ````
    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }
  ````


## <a name="user-and-group-schema"></a>Схема пользователей и групп
Azure Active Directory может предоставлять веб-службе SCIM ресурсы двух типов.  Этими типами являются пользователи и группы.  

Ресурсы пользователя определяются с помощью идентификатора схемы urn:ietf:params:scim:schemas:extension:enterprise:2.0:User, который включен в эту спецификацию протокола: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  Ниже в таблице 1 приводится используемое по умолчанию сопоставление атрибутов пользователей в Azure Active Directory и атрибутов ресурсов типа urn:ietf:params:scim:schemas:extension:enterprise:2.0:User.  

Ресурсы группы определяются с помощью идентификатора схемы http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Ниже в таблице 2 показано используемое по умолчанию сопоставление атрибутов групп в Azure Active Directory и атрибутов ресурсов http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  

### <a name="table-1-default-user-attribute-mapping"></a>Таблица 1. Сопоставление атрибутов пользователя по умолчанию
| Пользователь Azure Active Directory | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User |
| --- | --- |
| IsSoftDeleted |активно |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| objectId |id |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Таблица 2. Сопоставление атрибутов группы по умолчанию
| Группа Azure Active Directory | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| displayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |id |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="user-provisioning-and-de-provisioning"></a>Подготовка и отзыв пользователей
На рисунке ниже показаны сообщения, которые Azure Active Directory отправляет службе SCIM для управления жизненным циклом пользователя в стороннем хранилище удостоверений. Также схема демонстрирует, как служба SCIM, реализованная с помощью библиотек CLI корпорации Майкрософт для создания таких служб, преобразовывает эти запросы в вызовы методов поставщика.  

![][4]
*Рисунок 5. Последовательность действий при подготовке и отзыве пользователя*

1. Azure Active Directory запрашивает службу пользователя, у которого значение атрибута externalId совпадает со значением атрибута mailNickname пользователя Azure AD. Запрос имеет вид HTTP-запроса, пример которого приведен ниже. Значение jyoung в этом запросе обозначает идентификатор mailNickname пользователя Azure Active Directory. 
  ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
  ````
  Если служба создана с использованием библиотек Common Language Infrastructure корпорации Майкрософт для реализации служб SCIM, то такой запрос будет преобразован в вызов метода Query поставщика службы.  Подпись этого метода будет выглядеть так: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
  ````
  Ниже приводится определение интерфейса Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters. 
  ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }

    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }
  ````
  В вышеприведенном примере запроса пользователя с определенным значением атрибута externalId в метод Query передаются следующие значения аргументов. 
  * parameters.AlternateFilters.Count: 1
  * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
  * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
  * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. Если на запрос пользователя с определенным значением атрибута externalId, совпадающим со значением атрибута mailNickname пользователя, веб-служба вернет ответ, не содержащий пользователей, то Azure Active Directory отправит запрос к этой службе на подготовку пользователя, соответствующего пользователю в Azure Active Directory.  Ниже приведен пример такого запроса. 
  ````
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
  ````
  Библиотеки CLI, разработанные корпорацией Майкрософт для реализации служб SCIM, преобразуют этот запрос в вызов метода Create поставщика услуг.  Подпись метода Create будет выглядеть так: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
  ````
  В запросе на подготовку пользователя в аргументе resource передается экземпляр класса Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser, определенного в библиотеке Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Если запрос на подготовку пользователя завершается успешно, реализованный в службе метод должен вернуть экземпляр класса Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser, в котором свойство Identifier имеет значение уникального идентификатора подготовленного пользователя.  

3. Для обновления пользователя, уже существующего в хранилище удостоверений с интерфейсом SCIM, Azure Active Directory запросит у службы текущее состояние этого пользователя, отправив запрос следующего вида. 
  ````
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Если служба создана с использованием библиотек CLI корпорации Майкрософт для реализации служб SCIM, то такой запрос будет преобразован в вызов метода Retrieve поставщика службы.  Подпись метода Retrieve будет выглядеть так: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
  ````
  В приведенном выше примере запроса на получение текущего состояния пользователя в качестве значения аргумента parameters будет передан объект со следующими значениями свойств. 
  
  * Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. Если требуется обновить атрибут ссылки, Azure Active Directory передает службе запрос с требованием проверить, совпадает ли текущее значение атрибута ссылки в хранилище удостоверений с текущим значением этого атрибута в Azure Active Directory. Для пользователей таким способом запрашивается только значение атрибута manager. Ниже приведен пример запроса на проверку значения атрибута manager для определенного пользователя. 
  ````
    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...
  ````
  Значение id для параметра attributes в запросе означает, что, если существует объект пользователя, который соответствует выражению, переданному в параметре filter запроса, служба должна вернуть ресурс urn:ietf:params:scim:schemas:core:2.0:User или urn:ietf:params:scim:schemas:extension:enterprise:2.0:User, в котором установлено только значение атрибута id.  Значение атрибута **id** известно запросившей стороне. Оно включено в параметр filter запроса. Фактически этот атрибут запрашивается, чтобы узнать, существует ли такой объект. В ответе будет представлен минимально определенный ресурс, отвечающий критериям фильтра.   

  Если служба создана с использованием библиотек Common Language Infrastructure корпорации Майкрософт для реализации служб SCIM, то такой запрос будет преобразован в вызов метода Query поставщика службы. В нем передается аргумент parameters, значением которого будет объект со следующими свойствами. 
  
  * parameters.AlternateFilters.Count: 2
  * parameters.AlternateFilters.ElementAt(x).AttributePath: "id"
  * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
  * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
  * parameters.RequestedAttributePaths.ElementAt(0): "id"
  * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Здесь значение индекса «x» будет равно 0, а значение индекса «y» — 1 или наоборот, в зависимости от порядка выражений в параметре filter запроса.   

5. Ниже приведен пример запроса от Azure Active Directory к службе SCIM для обновления пользователя. 
  ````
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
  ````
  Библиотеки Microsoft CLI для реализации службы SCIM преобразуют такой запрос в вызов метода Update поставщика услуги. Подпись метода Update выглядит следующим образом. 
  ````
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }

    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }

      public string Value
      { get; set; }
    }
  ````
    В примере запроса на обновление пользователя в качестве значения аргумента patch передается объект со следующими значениями свойств. 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
  * (PatchRequest as PatchRequest2).Operations.Count: 1
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

6. Для отзыва пользователя из хранилища удостоверений, интерфейсом для которого является служба SCIM, Azure AD отправляет запрос следующего вида. 
  ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Если служба создана с использованием библиотек Common Language Infrastructure корпорации Майкрософт для реализации служб SCIM, то такой запрос будет преобразован в вызов метода Delete поставщика службы.   Подпись метода Delete будет выглядеть так: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
  ````
  В примере запроса на отзыв пользователя в качестве значения аргумента resourceIdentifier передается объект со следующими значениями свойств. 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="group-provisioning-and-de-provisioning"></a>Подготовка и отзыв групп
На рисунке ниже показаны сообщения, которые Azure AD отправляет службе SCIM для управления жизненным циклом группы в стороннем хранилище удостоверений.  Эти сообщения имеют три отличия от сообщений, относящихся к пользователям. 

* Схема ресурса группы определяется как `http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group`.  
* В запросах на получение групп по умолчанию предполагается, что из всех возвращаемых ресурсов исключается атрибут members.  
* Запросы, которые должны определить, имеет ли ссылочный атрибут указанное значение, применяются в отношении атрибута members.  

![][5]
*Рисунок 6. Последовательность действий при подготовке и отзыве группы*

## <a name="related-articles"></a>Связанные статьи
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)
* [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS](active-directory-saas-app-provisioning.md)
* [Настройка сопоставления атрибутов для подготовки пользователей](active-directory-saas-customizing-attribute-mappings.md)
* [Запись выражений для сопоставления атрибутов](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Фильтры области для подготовки пользователей](active-directory-saas-scoping-filters.md)
* [Уведомления о подготовке учетных записей](active-directory-saas-account-provisioning-notifications.md)
* [Список учебников по интеграции приложений SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[0]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[1]: ./media/active-directory-scim-provisioning/scim-figure-2a.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2b.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
