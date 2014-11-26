<properties linkid="manage-linux-howto-service-management-api" urlDisplayName="Service Management API" pageTitle="How to use the service management API for VMs - Azure" metaKeywords="" description="Learn how to use the Azure Service Management API for a Linux virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Use the Service Management API" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Как использовать API управления службой

## Инициализация

Для вызова API управления службой Azure IaaS из NodeJS используется модуль `azure`.

    var azure = require('azure');

Сначала создайте экземпляр службы ServiceManagementService. Этот объект будут использовать все вызовы, адресованные API-интерфейсу. Теперь устанавливаются идентификатор подписки, учетные данные и другие параметры подключения. Для управления несколькими подписками создайте несколько служб ServiceManagementService.

    var iaasClient = azure.createServiceManagementService(subscriptionid, auth, options);

-   Subscriptionid — обязательная строка. Это должен быть идентификатор подписки учетной записи, осуществляющей доступ.
-   Auth — необязательный объект, который задает закрытый ключ и общедоступный сертификат для использования с этой учетной записью.

    -   keyfile — путь к PEM-файлу, содержащему закрытый ключ. Игнорируется, если указано значение ключа keyvalue.
    -   keyvalue — фактическое значение закрытого ключа, хранящееся в PEM-файле.
    -   certfile — путь к PEM-файлу, содержащему открытый сертификат. Игнорируется, если указано значение сертификата cervalue.
    -   certvalue — фактическое значение общедоступного сертификата, хранящееся в PEM-файле.
    -   Если представленные выше значения не указаны, то считываются и используются значения переменных среды процесса `CLIENT_AUTH_KEYFILE` и `CLIENT_AUTH_CERTFILE`. Если они не заданы, то пробуются значения по умолчанию для файлов: priv.pem и pub.pem.
    -   Если не удается загрузить закрытый ключ и общедоступный сертификат, выводится сообщение об ошибке.
-   Options — необязательный объект, который может использоваться для управления свойствами, используемыми клиентом.

    -   host — имя узла сервера управления Azure. Если не указано, используется узел по умолчанию.
    -   apiversion — строка версии для использования в заголовке HTTP. Если не указана, используется версия по умолчанию.
    -   serializetype — может принимать значение XML или JSON. Если не указано, используется сериализация по умолчанию.

При необходимости можно использовать прокси-сервер туннелирования, чтобы HTTPS-запрос проходил через прокси-сервер. При создании объект IaasClient обрабатывает переменную среды `HTTPS_PROXY`. Если для него задан допустимый URL-адрес, то имя узла и порт из URL-адреса анализируются и используются в последующих запросах для определения прокси-сервера.

        iaasClient.SetProxyUrl(proxyurl)

SetProxyUrl вызывается для явного задания узла и порта прокси-сервера. Эта функция действует так же, как задание переменной среды `HTTPS_PROXY`, и переопределяет параметр среды.

## Обратные вызовы

Во всех интерфейсах API имеется обязательный аргумент ответного вызова. Вызов функции, переданной в обратном вызове, сигнализирует о завершении запроса.

    callback(rsp)

-   Функция callback имеет один параметр, представляющий объект ответа.
-   isSuccessful — значение true или false.
-   statusCode — код состояния HTTP из ответа.
-   response — ответ, который анализируется в объекте JavaScript. Задается, если isSuccessful имеет значение true.
-   error — объект JavaScript, содержащий сведения об ошибке, задается, если isSuccessful имеет значение false.
-   body — фактический текст ответа в виде строки.
-   headers — фактические заголовки HTTP ответа.
-   reqopts — параметры HTTP-запроса узла, используемые для создания запроса.

Заметьте, что в некоторых случаях завершение может указать только на то, что запрос был принят. В этом случае используйте **GetOperationStatus** для получения окончательного состояния.

## Интерфейсы API

**iaasClient.GetOperationStatus(requested, callback)**

-   Многие вызовы управления возвращаются до завершения операции. Они возвращают значение 202 "Принято" и помещают requested в HTTP-заголовок ms-request-id. Для опроса о завершении запроса воспользуйтесь этим API-интерфейсом и передайте значение requested.
-   callback — обязательный параметр.

