---
title: "Создание среды службы приложений Azure, используя шаблон Resource Manager"
description: "Описание способов создания внешнего или внутреннего балансировщика нагрузки среды службы приложений Azure с помощью шаблона Resource Manager"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 370ab7a32634f04b4776a2a9a84ecaacf11ea617
ms.contentlocale: ru-ru
ms.lasthandoff: 06/26/2017

---
# <a name="how-to-create-an-ase-using-azure-resource-manager-templates"></a>Создание среды службы приложений с внутренней подсистемой балансировки нагрузки с помощью шаблонов Azure Resource Manager

## <a name="overview"></a>Обзор
Среды службы приложений (ASE) создаются с помощью конечной точки с доступом к Интернету или конечной точки с внутренним адресом в виртуальной сети Azure.  Если при создании внутренней конечной точки эта конечная точка предоставляется компонентом Azure, она называется внутренним балансировщиком нагрузки.  ASE с общедоступной конечной точкой вызывается внешним ASE, а ASE с внутренним IP-адресом называется ASE с внутренним балансировщиком нагрузки.  

ASE можно создать на портале Azure или с помощью шаблонов Azure Resource Manager.  В этой статье описаны действия и синтаксис, необходимые для создания внешнего ASE или ASE с внутренним балансировщиком нагрузки с помощью шаблонов Azure Resource Manager.  Дополнительные сведения о создании ASE на портале см. в статьях [Создание внешней среды службы приложений][MakeExternalASE] или [Create and Use an Internal Load Balancer with an App Service Environment][MakeILBASE] (Создание и использование внутренней подсистемы балансировки нагрузки со средой службы приложений).

При создании ASE на портале можно выбрать одновременное создание виртуальной сети либо выбрать для развертывания уже существующую виртуальную сеть.  При создании из шаблона необходимо: 
* Создать виртуальную сеть Resource Manager.
* Создать подсеть в этой виртуальной сети.  Рекомендуется подсеть ASE /25 со 128 адресами для расширения в будущем.  Ее нельзя изменить после создания ASE.
* Идентификатор ресурса виртуальной сети.  Его можно получить на портале Azure в списке свойств виртуальной сети Azure.
* Подписка, которая будет использоваться.
* Расположение для развертывания.

Для автоматизации создания ASE сделайте следующее:

