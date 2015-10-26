<properties 
	pageTitle="Облачные службы Azure — определение службы и конфигурация службы — XML-сертификат" 
	description="Сведения о настройке сертификата в файлах определения и конфигурации службы." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015"
	ms.author="adegeo"/>



# Настройка определения и конфигурации службы для сертификата

С виртуальными машинами, на которых выполняются веб-роли или рабочие роли, могут быть связаны сертификаты. После передачи сертификата на портал необходимо настроить файлы определения службы (.csdef) и конфигурации службы (.cscfg) для этого сертификата.

После установки сертификата виртуальная машина может получить доступ к его закрытому ключу. Поэтому необходимо ограничивать доступ процессам с повышенным уровнем разрешений.

## Пример определения службы

Ниже приведен пример сертификата, указанного в определении службы.

```xml
<ServiceDefinition name="WindowsAzureProject4" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="MyWokerRole"> <!-- or <WebRole name="MyWebRole" vmsize="Small"> -->
    <ConfigurationSettings>
      ...
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="MySSLCert" storeLocation="LocalMachine" storeName="My" permissionLevel="elevated" />
    </Certificates>
  </WorkerRole>
</ServiceDefinition>
```

### Разрешения
Разрешениям (атрибуту `permisionLevel`) можно присвоить одно из следующих значений:

| Значение разрешения | Описание |
| ----------------  | ----------- |
| limitedOrElevated | **(По умолчанию).** Все процессы роли могут получить доступ к закрытому ключу. |
| elevated | Доступ к закрытому ключу могут получить только процессы повышенного уровня.|

## Пример конфигурации службы

Ниже приведен пример сертификата, определенного в конфигурации службы.

```xml
<Role name="MyWokerRole">
...
    <Certificates>
        <Certificate name="MySSLCert" 
            thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
            thumbprintAlgorithm="sha1" />
    </Certificates>
...
</Role>
```

**Обратите внимание** на совпадающие атрибуты `name`.

## Дальнейшие действия
Ознакомьтесь со схемой [XML определения службы](https://msdn.microsoft.com/library/azure/ee758711.aspx) и схемой [XML конфигурации службы](https://msdn.microsoft.com/library/azure/ee758710.aspx).

<!---HONumber=Oct15_HO3-->