**iaasClient.GetOSImage(imagename, callback)**

-   imagename — обязательное строковое имя образа.
-   callback — обязательный параметр.
-   В случае успеха объект ответа будет содержать свойства именованного образа.

**iaasClient.ListOSImages(callback)**

-   callback — обязательный параметр.
-   В случае успеха объект ответа будет содержать массив объектов образов.

**iaasClient.CreateOSImage(imageName, mediaLink, imageOptions, callback)**

-   imageName — обязательное строковое имя образа.
-   mediaLink — обязательное строковое имя ссылки на носитель.
-   imageOptions — необязательный объект. Он может содержать следующие свойства:

    -   Категория
    -   Label — по умолчанию принимает значение imageName, если оно не задано.
    -   Расположение
    -   RoleSize
-   callback — обязательный параметр. (Это может быть третий параметр, если объект imageOptions не задан.)

-   В случае успеха объект ответа будет содержать свойства созданного образа.

**iaasClient.ListHostedServices(callback)**

-   callback — обязательный параметр.
-   В случае успеха объект ответа будет содержать массив объектов размещенной службы.

**iaasClient.GetHostedService(serviceName, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   callback — обязательный параметр.
-   В случае успеха объект ответа будет содержать свойства размещенной службы.

**iaasClient.CreateHostedService(serviceName, serviceOptions, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   serviceOptions — необязательный объект. Он может содержать следующие свойства:
    -   Description — по умолчанию принимает значение «Service host».
    -   Label — по умолчанию принимает значение serviceName, если оно не задано.
    -   Location — область, в которой необходимо создать службу.
-   callback — обязательный параметр.

**iaasClient.GetStorageAccountKeys(serviceName, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   callback — обязательный параметр.
-   В случае успеха объект ответа будет содержать ключи доступа к службе хранилища.

**iaasClient.GetDeployment(serviceName, deploymentName, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   deploymentName — обязательное строковое имя развертывания.
-   callback — обязательный параметр.
-   В случае успеха объект ответа будет содержать свойства именованного развертывания.

**iaasClient.GetDeploymentBySlot(serviceName, deploymentSlot, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   deploymentSlot — обязательное строковое имя области развертывания (промежуточная или рабочая).
-   callback — обязательный параметр.
-   В случае успеха объект ответа будет содержать свойства развертываний в указанной области.

**iaasClient.CreateDeployment(serviceName, deploymentName, VMRole, deployOptions, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   deploymentName — обязательное строковое имя развертывания.
-   VMRole — обязательный объект, имеющий свойства роли, которую необходимо создать для развертывания.
-   deployOptions — необязательный объект. Он может содержать следующие свойства:
    -   DeploymentSlot — по умолчанию принимает значение «Production»
    -   Label — по умолчанию принимает значение deploymentName, если оно не задано.
    -   UpgradeDomainCount — нет значения по умолчанию.
-   callback — обязательный параметр.

**iaasClient.GetRole(serviceName, deploymentName, roleName, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   deploymentName — обязательное строковое имя развертывания.
-   roleName — обязательное строковое имя роли.
-   callback — обязательный параметр.
-   В случае успеха объект ответа будет содержать свойства именованной роли.

**iaasClient.AddRole(serviceName, deploymentName, VMRole, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   deploymentName — обязательное строковое имя развертывания.
-   VMRole — обязательный объект, имеющий свойства роли, которую необходимо добавить в развертывание.
-   callback — обязательный параметр.

**iaasClient.ModifyRole(serviceName, deploymentName, roleName, VMRole, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   deploymentName — обязательное строковое имя развертывания.
-   roleName — обязательное строковое имя роли.
-   VMRole — обязательный объект, имеющий свойства, которые необходимо изменить в роли.
-   callback — обязательный параметр.

**iaasClient.DeleteRole(serviceName, deploymentName, roleName, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   deploymentName — обязательное строковое имя развертывания.
-   roleName — обязательное строковое имя роли.
-   callback — обязательный параметр.

**iaasClient.AddDataDisk(serviceName, deploymentName, roleName, datadisk, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   deploymentName — обязательное строковое имя развертывания.
-   roleName — обязательное строковое имя роли.
-   Datadisk — обязательный объект, который используется для указания способа создания диска данных.
-   callback — обязательный параметр.

**iaasClient.ModifyDataDisk(serviceName, deploymentName, roleName, LUN, datadisk, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   deploymentName — обязательное строковое имя развертывания.
-   roleName — обязательное строковое имя роли.
-   LUN — число, определяющее диск данных.
-   Datadisk — обязательный объект, который используется для указания способа изменения диска данных.
-   callback — обязательный параметр.

**iaasClient.RemoveDataDisk(serviceName, deploymentName, roleName, LUN, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   deploymentName — обязательное строковое имя развертывания.
-   roleName — обязательное строковое имя роли.
-   LUN — число, определяющее диск данных.
-   callback — обязательный параметр.

**iaasClient.ShutdownRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   deploymentName — обязательное строковое имя развертывания.
-   roleInstance — обязательное строковое имя экземпляра роли.
-   callback — обязательный параметр.

**iaasClient.RestartRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   deploymentName — обязательное строковое имя развертывания.
-   roleInstance — обязательное строковое имя экземпляра роли.
-   callback — обязательный параметр.

**iaasClient.CaptureRoleInstance(serviceName, deploymentName, roleInstance, captOptions, callback)**

-   serviceName — обязательное строковое имя размещенной службы.
-   deploymentName — обязательное строковое имя развертывания.
-   roleInstance — обязательное строковое имя экземпляра роли.
-   captOptions — обязательный объект, определяющий действия перенаправления.
    -   PostCaptureActions
    -   ProvisioningConfiguration
    -   SupportsStatelessDeployment
    -   TargetImageLabel
    -   TargetImageName
-   callback — обязательный параметр.

## Объекты данных

При создании или изменении развертывания, роли или диска интерфейсы API принимают объекты как входные данные. Другие интерфейсы API возвращают подобные объекты при операции Get или List.
В этом разделе дается краткий обзор свойств объекта.
Развертывание

-   Name — строка.
-   DeploymentSlot — «Staging» (промежуточная среда) или «Production» (рабочая среда).
-   Status — строка (только вывод).
-   PrivateID — строка (только вывод).
-   Label — строка.
-   UpgradeDomainCount — число.
-   SdkVersion — строка.
-   Locked — значение true или false.
-   RollbackAllowed — значение true или false.
-   RoleInstance — объект (только вывод).
-   Role — объект VMRole.
-   InputEndpointList — массив объектов InputEndpoint.

**VMRole**

-   RoleName — строка. Требуется для создания.
-   RoleSize — строка. Необязателен для создания.
-   RoleType — строка. По умолчанию принимает значение "PersistentVMRole", если не задана при создании.
-   OSDisk — объект. Требуется для создания.
-   DataDisks — массив объектов. Необязателен для создания.
-   ConfigurationSets — массив объектов Configuration.

**RoleInstance**

-   RoleName — строка.
-   InstanceName — строка.
-   InstanceStatus — строка.
-   InstanceUpgradeDomain — число.
-   InstanceFaultDomain — число.
-   InstanceSize — строка.
-   IpAddress — строка.

**OSDisk**

-   SourceImageName — строка. Требуется для создания.
-   DisableWriteCache — значение true или false.
-   DiskName — строка.
-   MediaLink — строка.

**DataDisk**

-   DisableReadCache — значение true или false.
-   EnableWriteCache — значение true или false.
-   DiskName — строка.
-   MediaLink — строка.
-   LUN — число (от 0 до 15).
-   LogicalDiskSizeInGB — число.
-   SourceMediaLink — строка.
-   Задать диск во время создания можно тремя способами — по имени, по носителю или по размеру. Указанные параметры будут определять его работу. Подробные сведения см. в документе по RDFE API.

**ProvisioningConfiguration**

-   ConfigurationSetType — «ProvisioningConfiguration».
-   AdminPassword — строка.
-   MachineName — строка.
-   ResetPasswordOnFirstLogon — значение true или false.

**NetworkConfiguration**

-   ConfigurationSetType — «NetworkConfiguration».
-   InputEndpoints — массив объектов ExternalEndpoints.

**InputEndpoint**

-   RoleName
-   Виртуальный IP-адрес
-   Порт

**ExternalEndpoint**

-   LocalPort
-   Имя
-   Порт
-   Протокол

## Пример кода

Ниже приведен пример кода JavaScript, который создает размещенную службу и развертывание, а затем проводит опрос о состоянии завершения развертывания.

    var azure = require('azure');

    // specify where certificate files are located
    var auth = {
      keyfile : '../certs/priv.pem',
      certfile : '../certs/pub.pem'
    }

    // names and IDs for subscription, service, deployment
    var subscriptionId = '167a0c69-cb6f-4522-ba3e-d3bdc9c504e1';
    var serviceName = 'sampleService2';
    var deploymentName = 'sampleDeployment';

    // poll for completion every 10 seconds
    var pollPeriod = 10000;

    // data used to define deployment and role

    var deploymentOptions = {
      DeploymentSlot: 'Staging',
      Label: 'Deployment Label'
    }

    var osDisk = {
      SourceImageName : 'Win2K8SP1.110809-2000.201108-01.en.us.30GB.vhd',
    };

    var dataDisk1 = {
      LogicalDiskSizeInGB : 10,
      LUN : '0'
    };

    var provisioningConfigurationSet = {
      ConfigurationSetType: 'ProvisioningConfiguration',
      AdminPassword: 'myAdminPwd1',
      MachineName: 'sampleMach1',
      ResetPasswordOnFirstLogon: false
    };

    var externalEndpoint1 = {
      Name: 'endpname1',
      Protocol: 'tcp',
      Port: '59919',
      LocalPort: '3395'
    };

    var networkConfigurationSet = {
      ConfigurationSetType: 'NetworkConfiguration',
      InputEndpoints: [externalEndpoint1]
    };

    var VMRole = {
      RoleName: 'sampleRole',
      RoleSize: 'Small',
      OSDisk: osDisk,
      DataDisks: [dataDisk1],
      ConfigurationSets: [provisioningConfigurationSet, networkConfigurationSet]
    }


    // function to show error messages if failed
    function showErrorResponse(rsp) {
      console.log('There was an error response from the service');
      console.log('status code=' + rsp.statusCode);
      console.log('Error Code=' + rsp.error.Code);
      console.log('Error Message=' + rsp.error.Message);
    }

    // polling for completion
    function PollComplete(reqid) {
      iaasCli.GetOperationStatus(reqid, function(rspobj) {
        if (rspobj.isSuccessful && rspobj.response) {
          var rsp = rspobj.response;
          if (rsp.Status == 'InProgress') {
            setTimeout(PollComplete(reqid), pollPeriod);
            process.stdout.write('.');
          } else {
            console.log(rsp.Status);
            if (rsp.HttpStatusCode) console.log('HTTP Status: ' + rsp.HttpStatusCode);
            if (rsp.Error) {      
              console.log('Error code: ' + rsp.Error.Code);
              console.log('Error Message: ' + rsp.Error.Message);
            }
          }
        } else {
          showErrorResponse(rspobj);
        }
      });
    }


    // create the client object
    var iaasCli = azure.createServiceManagementService(subscriptionId, auth);

    // create a hosted service.
    // if successful, create deployment
    // if accepted, poll for completion
    iaasCli.CreateHostedService(serviceName, function(rspobj) {
      if (rspobj.isSuccessful) {
        iaasCli.CreateDeployment(serviceName, 
                                 deploymentName,
                                 VMRole, deploymentOptions,
                                  function(rspobj) {
          if (rspobj.isSuccessful) {
          // get request id, and start polling for completion
            var reqid = rspobj.headers['x-ms-request-id'];
            process.stdout.write('Polling');
            setTimeout(PollComplete(reqid), pollPeriod);
          } else {
            showErrorResponse(rspobj);
          }
        });
      } else {
        showErrorResponse(rspobj);
      }
    });
