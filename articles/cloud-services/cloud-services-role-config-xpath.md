<properties 
pageTitle="Памятка по конфигурации XPath для роли облачных служб | Microsoft Azure" 
description="Различные параметры XPath можно использовать в конфигурации роли облачной службы, чтобы предоставить их в качестве переменных среды." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="08/10/2016" 
ms.author="adegeo"/>

# Предоставление параметров конфигурации ролей как переменной среды с помощью XPath

В файле определения службы рабочей роли или веб-роли облачной службы можно предоставить значения конфигурации среды выполнения как переменные среды. Поддерживаются следующие значения XPath (которые соответствуют значениям API).

Эти значения XPath также можно использовать с помощью библиотеки [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx).

## Приложение, запущенное в эмуляторе

Указывает, что приложение выполняется в эмуляторе.

| Тип | Пример |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@emulated" |
| Код | var x = RoleEnvironment.IsEmulated; |


## Идентификатор развертывания

Получает идентификатор развертывания для экземпляра.

| Тип | Пример |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@id" |
| Код | var deploymentId = RoleEnvironment.DeploymentId; |


## Идентификатор роли 

Получает идентификатор текущей роли для экземпляра.

| Тип | Пример |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@id" |
| Код | var id = RoleEnvironment.CurrentRoleInstance.Id; |


## Обновление домена

Получает домен обновления для экземпляра.

| Тип | Пример |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Код | var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |


## Домен сбоя

Получает домен сбоя для экземпляра.

| Тип | Пример |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Код | var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |


## Имя роли

Получает имя роли для экземпляров.

| Тип | Пример |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Код | var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |


## Параметр конфигурации

Получает значение указанного параметра конфигурации.

| Тип | Пример |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Код | var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |
 
## Путь к локальному хранилищу

Получает путь к локальному хранилищу для экземпляра.

| Тип | Пример |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Код | var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |


## Размер локального хранилища

Получает размер локального хранилища для экземпляра.

| Тип | Пример |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Код | var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## Протокол конечной точки 

Получает протокол конечной точки для экземпляра.

| Тип | Пример |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Код | var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## IP-адрес конечной точки

Получает IP-адрес указанной конечной точки.

| Тип | Пример |
| ----- | ---- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Код | var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## Порт конечной точки 

Получает порт конечной точки для экземпляра.

| Тип | Пример |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Код | var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |





## Пример

Ниже приведен пример рабочей роли, создающей задачу запуска с переменной среды `TestIsEmulated`, которой присваивается [значение XPath @emulated](#app-running-in-emulator).

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## Дальнейшие действия

Узнайте больше о файле [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg).

Создайте пакет [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg).

Включите [удаленный рабочий стол](cloud-services-role-enable-remote-desktop.md) для роли.

<!---HONumber=AcomDC_0810_2016-->