1. Создайте ASE с помощью шаблона.  При создании внешнего ASE это последний шаг.  Если создается ASE с внутренним балансировщиком нагрузки, необходимо выполнить несколько дополнительных действий.
2. После создания ASE с внутренним балансировщиком нагрузки отправляется SSL-сертификат, соответствующий домену ASE с внутренним балансировщиком нагрузки.
3. Переданный SSL-сертификат назначается ASE с внутренним балансировщиком нагрузки в качестве SSL-сертификата по умолчанию.  Этот сертификат будет использоваться для передачи трафика SSL в приложения в среде службы приложений с внутренним балансировщиком нагрузки, если при обращении к ним используется общий корневой домен, назначенный этой среде (например, https://someapp.mycustomrootcomain.com).


## <a name="creating-the-ase"></a>Создание ASE
Пример шаблона Azure Resource Manager, с помощью которого создается ASE, и соответствующий файл параметров доступны на сайте [GitHub][quickstartasev2create].

Если вы хотите создать ASE с внутренним балансировщиком нагрузки, следует использовать шаблоны Resource Manager [отсюда][quickstartilbasecreate].  Они ориентированы на данный вариант использования.  Большинство параметров в файле *azuredeploy.parameters.json* используются при создании как ASE с внутренним балансировщиком нагрузки, так и со внешними ASE.  Ниже приведены параметры с особыми примечаниями и уникальные параметры, используемые при создании ASE с внутренним балансировщиком нагрузки.

* *interalLoadBalancingMode*. В большинстве случаев для этого параметра нужно задать значение 3. В таком случае трафик HTTP и HTTPS на портах 80 и 443 и порты канала данных и канала управления, прослушиваемые FTP-службой в среде службы приложений, будут привязаны к внутреннему адресу в виртуальной сети, выделенному внутренним балансировщиком нагрузки.  Если задать для этого свойства значение 2, к адресу внутреннего балансировщика нагрузки будут привязаны только порты, связанные со службой FTP (канала данных и канала управления), а HTTP- и HTTP-трафик останется привязанным к общедоступному виртуальному IP-адресу.
* *dnsSuffix*. Этот параметр определяет корневой домен по умолчанию, который будет назначен среде службы приложений.  В общедоступной версии службы приложений Azure корневой домен по умолчанию для всех веб-приложений — *azurewebsites.net*.  Однако так как ASE с внутренним балансировщиком нагрузки является внутренней для виртуальной сети пользователя, бессмысленно использовать корневой домен по умолчанию общедоступной службы.  Вместо этого ASE с внутренним балансировщиком нагрузки необходимо назначить корневой домен по умолчанию, который целесообразно использовать в пределах внутренней виртуальной сети компании.  Например, гипотетическая компания Contoso может использовать корневой домен по умолчанию *internal-contoso.com* для приложений, которые должны быть разрешаемыми и доступными только в виртуальной сети компании Contoso. 
* *ipSslAddressCount*. Для этого параметра в файле *azuredeploy.json* автоматически задается значение 0, так как средам службы приложений с внутренним балансировщиком нагрузки назначен только адрес этого балансировщика.  Для ASE с внутренним балансировщиком нагрузки нет явно заданных адресов SSL на основе IP, поэтому для пула этих адресов необходимо задать значение 0. В противном случае произойдет ошибка подготовки. 

Когда файл *azuredeploy.parameters.json* будет заполнен, можно приступить к созданию ASE с помощью приведенного ниже фрагмента кода Powershell.  Измените пути к файлам в соответствии с расположением файлов шаблонов Azure Resource Manager на вашем компьютере.  Кроме того, введите собственные значения для имени развертывания Azure Resource Manager и группы ресурсов.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

После отправки шаблона Azure Resource Manager для создания ASE потребуется около часа.  Созданная ASE появится на портале в списке сред службы приложений для подписки, использованной для развертывания.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Загрузка и настройка SSL-сертификата по умолчанию
После создания среды ASE с внутренним балансировщиком нагрузки ей необходимо назначить SSL-сертификат по умолчанию, который будет использоваться для установки SSL-подключений к приложениям.  Если DNS-суффикс по умолчанию среды службы приложений все той же гипотетической компании Contoso — *internal-contoso.com*, то для подключения к *https://случайному_приложению.internal-contoso.com* требуется сертификат SSL, действительный для домена **.internal-contoso.com*. 

Есть множество способов получить действительный SSL-сертификат, в частности можно получить сертификат от внутреннего центра сертификации, приобрести его у внешнего поставщика и использовать самозаверяющий сертификат.  Независимо от источника SSL-сертификата для него необходимо соответствующим образом настроить следующие атрибуты:

* *Subject* — для этого атрибута необходимо задать значение *.имя_корневого_домена.com*.
* *Subject Alternative Name* — этот атрибут должен содержать два значения: *.имя_корневого_домена.com* и *.scm.имя_корневого_домена.com*.  Вторая запись требуется, так как для SSL-подключений к сайту SCM или Kudu, связанному с каждым приложением, будет использоваться адрес в формате *имя_приложения.scm.имя_корневого_домена.com*.

После получения действительного SSL-сертификата требуется выполнить еще два дополнительных подготовительных действия.  SSL-сертификат необходимо преобразовать в PFX-файл и сохранить в таком формате.  PFX-файл должен содержать все промежуточные и корневые сертификаты, а также быть защищен паролем.

Затем PFX-файл необходимо преобразовать в строку Base64, так как SSL-сертификат будет загружен с использованием шаблона Azure Resource Manager.  Эти шаблоны представлены в виде текстовых файлов. Такое преобразование требуется, чтобы добавить PFX-файл в качестве параметра шаблона.

В приведенном ниже фрагменте кода Powershell показано, как создать самозаверяющий сертификат, экспортировать его в формате PFX-файла, преобразовать PFX-файл в строку в кодировке Base64 и сохранить эту строку в отдельном файле.  Код PowerShell для преобразования в строку в кодировке Base64 взят из [блога скриптов Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Когда сертификат SSL будет создан и преобразован в строку в кодировке Base64, можно приступить к [настройке сертификата SSL по умолчанию][quickstartconfiguressl] с использованием шаблона Azure Resource Manager, размещенного на сайте GitHub.

Ниже перечислены параметры, содержащиеся в файле *azuredeploy.parameters.json* .

* *appServiceEnvironmentName* — имя настраиваемой среды службы приложений с внутренним балансировщиком нагрузки.
* *existingAseLocation* — текстовая строка, содержащая регион Azure, где развернута среда службы приложений с внутренним балансировщиком нагрузки.  Например, South Central US.
* *pfxBlobString* — представление PFX-файла в виде строки в кодировке Base64.  Нужно скопировать строку, содержащуюся в файле exportedcert.pfx.b64, и вставить ее в качестве значения атрибута *pfxBlobString* в фрагменте кода выше.
* *password* — пароль, используемый для защиты PFX-файла.
* *certificateThumbprint* — отпечаток сертификата.  Если вы получите это значение из Powershell (например, значение *$certificate.Thumbprint* в приведенном выше фрагменте кода), его можно использовать без каких-либо изменений.  Однако если вы скопируете это значение в диалоговом окне сертификатов Windows, в нем нужно удалить лишние пробелы.  Значение атрибута *certificateThumbprint* должно выглядеть следующим образом: AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName* — понятный идентификатор строки для сертификата. Пользователь может выбрать его по своему усмотрению.  Это имя используется как часть уникального идентификатора Azure Resource Manager для сущности *Microsoft.Web/certificates* , представляющей SSL-сертификат.  Имя **должно** заканчиваться таким суффиксом: \_имя_ASE_InternalLoadBalancingASE.  Этот суффикс сообщает порталу о том, что для обеспечения безопасности ASE с поддержкой ILB используется сертификат.

Ниже приведен сокращенный пример файла *azuredeploy.parameters.json* .

    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Когда файл *azuredeploy.parameters.json* будет заполнен, переходите к настройке SSL-сертификата по умолчанию, используя приведенный ниже фрагмент кода Powershell.  Измените пути к файлам в соответствии с расположением файлов шаблонов Azure Resource Manager на вашем компьютере.  Кроме того, введите собственные значения для имени развертывания Azure Resource Manager и группы ресурсов.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

После отправки шаблона Azure Resource Manager применение изменений для одного внешнего интерфейса ASE займет примерно сорок минут.  Например, для среды ASE с размером по умолчанию, содержащей два внешних интерфейса, реализация шаблона займет примерно один час и двадцать минут.  Во время выполнения шаблона нельзя масштабировать среду ASE.  

Когда шаблон будет реализован, к приложениям в среде ASE с внутренним балансировщиком нагрузки можно будет получать доступ по протоколу HTTPS, а подключения будут защищены с помощью SSL-сертификата по умолчанию.  SSL-сертификат по умолчанию будет использоваться, если обращение к приложениям в ASE с внутренним балансировщиком нагрузки осуществляется с помощью сочетания имени приложения и имени узла по умолчанию.  Например, в *https://mycustomapp.internal-contoso.com* будет использоваться сертификат SSL по умолчанию для *.internal-contoso.com*.

Так же как и для приложений в общедоступной мультитенантной службе, разработчики могут настроить для отдельных приложений пользовательские имена узлов и уникальные привязки сертификатов SNI SSL.  

## <a name="asev1"></a>ASEv1 ##
Среда службы приложений имеет две версии: ASEv1 и ASEv2. Приведенные выше сведения относятся к версии ASEv2. В этом разделе показаны различия между ASEv1 и ASEv2.

В ASEv1 необходимо вручную управлять всеми ресурсами. Включая внешние интерфейсы, рабочие процессы и IP-адреса, использованные для SSL на основе IP. Перед масштабированием плана службы приложений необходимо выполнить масштабирование пула рабочих процессов, в котором необходимо разместить план.

ASEv1 использует модель ценообразования отличную от ASEv2. В ASEv1 необходимо платить за каждое выделенное ядро. Включая ядра, используемые для внешних интерфейсов, и рабочие процессы, которые не содержат рабочие нагрузки. В ASEv1 по умолчанию максимальный размер масштабирования среды службы приложений составляет до 55 узлов. Включая рабочие роли и внешние интерфейсы. Одним из преимуществ ASEv1 является возможность развертывания в классической виртуальной сети, а также виртуальной сети Resource Manager. Дополнительные сведения об ASEv1 см. в статье [Введение в среду службы приложения][ASEv1Intro].

Если вы хотите создать ASEv1 с помощью шаблона Resource Manager, см. статью [Создание среды службы приложений с внутренним балансировщиком нагрузки с помощью шаблонов Azure Resource Manager][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: ../../app-service-web/app-service-app-service-environment-create-ilb-ase-resourcemanager.md

