<properties 
pageTitle="Настройка подключения к удаленному рабочему столу для роли в облачных службах Azure" 
description="Настройка приложения в облачной службе Azure для удаленного подключения." 
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
ms.date="07/06/2015" 
ms.author="adegeo"/>

# Настройка подключения к удаленному рабочему столу для роли в Azure
Создав облачную службу, которая управляет вашим приложением, вы можете удаленно подключиться к экземпляру роли в этом приложении и настроить нужные параметры или устранить неполадки. В облачной службе должна быть настроена возможность удаленного подключения.

* Если вам требуется сертификат для проверки подлинности удаленного рабочего стола, в [этой статье](cloud-services-certs-create.md) приведены инструкции по его созданию. Ниже приведены инструкции по настройке сертификата.
* Если у вас уже есть удаленный рабочий стол, настроенный для работы с облачной службой, [подключите](#remote-into-role-instances) его к облачной службе.

## Настройка подключения к удаленному рабочему столу для веб-ролей и рабочих ролей
Чтобы разрешить подключение к удаленному рабочему столу для рабочей роли или веб-роли, настройте подключение для приложения в модели службы. Подключение также можно настроить на портале управления Azure после запуска экземпляров.

### Настройка подключения в модели службы
В файл определения службы, который импортирует в модель службы модули **RemoteAccess** и **RemoteForwarder**, необходимо добавить элемент **Imports**. Если проект Azure создается с помощью Visual Studio, файлы для модели службы создаются автоматически.

Модель службы состоит из файлов [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) и [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg). Файл определения упаковывается вместе с двоичными файлами роли на этапе подготовки приложения для облачной службы к развертыванию. Файл ServiceConfiguration.cscfg развертывается вместе с пакетом приложения. Azure использует этот файл, чтобы определить, как должно работать приложение.

Создав проект, с помощью Visual Studio вы можете [разрешить подключение к удаленному рабочему столу](https://msdn.microsoft.com/library/gg443832.aspx).

Когда подключение настроено, файл определения службы должен быть таким, как показано в примере ниже (обратите внимание на добавленный элемент `<Imports>`).

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```

Файл конфигурации службы должен содержать значения, которые вы указали во время настройки подключения, и быть таким, как показано в примере ниже (обратите внимание на элементы `<ConfigurationSettings>` и `<Certificates>`).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```

[Создавая пакет](cloud-services-model-and-package.md#cspkg) и [публикуя](cloud-services-how-to-create-deploy-portal.md) приложение, следите за тем, чтобы был установлен флажок **Включить удаленный рабочий стол для всех ролей**. В [этой статье](https://msdn.microsoft.com/library/ff683672.aspx) приведен список наиболее распространенных задач, связанных с использованием Visual Studio и облачных служб.

### Настройка подключения в запущенных экземплярах
На странице настройки облачной службы вы можете активировать или изменить параметры подключения к удаленному рабочему столу. Дополнительные сведения см. в статье [Настройка удаленного доступа к экземплярам ролей](cloud-services-how-to-configure.md).




## Удаленное подключение к экземплярам ролей
Для доступа к экземплярам веб-ролей или рабочих ролей необходимо использовать RDP-файл. Файл можно загрузить с портала управления или получить программным путем.

### Загрузка RDP-файла с портала управления

Ниже описана процедура загрузки RDP-файла с портала управления. С помощью этого файла вы сможете подключаться к экземпляру роли по протоколу удаленного рабочего стола.

1.  На странице экземпляров выберите нужный экземпляр и на панели команд нажмите кнопку **Подключиться**.
2.  Щелкните **Сохранить**, чтобы сохранить RDP-файл на локальный компьютер.
3.  Откройте **подключение к удаленному рабочему столу**, щелкните **Показать параметры** и нажмите кнопку **Открыть**.
4.  Перейдите в расположение, в которое вы сохранили RDP-файл, выберите загруженный файл, нажмите кнопку **Открыть** и щелкните **Подключиться**. Следуйте инструкциям для подключения к экземпляру.

### Получение RDP-файла с помощью PowerShell
RDP-файл можно получить с помощью командлета [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx).

### Загрузка RDP-файла с помощью Visual Studio
В Visual Studio подключение к удаленному рабочему столу можно создать с помощью обозревателя сервера.

1.  В обозревателе сервера разверните узел **Azure\\Cloud Services\\[имя облачной службы]**.
2.  Разверните узел **Промежуточное хранение** или **Производство**.
3.  Разверните нужную роль.
4.  Щелкните правой кнопкой мыши один из экземпляров роли, выберите пункт **Подключиться с помощью удаленного рабочего стола**, а затем введите имя пользователя и пароль.

### Загрузка RDP-файла через API REST управления службами программным способом
Вы можете загрузить RDP-файл с помощью операции REST [Загрузить RDP-файл](https://msdn.microsoft.com/library/jj157183.aspx). Затем с помощью этого файла вы сможете подключаться к облачной службе по протоколу удаленного рабочего стола.

## Дальнейшие действия
Возможно, вам потребуется [упаковать](cloud-services-model-and-package.md) или [передать (развернуть)](cloud-services-how-to-create-deploy-portal.md) свое приложение.

<!---HONumber=August15_HO6-->