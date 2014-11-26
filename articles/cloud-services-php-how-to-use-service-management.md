<properties linkid="develop-php-how-to-guides-service-management" urlDisplayName="Service Management" pageTitle="How to use Azure service management APIs (PHP)" metaKeywords="" description="Learn how to use the Azure PHP Service Management APIs to manage cloud services and other Azure applications." metaCanonical="" services="" documentationCenter="PHP" title="How to use Service Management from PHP" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Использование управления службами в PHP

В этом руководстве показано, как программными средствами PHP выполнять общие задачи управления службами. Класс [ServiceManagementRestProxy][ServiceManagementRestProxy] в [Azure SDK для PHP][Azure SDK для PHP] поддерживает программный доступ к большинству функциональных возможностей, связанных с управлением службами, которые доступны на [портале управления][портале управления] (например, **создание, обновление и удаление облачных служб, развертываний и территориальных групп**). Эта возможность может быть полезна при создании приложений, которым требуется программный доступ к управлению службами.

## Оглавление

-   [Что такое управление службами][Что такое управление службами]
-   [Основные понятия][Основные понятия]
-   [Создание приложения PHP][Создание приложения PHP]
-   [Получение клиентских библиотек Azure][Получение клиентских библиотек Azure]
-   [Практическое руководство. Подключение к управлению службами][Практическое руководство. Подключение к управлению службами]
-   [Практическое руководство. Отображение списка доступных расположений][Практическое руководство. Отображение списка доступных расположений]
-   [Практическое руководство. Создание облачной службы][Практическое руководство. Создание облачной службы]
-   [Практическое руководство. Удаление облачной службы][Практическое руководство. Удаление облачной службы]
-   [Практическое руководство. Создание развертывания][Практическое руководство. Создание развертывания]
-   [Практическое руководство. Обновление развертывания][Практическое руководство. Обновление развертывания]
-   [Практическое руководство. Перемещение развертываний между промежуточным хранением и рабочим расположением][Практическое руководство. Перемещение развертываний между промежуточным хранением и рабочим расположением]
-   [Практическое руководство. Удаление развертывания][Практическое руководство. Удаление развертывания]
-   [Практическое руководство. Создание службы хранения][Практическое руководство. Создание службы хранения]
-   [Практическое руководство. Удаление службы хранения][Практическое руководство. Удаление службы хранения]
-   [Практическое руководство. Создание территориальной группы][Практическое руководство. Создание территориальной группы]
-   [Практическое руководство. Удаление территориальной группы][Практическое руководство. Удаление территориальной группы]

## <span id="WhatIs"></span></a>Что такое управление службами

API управления службой обеспечивает программный доступ к большинству функциональных возможностей управления службами, доступных через [портал управления][портале управления]. Пакет Azure SDK для PHP позволяет управлять облачными службами, учетными записями хранения и территориальными группами.

Чтобы использовать API-интерфейс управления службами, необходимо [создать учетную запись Azure][создать учетную запись Azure].

## <span id="Concepts"></span></a>Основные понятия

Пакет Azure SDK для PHP служит оболочкой для [API-интерфейса управления службами Azure][API-интерфейса управления службами Azure], который является интерфейсом REST API. Все операции API выполняются по SSL и проходят взаимную проверку подлинности с использованием сертификатов X.509 v3. Доступ к службе управления можно получить из службы, работающей в Azure или непосредственно через Интернет из любого приложения, которое может отправить HTTPS-запрос и получить HTTPS-ответ.

## <span id="CreateApplication"></span></a>Создание приложения на PHP

Единственным требованием для создания приложения на PHP, которое использует возможности Azure по управлению службами, является ссылка на классы в пакете Windows Azure SDK для PHP непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

В этом руководстве будут использоваться компоненты службы, которые могут быть вызваны локально в приложении на PHP или в коде, работающем в веб-роли, роли рабочего процесса или на веб-сайте Azure.

## <span id="GetClientLibraries"></span></a>Получение клиентских библиотек Azure

