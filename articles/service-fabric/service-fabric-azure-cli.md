---
title: "Взаимодействие с кластерами Service Fabric с помощью интерфейса командной строки | Документация Майкрософт"
description: "Как использовать интерфейс командной строки Azure для взаимодействия с кластером Service Fabric."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: c3ec8ff3-3b78-420c-a7ea-0c5e443fb10e
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2016
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: caf6dd414bd8f8180c90835dd9744dcd98f7709c


---
# <a name="using-the-azure-cli-to-interact-with-a-service-fabric-cluster"></a>Использование интерфейса командной строки Azure для взаимодействия с кластером Service Fabric
Вы можете взаимодействовать с кластером Service Fabric с компьютера под управлением Linux с помощью интерфейса командной строки (CLI) Azure для Linux.

Для этого сначала нужно получить и установить последнюю версию CLI из репозитория Git с помощью следующих команд:

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

По всем командам можно получить справку. Наберите имя нужной команды, чтобы узнать о ее возможностях. Для команд также поддерживается функция автозавершения. Например, следующая команда отобразит справку по всем командам application. 

```sh
 azure servicefabric application 
```

Можно ограничить справочную информацию одной конкретной командой, как показано в следующем примере:

```sh
 azure servicefabric application  create
```

Чтобы включить в интерфейсе командной строки функцию автозавершения, выполните следующие команды:

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

Следующие команды позволяют подключиться к кластеру и просмотреть список узлов кластера:

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

Чтобы узнать, какие можно использовать именованные параметры и для чего они нужны, введите после команды оператор --help. Например:

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

Чтобы подключиться к кластеру с несколькими компьютерами с компьютера, **который не входит в кластер**, используйте следующую команду:

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

Замените тег PublicIPorFQDN нужным IP-адресом или полным доменным именем. Чтобы подключиться к кластеру с несколькими компьютерами с компьютера, **который входит в кластер**, используйте следующую команду:

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

Можно использовать PowerShell или CLI для взаимодействия с кластером Service Fabric, созданным с помощью портала Azure. 

> [!WARNING]
> Эти кластеры не являются защищенными, и вы можете поставить ваш компьютер под угрозу, добавив общедоступный IP-адрес в манифест кластера.

## <a name="using-the-azure-cli-to-connect-to-a-service-fabric-cluster"></a>Использование интерфейса командной строки Azure для подключения к кластеру Service Fabric
Следующие команды интерфейса командной строки Azure позволяют подключиться к безопасному кластеру. Сведения о сертификате должны соответствовать сертификату на узлах кластера.

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```

Если ваш сертификат выдан центром сертификации, следует добавить параметр --ca-cert-path, как показано в следующем примере: 

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Если вы используете несколько центров сертификации, перечислите их все, разделив запятыми.

Если параметр "Общее имя" в сертификате не соответствует конечной точке подключения, для обхода проверки можно использовать параметр `--strict-ssl` , как показано в следующем примере: 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl false 
```

Если вы хотите пропустить проверку центра сертификации, можно добавить параметр --reject-unauthorized, как показано в следующем примере: 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized false 
```

После подключения вы сможете взаимодействовать с кластером с помощью дополнительных команд интерфейса командной строки. 

## <a name="deploying-your-service-fabric-application"></a>Развертывание приложения Service Fabric
Выполните следующие команды, чтобы скопировать, зарегистрировать и запустить приложение Service Fabric:

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## <a name="upgrading-your-application"></a>Обновление приложения
Процесс выполняется так же, [как и в среде Windows](service-fabric-application-upgrade-tutorial-powershell.md).

Выполняйте сборку, копирование, регистрацию и создание приложения в корневом каталоге проекта. Если экземпляр приложения имеет имя fabric:/MySFApp и тип MySFApp, команда будет выглядеть так:

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

Внесите изменения в приложение и выполните повторную сборку службы в измененном виде.  Замените файл манифеста измененной службы (ServiceManifest.xml) обновленной версией для этой службы (а также все необходимые файлы из каталогов Code, Config и Data). Также замените манифест приложения (ApplicationManifest.xml) обновленной версией для приложения и измененной службы.  Теперь скопируйте и зарегистрируйте обновленное приложение с помощью следующих команд:

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

Теперь можно запустить обновление приложения с помощью следующей команды:

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0  --rolling-upgrade-mode UnmonitoredAuto
```

Обновление приложения можно отслеживать с помощью SFX. Процесс обновления завершится через несколько минут.  Можно также проверить обновленное приложение на наличие ошибок или использовать возможность автоматического отката в Service Fabric.

## <a name="converting-from-pfx-to-pem-and-vice-versa"></a>Преобразование из PFX в PEM и обратно

Для доступа к безопасным кластерам, работающим в разных средах, может потребоваться установить сертификат на локальном компьютере (под управлением Windows или Linux). Например, при доступе к защищенному кластеру Linux с компьютера Windows (или наоборот) может потребоваться преобразовать сертификат из формата PFX в PEM (или наоборот). 

Чтобы преобразовать PEM-файл в PFX-файл, используйте следующую команду:

```bash
openssl pkcs12 -export -out certificate.pfx -inkey mycert.pem -in mycert.pem -certfile mycert.pem
```

Чтобы преобразовать PFX-файл в PEM-файл, используйте следующую команду:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Дополнительные сведения см. в [документации по OpenSSL](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html).

## <a name="troubleshooting"></a>Устранение неполадок
### <a name="copying-of-the-application-package-does-not-succeed"></a>Копирование пакета приложения завершается ошибками
Проверьте, установлен ли клиент `openssh`. Так как по умолчанию он отсутствует в Ubuntu Desktop, установите его с помощью следующей команды.

```
 sudo apt-get install openssh-server openssh-client**
```

Если проблема сохранится, попробуйте отключить PAM для ssh, изменив файл **sshd_config** с помощью следующей команды:

```sh
 sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
 sudo service sshd reload
```

Если проблема сохранится, попробуйте число количество сеансов ssh, выполнив следующие команды:

```sh
 sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
 sudo service sshd reload
```
Использование ключей (вместо паролей) для проверки подлинности ssh пока не поддерживается (так как платформа использует ssh для копирования пакетов), поэтому используйте только проверку пароля.



## <a name="next-steps"></a>Дальнейшие действия
Настройте среду разработки и разверните приложение Service Fabric в кластер Linux.




<!--HONumber=Dec16_HO2-->


