<properties
   pageTitle="Общее сведения о политиках безопасности запуска от имени приложения Service Fabric | Microsoft Azure"
   description="Общие сведения о запуске приложения Service Fabric с использованием системных и локальных учетных записей безопасности, включая точку SetupEntry, если приложению перед запуском необходимо выполнить привилегированную операцию."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/24/2015"
   ms.author="mfussell"/>

# Запуск от имени: запуск приложения Service Fabric с использованием разных разрешений безопасности
Платформа Azure Service Fabric предоставляет возможность защиты приложений, работающих в кластере под разными учетными записями, с помощью функции **запуска от имени**. Кроме того, платформа защищает ресурсы, используемые приложениями с учетной записью пользователя, — файлы, каталоги и сертификаты.

По умолчанию приложения Service Fabric выполняются под учетной записью, используемой процессом Fabric.exe. Платформа также позволяет запускать приложения под учетной записью локального пользователя, указанной в манифесте приложения. Функция запуска от имени поддерживает следующие типы учетных записей: **LocalUser**, **NetworkService**, **LocalService** и **LocalSystem**.

> [AZURE.NOTE]Учетные записи доменов поддерживаются в развертываниях Windows Server, где доступна служба Azure Active Directory.

Вы можете определять и создавать группы пользователей, в которые можно добавлять одного или нескольких пользователей для совместного управления. Это особенно полезно, если для разных точек входа службы есть несколько пользователей. У них должны быть определенные общие права, доступные на уровне группы.

## Настройка политики запуска от имени для SetupEntryPoint

Как описано в [модели приложения](service-fabric-application-model.md), **SetupEntryPoint** — это привилегированная точка входа, которая выполняется с теми же учетными данными, что и Service Fabric (обычно это *сетевая* учетная запись), до запуска других точек входа. Исполняемый файл, указанный в точке входа **EntryPoint**, обычно является узлом службы, запускаемым на продолжительный срок. Поэтому наличие отдельной точки входа позволяет избежать запуска исполняемого файла узла службы с высокими привилегиями на длительное время. Исполняемый файл, указанный в точке входа **EntryPoint**, запускается после успешного выхода из точки **SetupEntryPoint**. Возникающий вследствие этого процесс отслеживается и перезапускается (снова начиная с точки входа **SetupEntryPoint**), даже если произошло непредвиденное завершение его работы или сбой.

Ниже приведен простой пример манифеста службы с отображением точки входа SetupEntryPoint и главной точки входа EntryPoint для службы.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### Настройка политики запуска от имени

Настроив службу для использования точки SetupEntryPoint, можно изменить разрешения безопасности, с которыми она выполняется в манифесте приложения. В приведенном ниже примере показано, как настроить службу для запуска с правами учетной записи администратора.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

Сначала создайте раздел **Principals** с именем пользователя, например SetupAdminUser. Это означает, что пользователь является членом системной группы "Администраторы".

Затем в разделе **ServiceManifestImport** настройте политику для применения этого субъекта к точке **SetupEntryPoint**. Благодаря этому при выполнении файла **MySetup.bat** платформа Service Fabric будет использовать функцию запуска от имени с привилегиями администратора. Учитывая, что вы *не* применили политику к главной точке входа, код в файле **MyServiceHost.exe** будет запущен с использованием системной учетной записи **NetworkService**. Это учетная запись по умолчанию, с использованием которой запускаются все точки входа службы.

Теперь добавим файл MySetup.bat в проект Visual Studio, чтобы проверить привилегии администратора. В Visual Studio щелкните правой кнопкой мыши проект службы и добавьте новый файл MySetup.bat.

Затем убедитесь, что файл MySetup.bat включен в пакет службы. По умолчанию он не включен. Щелкните файл правой кнопкой мыши, чтобы открыть контекстное меню, и выберите **Свойства**. Убедитесь, что в диалоговом окне «Свойства» параметру **Копировать в выходной каталог** задано значение **Копировать, если новее**. Это показано на приведенном ниже снимке экрана.

![Свойство CopyToOutput в Visual Studio для пакетного файла SetupEntryPoint][image1]

Теперь откройте файл MySetup.bat и добавьте следующие команды.

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > test.txt
echo %TestVariable% >> test.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

Затем скомпилируйте и разверните решение в кластере локальной разработки. После запуска службы (отображено в обозревателе Service Fabric) вы можете убедиться в успешном выполнении файла MySetup.bat двумя способами. Откройте командную строку PowerShell и введите следующую команду.

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

Запишите имя узла с развернутой и запущенной службой в обозревателе Service Fabric (например, Node 1). Затем перейдите в рабочую папку экземпляра приложения и найдите файл out.txt, который показывает значение **TestVariable**. Например, если служба была развернута на узле Node 2, можно перейти по этому пути для **MyApplicationType**.

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