[WACOM.INCLUDE [получение-клиент-библиотеки](../includes/get-client-libraries.md)]

## <span id="Connect"></span></a>Практическое руководство. Подключение к управлению службами

Чтобы подключиться к конечной точке управления службами, необходимо указать ИД подписки Azure и путь к допустимому сертификату управления. Идентификатор подписки можно получить на [портале управления][портале управления], а сертификаты управления можно создать несколькими способами. В этом руководстве используется пакет средств [OpenSSL][OpenSSL], который можно [загрузить для Windows][загрузить для Windows] и выполнить в консоли.

Фактически необходимо создать два сертификата: один для сервера (файл `.cer`) и один для клиента (файл `.pem`). Чтобы создать `.pem`-файл, выполните следующие действия.

    `openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Чтобы создать сертификат `.cer`, выполните следующие действия.

    `openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Дополнительные сведения о сертификатах Azure см. в разделе [Обзор сертификатов в Azure][Обзор сертификатов в Azure]. Полное описание параметров OpenSSL см. в документации по адресу [][]<http://www.openssl.org/docs/apps/openssl.html></a>

Если вы загрузили и импортировали файл со своими настройками публикации с помощью [средств командной строки Azure][средств командной строки Azure], можно воспользоваться `.pem`-файлом, который создается этими средствами, вместо создания такого файла собственноручно. Средства создают `.cer`-файл для вас и загружают его в Azure, а также помещают соответствующий `.pem`-файл в каталог `.azure` на вашем компьютере (в папке пользователя).

После создания этих файлов необходимо передать `.cer` в Azure с помощью [портала управления][портале управления] и отметить, где сохранен `.pem`-файл.

После получения ИД подписки, создания сертификата и передачи `.cer`-файла в Azure можно подключиться к конечной точке управления Azure, создав строку подключения и передав ее в метод **createServiceManagementService** класса **ServicesBuilder**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $conn_string = "SubscriptionID=<your_subscription_id>;CertificatePath=<path_to_.pem_certificate>";

    $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

В примере выше `$serviceManagementRestProxy` является объектом [ServiceManagementRestProxy][ServiceManagementRestProxy]. Класс **ServiceManagementRestProxy** – это основной класс, используемый для управления службами Azure.

## <span id="ListAvailableLocations"></span></a>Практическое руководство. Отображение списка доступных расположений

