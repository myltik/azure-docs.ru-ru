
<properties
   pageTitle="Управление секретами в приложениях Service Fabric | Microsoft Azure"
   description="В этой статье описывается, как защитить значения секретов в приложении Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="vturecek"/>

# Управление секретами в приложениях Service Fabric

В этом руководстве описаны шаги по управлению секретами в приложении Service Fabric. Секретом может считаться любая конфиденциальная информации, например строка подключения к хранилищу, пароль или другое значение, которое не должно обрабатываться в виде обычного текста.

В этом руководстве для управления ключами и секретами используется хранилище ключей Azure. Однако *использование* секретов в приложении осуществляется без привязки к определенной облачной платформе, чтобы приложения могли быть развернуты в кластере, размещенном в любом месте.

## Обзор

Для управления параметрами конфигурации службы рекомендуется использовать [пакеты конфигурации службы][config-package]. Пакеты конфигурации имеют числовые версии, которые можно обновлять, используя управляемые последовательные обновления с проверкой работоспособности и автоматическим откатом обновления. Это предпочтительно для глобальной конфигурации, так как уменьшает вероятность глобального простоя службы. Зашифрованные секреты не являются исключением. Служба Service Fabric снабжена встроенными компонентами для шифрования и расшифровки значений в файле Settings.xml пакета конфигурации. При этом используется сертификат шифрования.

На этой схеме показаны основные этапы процесса управления секретами в приложении Service Fabric:

![обзор управления секретами][overview]

Этот поток состоит из четырех основных шагов.

 1. Получение сертификата шифрования данных.
 2. Установка сертификата в кластере.
 3. Шифрование значений секретов при развертывании приложения с помощью сертификата и их включение в файл конфигурации Settings.xml службы.
 4. Чтение зашифрованных значений из файла Settings.xml. Для расшифровки используется тот же сертификат шифрования.

В качестве безопасного расположения для хранения сертификатов здесь используется [Хранилище ключей Azure][key-vault-get-started]. С его помощью сертификаты также устанавливаются в кластеры Service Fabric в Azure. Если вы не выполняете развертывание в Azure, то использование хранилища ключей для управления секретами в приложениях Service Fabric не требуется.

## Сертификат шифрования данных

Сертификат шифрования данных используются исключительно для шифрования и расшифровки значений конфигурации в файле Settings.xml службы и не применяется при аутентификации. Сертификат должен отвечать приведенным ниже требованиям.

 - Сертификат должен содержать закрытый ключ.
 - Сертификат должен быть создан для обмена ключами, которые можно экспортировать в файл обмена личной информацией (PFX-файл).
 - Использование ключа сертификата должно включать шифрование данных (10) и не должно включать аутентификацию сервера или клиента.
 
 Например, при создании самозаверяющего сертификата с помощью PowerShell для флага `KeyUsage` необходимо задать значение `DataEncipherment`:

 ```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```


## Установка сертификата в кластере

Этот сертификат должен быть установлен на каждом узле в кластере. Он будет использоваться в среде выполнения для расшифровки значений, хранящихся в файле Settings.xml службы. Инструкции по созданию и настройке кластера с помощью Azure Resource Manager см. в [этой статье][service-fabric-cluster-creation-via-arm].

## Шифрование секретов приложений

Пакет SDK для Service Fabric содержит встроенные функции шифрования и расшифровки секретов. Значения секретов могут шифроваться в процессе сборки, а затем программно расшифровываться и считываться в коде службы.

Для шифрования секрета используется следующая команда PowerShell. Чтобы создать зашифрованные данные для значений секретов, необходимо использовать тот же сертификат шифрования, установленный в кластере.

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Результирующая строка в кодировке Base-64 содержит как зашифрованные данные секрета, так и сведения о сертификате, с помощью которого выполнено его шифрование. Эту строку в кодировке Base-64 можно вставить в параметр в файле конфигурации Settings.xml службы. При этом атрибуту `IsEncrypted` необходимо задать значение `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```

### Включение секретов приложений в экземпляры приложений  

В идеале развертывание в разных средах необходимо сделать максимально автоматическим. Этого можно добиться, выполняя шифрование секретов в среде сборки и предоставляя зашифрованные секреты в качестве параметров при создании экземпляров приложений.

#### Использование переопределяемых параметров в Settings.xml

Файл конфигурации Settings.xml позволяет применять переопределяемые параметры, которые можно предоставить при создании приложения. Вместо предоставления параметру значения используйте атрибут `MustOverride`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Чтобы переопределить значения в файле Settings.xml, объявите параметр override для службы в файле ApplicationManifest.xml:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

Теперь при создании экземпляра приложения можно указать значение в качестве *параметра приложения*. Для облегчения интеграции в процессе сборки создание экземпляра приложения можно выполнить с помощью сценария PowerShell или написать на языке C#.

При использовании PowerShell параметр передается в команду `New-ServiceFabricApplication` в виде [хэш-таблицы](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
PS C:\Users\vturecek> New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

При использовании языка C# параметры приложения указываются в `ApplicationDescription` как `NameValueCollection`:

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## Расшифровка секретов из кода службы

В Windows службы Service Fabric по умолчанию выполняются от имени учетной записи СЕТЕВОЙ СЛУЖБЫ (NetworkService) и не имеют доступа к сертификатам, установленным на узле, если не внести в настройки дополнительные изменения.

При использовании сертификата шифрования данных необходимо убедиться, что СЕТЕВАЯ СЛУЖБА или любая другая учетная запись, от имени которой выполняется служба, имеет доступ к закрытому ключу сертификата. Service Fabric будет выполнять обработку, автоматически предоставляя доступ для вашей службы, если ее настроить соответствующим образом. Такую конфигурацию можно настроить в файле ApplicationManifest.xml, определив пользователей и политики безопасности для сертификатов. В следующем примере учетной записи СЕТЕВОЙ СЛУЖБЫ предоставляется доступ на чтение к сертификату, определенному его отпечатком:

```xml
<ApplicationManifest … >
    <Principals>
        <Users>
            <User Name="Service1" AccountType="NetworkService" />
        </Users>
    </Principals>
  <Policies>
    <SecurityAccessPolicies>
      <SecurityAccessPolicy GrantRights=”Read” PrincipalRef="Service1" ResourceRef="MyCert" ResourceType="Certificate"/>
    </SecurityAccessPolicies>
  </Policies>
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

> [AZURE.NOTE] При копировании отпечатка сертификата из оснастки хранилища сертификатов в Windows в начале строки отпечатка помещается невидимый знак. Этот невидимый знак может приводить к ошибке при попытке найти сертификат по отпечатку, поэтому не забудьте его удалить.

### Использование секретов приложений в коде службы

Интерфейс API для доступа к значениям конфигурации из файла Settings.xml в пакете конфигурации позволяет легко расшифровать данные, для которых атрибуту `IsEncrypted` задано значение `true`. Поскольку зашифрованный текст содержит информацию о сертификате, использованном для шифрования, то находить сертификат вручную не требуется. Необходимо только установить сертификат на узле, на котором выполняется данная служба. Для получения исходного значения секрета просто вызовите метод `DecryptValue()`:

```csharp
ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");
SecureString mySecretValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue()
```

## Дальнейшие действия

Дополнительные сведения о запуске приложений с использованием разных разрешений безопасности см. в [этой статье](service-fabric-application-runas-security.md).

<!-- Links -->
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-model.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]: ./media/service-fabric-application-secret-management/overview.png

<!---HONumber=AcomDC_0824_2016-->