##  Запуск команд PowerShell из точки входа SetupEntryPoint
Чтобы запустить PowerShell из точки **SetupEntryPoint**, запустите **PowerShell.exe** в пакетном файле, указывающем на файл PowerShell. Сначала добавьте файл PowerShell в проект службы, например **MySetup.ps1** Не забудьте задать свойство *Копировать, если новее*, чтобы включить этот файл в состав пакета службы. В приведенном ниже примере показан образец пакетного файла для запуска файла PowerShell с именем MySetup.ps1, который задает системную переменную среды с именем **TestVariable**.

Файл MySetup.bat для запуска файла PowerShell.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

В файле PowerShell добавьте следующую команду, чтобы задать системную переменную среды.

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

## Применение политики запуска от имени к службам
Выше мы описали, как применять политику запуска от имени к точке SetupEntryPoint. Теперь давайте подробнее рассмотрим процесс создания разных субъектов, которые могут применяться в качестве политик служб.

### Создание локальных групп пользователей
Вы можете определять и создавать группы пользователей, чтобы добавлять в них одного или нескольких пользователей. Это особенно полезно, если для разных точек входа службы существует несколько пользователей и они должны иметь определенные общие права, доступные на уровне группы. В следующем примере показана локальная группа с именем **LocalAdminGroup** и привилегиями администратора. В нее входят два пользователя: Customer1 и Customer2.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### Создание локальных пользователей
Можно создать локального пользователя, используемого для защиты службы в приложении. Если в разделе Principals манифеста приложения указан тип учетной записи **LocalUser**, платформа Service Fabric создает учетные записи локальных пользователей на компьютерах с развернутым приложением. По умолчанию эти учетные записи не соответствуют именам, указанным в манифесте приложения (например, Customer3 в примере ниже). Вместо этого они создаются динамически и имеют случайные пароли.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

## Назначение политик пакетам кода службы
С помощью раздела **RunAsPolicy** для **ServiceManifestImport** определяется учетная запись из раздела Principals, которая будет использоваться для выполнения пакета кода. Также это позволяет связать пакеты кода из манифеста службы с учетными записями пользователей в разделе Principals. Это можно сделать для точки входа Setup или Main. Или можно указать All, чтобы применить настройки к двум точкам входа. В приведенном ниже примере показаны разные примененные политики.

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Если параметр **EntryPointType** не указан, по умолчанию используется EntryPointType="Main". Выбор **SetupEntryPoint** особенно уместен при выполнении операции установки с определенными высокими привилегиями под системной учетной записью. Фактический код может выполняться под учетной записью с более низкими привилегиями.

### Применение политики по умолчанию ко всем пакетам кода службы
С помощью раздела **DefaultRunAsPolicy** можно указать учетную запись пользователя по умолчанию для всех пакетов кода без конкретного определенного раздела **RunAsPolicy**. Иногда большую часть пакетов кода, указанных в используемых приложением манифестах служб, нужно запускать с использованием одной и той же учетной записи пользователя с функцией запуска от имени. Для этого в приложении можно определить стандартную политику запуска от имени с использованием этой учетной записью пользователя, а не указывать **RunAsPolicy** для каждого пакета кода. Следующий пример демонстрирует: если в пакете кода не указано **RunAsPolicy**, такой пакет кода должен запускаться под учетной записью **MyDefaultAccount**, указанной в разделе Principals.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## Назначение SecurityAccessPolicy для конечных точек HTTP и HTTPS
Если политика запуска от имени применяется к службе, а манифест служб объявляет ресурсы конечной точки с помощью протокола HTTP, вам нужно указать **SecurityAccessPolicy**. Это гарантирует, что порты, выделенные этим конечным точкам, будут правильно внесены в списки управления доступом для учетной записи запуска от имени, используемой для запуска службы. В противном случае файл **http.sys** не получит доступ к службе, а вызовы с клиента будут завершаться ошибками. В следующем примере учетная запись Customer3 применяется к конечной точке с именем **ServiceEndpointName**, предоставляя ей права полного доступа.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Для конечной точки HTTPS необходимо также указать имя сертификата, который будет возвращен клиенту. Это можно сделать с помощью **EndpointBindingPolicy** и сертификата, определенного в разделе Certificates в манифесте приложения.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## Полный пример манифеста приложения
В следующем манифесте приложения показаны разные параметры, описанные выше.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
	 <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

* [Сведения о модели приложения](service-fabric-application-model.md)
* [Указание ресурсов в манифесте службы](service-fabric-service-manifest-resources.md)
* [Развертывание приложения](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

<!---HONumber=AcomDC_0107_2016-->