Чтобы получить список расположений, доступных для размещения служб, используйте метод **ServiceManagementRestProxy-\>listLocations**:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $result = $serviceManagementRestProxy->listLocations();

        $locations = $result->getLocations();

        foreach($locations as $location){
              echo $location->getName()."<br />";
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

При создании облачной службы, служба хранилища или территориальной группы необходимо предоставить допустимое расположение. Метод **listLocations** всегда будет возвращать текущий список доступных в настоящее время расположений. На момент написания этой статьи были доступны следующие расположения:

-   В любом месте в США
-   В любом месте в Европе
-   Западная Европа
-   В любом месте в Азии
-   Юго-Восточная Азия
-   Восточная Азия
-   Северо-центральный регион США
-   Северная Европа
-   Южно-центральный регион США
-   Запад США
-   Восток США

> [WACOM.NOTE]
> В приведенных ниже примерах кода расположения передаются в методы как строки. Однако расположения также можно передавать как перечисления с помощью класса `WindowsAzure\ServiceManagement\Models\Locations`. Например, вместо передачи в метод, принимающий расположение, значения "Запад США", можно передать `Locations::WEST_US`.

## <span id="CreateCloudService"></span></a>Практическое руководство. Создание облачной службы

При создании приложения и запуска его в Windows Azure совокупность кода и конфигурации называется [облачной службой][облачной службой] Windows Azure (в более ранних выпусках Azure это сочетание называлось *размещенной службой*). Метод **createHostedServices** позволяет создать новую размещенную службу, предоставляя имя размещенной службы (которое должно быть уникальным в Azure), метку (имя размещенной службы в кодировке base64) и объект **CreateServiceOptions**. Объект [CreateServiceOptions][CreateServiceOptions] позволяет задать для службы расположение *или* территориальную группу.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
        // Instead of setLocation, you can use setAffinityGroup
        // to set an affinity group.

        $result = $serviceManagementRestProxy->createHostedService($name, $label, $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Можно получить список всех размещенных служб подписки с помощью метода **listHostedServices**, который возвращает объект [ListHostedServicesResult][ListHostedServicesResult]. Вызов метода **getHostedServices** позволит перебрать массив объектов [HostedServices] и извлечь свойства службы.

    $listHostedServicesResult = $serviceManagementRestProxy->listHostedServices();

    $hosted_services = $listHostedServicesResult->getHostedServices();

    foreach($hosted_services as $hosted_service){
        echo "Service name: ".$hosted_service->getName()."<br />";
        echo "Management URL: ".$hosted_service->getUrl()."<br />";
        echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
        echo "Location: ".$hosted_service->getLocation()."<br />";
        echo "------<br />";
        }

Чтобы получить сведения о конкретной размещенной службе, можно передать имя размещенной службы в метод **getHostedServiceProperties**:

    $getHostedServicePropertiesResult = $serviceManagementRestProxy->getHostedServiceProperties("myhostedservice");
        
    $hosted_service = $getHostedServicePropertiesResult->getHostedService();
        
    echo "Service name: ".$hosted_service->getName()."<br />";
    echo "Management URL: ".$hosted_service->getUrl()."<br />";
    echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
    echo "Location: ".$hosted_service->getLocation()."<br />";

После создания облачной службы можно развернуть свой код в службе с помощью метода [createDeployment][Практическое руководство. Создание развертывания].

## <span id="DeleteCloudService"></span></a>Практическое руководство. Удаление облачной службы

Можно удалить облачную службу, передав облачную службу в метод **deleteHostedService**:

    $serviceManagementRestProxy->deleteHostedService("myhostedservice");

Обратите внимание: прежде чем удалить службу, необходимо сначала удалить все развернутые приложения этой службы. (Подробные сведения см. в разделе [Практическое руководство. Удаление развертывания][Практическое руководство. Удаление развертывания].)

## <span id="CreateDeployment"></span></a>Практическое руководство. Создание развертывания

Метод **createDeployment** служит для передачи нового [пакета служб][пакета служб] и создания нового развертывания в тестовой или производственной среде. Параметры этого метода приведены ниже:

-   **$name**: имя размещаемой службы.
-   **$deploymentName**: имя развертывания.
-   **$slot**: перечисление, которое указывает область в тестовой или рабочей области.
-   **$packageUrl**: URL-адрес пакета развертывания (CSPGK-файл). Файл пакета должны быть сохранен в учетной записи хранилища BLOB-объектов Azure в рамках той же подписки, что и размещенная служба, в которую был передан пакет. Можно создать пакет развертывания с помощью [командлетов Azure PowerShell][командлетов Azure PowerShell] или с помощью [средства командной строки cspack][средства командной строки cspack]
-   **$configuration**: файл конфигурации службы (CSCFG-файл).
-   **$label**: имя размещенной службы в кодировке Base64.

В следующем примере создается новое развертывание в рабочей области размещенной службы с именем `myhostedservice`.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $deploymentName = "v1";
        $slot = DeploymentSlot::PRODUCTION;
        $packageUrl = "URL_for_.cspkg_file";
        $configuration = base64_encode(file_get_contents('path_to_.cscfg_file'));
        $label = base64_encode($name);

        $result = $serviceManagementRestProxy->createDeployment($name,
                                                         $deploymentName,
                                                         $slot,
                                                         $packageUrl,
                                                         $configuration,
                                                         $label);
        
        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Обратите внимание, что в приведенном выше примере состояние операции **createDeployment** может быть получено путем передачи результата, возвращенного **createDeployment** в метод **getOperationStatus**.

Свойства развертывания можно получить с помощью метода **getDeployment**. В следующем примере развертывание извлекается с помощью передачи области развертывания в объекте [GetDeploymentOptions][ListHostedServicesResult], но вместо этого можно указать имя развертывания. В этом примере также перебираются все экземпляры развертывания.

    $options = new GetDeploymentOptions();
    $options->setSlot(DeploymentSlot::PRODUCTION);
        
    $getDeploymentResult = $serviceManagementRestProxy->getDeployment("myhostedservice", $options);
    $deployment = $getDeploymentResult->getDeployment();

    echo "Name: ".$deployment->getName()."<br />";
    echo "Slot: ".$deployment->getSlot()."<br />";
    echo "Private ID: ".$deployment->getPrivateId()."<br />";
    echo "Status: ".$deployment->getStatus()."<br />";
    echo "Instances: <br />";
    foreach($deployment->getroleInstanceList() as $roleInstance){
        echo "Instance name: ".$roleInstance->getInstanceName()."<br />";
        echo "Instance status: ".$roleInstance->getInstanceStatus()."<br />";
        echo "Instance size: ".$roleInstance->getInstanceSize()."<br />";
    }
    echo "------<br />";

## <span id="UpdateDeployment"></span></a>Практическое руководство. Обновление развертывания

Развертывание может быть обновлено с помощью метода **changeDeploymentConfiguration** или метода **updateDeploymentStatus**.

Метод **changeDeploymentConfiguration** позволяет передавать новый файл конфигурации службы (`.cscfg`), что изменяет некоторые параметры службы (включая количество экземпляров в развертывании). Дополнительные сведения см. в статье [Схема конфигурации служб Azure (CSCFG)][Схема конфигурации служб Azure (CSCFG)]. В следующем примере продемонстрировано, как передавать новый файл конфигурации службы:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\ChangeDeploymentConfigurationOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $configuration = base64_encode(file_get_contents('path to .cscfg file'));
        $options = new ChangeDeploymentConfigurationOptions();
        $options->setSlot(DeploymentSlot::PRODUCTION);

        $result = $serviceManagementRestProxy->changeDeploymentConfiguration($name, $configuration, $options);
        
        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Обратите внимание, что в приведенном выше примере состояние операции **changeDeploymentConfiguration** может быть получено путем передачи результата, возвращенного **changeDeploymentConfiguration** в метод **getOperationStatus**.

Метод **updateDeploymentStatus** позволяет указать состояние развертывания ВЫПОЛНЯЕТСЯ или ПРИОСТАНОВЛЕНО. В следующем примере продемонстрировано, как задать состояние ВЫПОЛНЯЕТСЯ для развертывания в рабочей области размещенной службы с именем `myhostedservice`.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\DeploymentStatus;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::PRODUCTION);
        
        $result = $serviceManagementRestProxy->updateDeploymentStatus("myhostedservice", DeploymentStatus::RUNNING, $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="MoveDeployments"></span></a>Практическое руководство. Перемещение развертываний между промежуточным хранением и рабочим расположением

Azure предоставляет две среды для развертывания: среду промежуточного хранения и рабочую среду. Обычно служба развертывается в промежуточной среде для тестирования перед развертыванием в рабочей среде. Когда требуется перенести службы из промежуточной среды в рабочую, это можно сделать без повторного развертывания всей службы. Для этого можно заменить развертывания. (Дополнительные сведения о переключении развертываний см. в разделе [Обзор управления развертываниями в Azure][Обзор управления развертываниями в Azure]).

В следующем примере показано, как с помощью метода **swapDeployment** переключаться между двумя развертываниями (с именами `v1` и `v2`). В этом примере перед вызовом метода **swapDeployment** развертывание `v1` находится в рабочей области, а развертывание `v2` — в промежуточной. После вызова метода **swapDeployment** развертывание `v2` переходит в рабочую область, а `v1` — в промежуточную.

    require_once 'vendor\autoload.php'; 

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $result = $serviceManagementRestProxy->swapDeployment("myhostedservice", "v2", "v1");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="DeleteDeployment"></span></a>Практическое руководство. Удаление развертывания

Чтобы удалить развертывание, используйте метод **deleteDeployment**. Следующий пример показывает, как удалить развертывание в тестовой среде с помощью метода **setSlot** объекта [GetDeploymentOptions][ListHostedServicesResult] с последующей передачей его в **deleteDeployment** Вместо определения развертывания по области можно с помощью метода **setName** класса [GetDepolymentOptions] указать развертывание по имени.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::STAGING);
        
        $result = $serviceManagementRestProxy->deleteDeployment("myhostedservice", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="CreateStorageService"></span></a>Практическое руководство. Создание службы хранения

Служба [хранилища][хранилища] предоставляет доступ к [Blob-объектам][Blob-объектам], [таблицам][таблицам] и [запросам][запросам] Azure. Для создания службы хранилища требуется указать имя службы (от 3 до 24 символов в нижнем регистре, уникальное имя в Azure), описание, метка (имя службы с 64-разрядным шифрованием, до 100 символов) и расположение или территориальную группу. Описание для службы не является обязательным. Расположение, территориальная группа и описание задаются в объекте [CreateServiceOptions][CreateServiceOptions], который передается в метод **createStorageService**. В следующем примере показано, как создать службу хранилища путем указания расположения. Если вы хотите использовать территориальную группу, сначала необходимо создать такую группу (см. раздел [Практическое руководство. Создание территориальной группы][Практическое руководство. Создание территориальной группы]) и установить ее с помощью метода **CreateServiceOptions-\>setAffinityGroup**.

    require_once 'vendor\autoload.php';
     
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
    use WindowsAzure\Common\ServiceException;
     
     
    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "mystorageaccount";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
        $options->setDescription("My storage account description.");

        $result = $serviceManagementRestProxy->createStorageService($name, $label, $options);

        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Обратите внимание, что в приведенном выше примере состояние операции **createStorageService** может быть получено путем передачи результата, возвращенного **createStorageService**, в метод **getOperationStatus**.

Можно вывести список ваших учетных записей хранения и их свойств с помощью метода **listStorageServices**:

    // Create REST proxy.
    $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
    $getStorageServicesResult = $serviceManagementRestProxy->listStorageServices();
    $storageServices = $getStorageServicesResult->getStorageServices();

    foreach($storageServices as $storageService){
        echo "Service name: ".$storageService->getName()."<br />";
        echo "Service URL: ".$storageService->getUrl()."<br />";
        echo "Affinity group: ".$storageService->getAffinityGroup()."<br />";
        echo "Location: ".$storageService->getLocation()."<br />";
        echo "------<br />";
    }

## <span id="DeleteStorageService"></span></a>Практическое руководство. Удаление службы хранения

Можно удалить облачную службу, передав имя облачной службы в метод **deleteStorageService**. Удаление службы хранилища приведет к удалению всех данных, хранящихся в службе (BLOB-объектов, таблиц и очередей).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $serviceManagementRestProxy->deleteStorageService("mystorageservice");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="CreateAffinityGroup"></span></a>Практическое руководство. Создание территориальной группы

Территориальная группа – это логическое группирование служб Azure, которое позволяет Azure разместить службы для оптимизации производительности. Например, можно создать территориальную группу в расположении "Запад США", затем создать [облачную службу][Практическое руководство. Создание облачной службы] в этой территориальной группе. При создании службы хранилища в той же территориальной группе Azure помещает ее в расположение "Запад США" и оптимизирует в центре обработки данных для наилучшей производительности с использованием облачных служб в той же территориальной группе.

Чтобы создать территориальную группу, необходимо указать имя, метку (имя в кодировке Base64) и расположение. При необходимости можно предоставить описание:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateAffinityGroupOptions;
    use WindowsAzure\Common\ServiceException;
     
    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myAffinityGroup";
        $label = base64_encode($name);
        $location = "West US";

        $options = new CreateAffinityGroupOptions();
        $options->setDescription = "My affinity group description.";
        
        $serviceManagementRestProxy->createAffinityGroup($name, $label, $location, $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

После создания территориальной группы при [создании службы хранилища][Практическое руководство. Создание службы хранения] можно указать группу (вместо расположения).

Чтобы вывести список территориальных групп и просмотреть их свойства, следует вызвать метод **listAffinityGroups**, а затем — соответствующие методы класса [AffinityGroup][AffinityGroup]:

    $result = $serviceManagementRestProxy->listAffinityGroups();

    $groups = $result->getAffinityGroups();

    foreach($groups as $group){
        echo "Name: ".$group->getName()."<br />";
        echo "Description: ".$group->getDescription()."<br />";
        echo "Location: ".$group->getLocation()."<br />";
        echo "------<br />";
    }

## <span id="DeleteAffinityGroup"></span></a>Практическое руководство. Удаление территориальной группы

Можно удалить территориальную группу, передав имя группы в метод **deleteAffinityGroup**. Обратите внимание, что перед удалением территориальной группы следует отменить сопоставление этой группы с любыми службами (или необходимо удалить службы, которые используют эту территориальную группу).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        // An affinity group must be disassociated from all services 
        // before it can be deleted.
        $serviceManagementRestProxy->deleteAffinityGroup("myAffinityGroup");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

  [ServiceManagementRestProxy]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php
  [Azure SDK для PHP]: ../php-download-sdk/
  [портале управления]: https://manage.windowsazure.com/
  [Что такое управление службами]: #WhatIs
  [Основные понятия]: #Concepts
  [Создание приложения PHP]: #CreateApplication
  [Получение клиентских библиотек Azure]: #GetClientLibraries
  [Практическое руководство. Подключение к управлению службами]: #Connect
  [Практическое руководство. Отображение списка доступных расположений]: #ListAvailableLocations
  [Практическое руководство. Создание облачной службы]: #CreateCloudService
  [Практическое руководство. Удаление облачной службы]: #DeleteCloudService
  [Практическое руководство. Создание развертывания]: #CreateDeployment
  [Практическое руководство. Обновление развертывания]: #UpdateDeployment
  [Практическое руководство. Перемещение развертываний между промежуточным хранением и рабочим расположением]: #MoveDeployments
  [Практическое руководство. Удаление развертывания]: #DeleteDeployment
  [Практическое руководство. Создание службы хранения]: #CreateStorageService
  [Практическое руководство. Удаление службы хранения]: #DeleteStorageService
  [Практическое руководство. Создание территориальной группы]: #CreateAffinityGroup
  [Практическое руководство. Удаление территориальной группы]: #DeleteAffinityGroup
  [создать учетную запись Azure]: /ru-ru/pricing/free-trial/
  [API-интерфейса управления службами Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee460799.aspx
  [OpenSSL]: http://www.openssl.org/
  [загрузить для Windows]: http://www.openssl.org/related/binaries.html
  [Обзор сертификатов в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg981935.aspx
  []: http://www.openssl.org/docs/apps/openssl.html
  [средств командной строки Azure]: ../command-line-tools/
  [облачной службой]: ../cloud-services-what-is/
  [CreateServiceOptions]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/CreateServiceOptions.php
  [ListHostedServicesResult]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php
  [пакета служб]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433093
  [командлетов Azure PowerShell]: ../install-configure-powershell/
  [средства командной строки cspack]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg432988.aspx
  [Обзор управления развертываниями в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh386336.aspx
  [хранилища]: ../storage-whatis-account/
  [Blob-объектам]: ../storage-php-how-to-use-blobs/
  [таблицам]: ../storage-php-how-to-use-table-storage/
  [запросам]: ../storage-php-how-to-use-queues/
  [AffinityGroup]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/AffinityGroup.php
