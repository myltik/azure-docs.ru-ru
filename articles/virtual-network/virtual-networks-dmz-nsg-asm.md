---
title: Пример сети периметра Azure. Создание простой сети периметра с группами безопасности сети | Документация Майкрософт
description: Создание сети периметра с группами безопасности сети
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: f8622b1d-c07d-4ea6-b41c-4ae98d998fff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ed172d552e1e4c9ee27c58abcd7ad2d98df21579
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "23125502"
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-classic-powershell"></a>Пример 1 – построение простой сети периметра с использованием групп безопасности сети и классического PowerShell
[Вернуться на страницу с советами и рекомендациями по построению периметра безопасности][HOME]

> [!div class="op_single_selector"]
> * [Шаблон Resource Manager](virtual-networks-dmz-nsg.md)
> * [Классическая модель: PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

В этой статье мы создадим простую сеть периметра с четырьмя серверами Windows Server и группами безопасности сети. Здесь также рассмотрены все используемые команды PowerShell для более глубокого понимания каждого этапа. В разделе «Сценарий прохождения трафика» также подробно описано поэтапное прохождение трафика через уровни защиты в сети периметра. Наконец, в разделе ссылок содержится весь код и приведены инструкции по созданию этой среды для тестирования и экспериментов с различными сценариями. 

![Входящая сеть периметра с группой безопасности сети][1]

## <a name="environment-description"></a>Описание среды
В этом примере используется подписка, которая содержит такие ресурсы:

* две облачные службы: FrontEnd001 и BackEnd001;
* виртуальная сеть CorpNetwork с двумя подсетями (FrontEnd и BackEnd);
* одна группа безопасности сети, которая применяется к обеим подсетям;
* сервер Windows Server, который представляет веб-сервер приложений (IIS01);
* два сервера Windows Server, представляющих тыловые серверы приложений (AppVM01, AppVM02);
* сервер Windows Server, который представляет DNS-сервер (DNS01).

Ниже в разделе справочной информации приведен сценарий PowerShell, который создает большую часть среды для этого примера. Сценарий создает также виртуальные машины и сети, но подробного описания этого процесса нет в этом документе. 

Чтобы создать среду, сделайте следующее:

1. Сохраните XML-файл конфигурации сети, который можно найти в разделе справочной информации (предварительно укажите в файле имена, расположение и IP-адреса в соответствии с нужным сценарием).
2. Измените пользовательские переменные в сценарии в соответствии со средой, в которой он будет выполняться (подписки, имена служб и т. д.).
3. Запустите сценарий в PowerShell.

>[!Note]
>Регион, указанный в сценарии PowerShell, должен соответствовать региону, указанному в XML-файле конфигурации сети.
>
>

После успешного запуска сценария вы можете выполнить дополнительные (необязательные) действия. В разделе справочной информации приведены два сценария для настройки простого веб-приложения на веб-сервере и сервере приложений. Это позволит провести тестирование, используя данную конфигурацию сети периметра.

В следующих разделах содержится подробное описание групп безопасности сети и принципа их работы на примере основных строк этого сценария PowerShell.

## <a name="network-security-groups-nsg"></a>Группы безопасности сети
В этом примере создается группа безопасности сети, после чего в нее загружаются шесть правил. 

> [!TIP]
> В общем, сначала создаются конкретные правила разрешения, а затем более общие правила запрета. Порядок выполнения правил задается назначенным приоритетом. Когда система находит правило, применимое к пакету трафика, остальные правила игнорируются. Правила группы безопасности сети могут применяться ко входящему или исходящему трафику (относительно подсети).
> 
> 

Декларативно для входящего трафика определяются следующие правила.

1. Внутренний трафик DNS (порт 53) разрешается.
2. Трафик RDP (порт 3389) из Интернета к любой виртуальной машине разрешается.
3. Трафик HTTP (порт 80) из Интернета к веб-серверу (IIS01) разрешается.
4. Весь трафик (все порты) от сервера IIS01 к серверу AppVM1 разрешается.
5. Весь трафик (все порты) из Интернета ко всей виртуальной сети (обе подсети) запрещается.
6. Весь трафик (все порты) из подсети FrontEnd в подсеть BackEnd запрещается.

С учетом этих правил, связанных с каждой подсетью, если HTTP-запрос поступил из Интернета на веб-сервер, должны быть применены оба правила 3 (Разрешить) и 5 (Запретить), но так как правило 3 имеет более высокий приоритет, то будет применено только оно, и правило 5 не задействуется. Таким образом, HTTP-запрос сможет достичь веб-сервера. Если тот же трафик пытается достичь сервера DNS01, то сначала будет применено правило 5 (Запретить), и трафику не будет разрешено пройти на сервер. Правило 6 (Запретить) блокирует трафик из интерфейсной подсети во внутреннюю подсеть (за исключением трафика, разрешенного правилами 1 и 4). Это позволяет защитить внутреннюю сеть при компрометации веб-приложения на интерфейсном сервере. В этом случае злоумышленник получит ограниченный доступ к "защищенной" внутренней сети (только к ресурсам, предоставляемым на сервере AppVM01).

Для исходящего трафика стандартное правило разрешает любой трафик в Интернет. В этом примере мы разрешаем исходящий трафик и не меняем для него никаких правил. Чтобы заблокировать трафик в обоих направлениях, требуется определенная пользователем маршрутизация. Этот аспект рассмотрен в примере 3 в статье [о сетевой безопасности в облачных службах][HOME].

Ниже приведены пояснения по каждому правилу. (**Примечание.** Все объекты в следующем списке, в начале имени которых стоит знак доллара (например, $NSGName), являются пользовательскими переменными из скрипта, который приведен в разделе справочной информации ниже.)

1. Сначала создается группа безопасности сети, в которую затем будут добавлены правила.

    ```PowerShell
    New-AzureNetworkSecurityGroup -Name $NSGName `
        -Location $DeploymentLocation `
        -Label "Security group for $VNetName subnets in $DeploymentLocation"
    ```

2. Первое правило в этом примере разрешает трафик DNS между всеми внутренними сетями к DNS-серверу во внутренней подсети. У правила есть несколько важных параметров.
   
   * Параметр Type определяет тип трафика (входящий или исходящий), к которому будет применяться правило. Направление определяется с точки зрения подсети или виртуальной машины (в зависимости от того, где работает группа безопасности сети). Таким образом, если для параметра Type задано значение Inbound и в подсети обнаруживается входящий трафик, к нему будет применено это правило. Исходящий из подсети трафик не будет обрабатываться этим правилом.
   * Параметр Priority определяет порядок обработки потока трафика. Чем меньше число, тем выше приоритет. Как только для потока трафика определяется подходящее правило, все остальные правила игнорируются. Например, правило с приоритетом 1 разрешает трафик, а правило с приоритетом 2 — запрещает. Если оба правила применимы к трафику, то он будет разрешен, так как правило 1 имеет более высокий приоритет и после его применения обработка остальных правил прекращается.
   * Параметр Action определяет, что нужно делать с трафиком, который подпадает под действие правила, — запретить или разрешить.

    ```PowerShell    
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
        -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[4] `
        -DestinationPortRange '53' `
        -Protocol *
    ```

3. Это правило пропускает трафик RDP из Интернета к порту RDP на любом сервере в связанной подсети. В правиле используется два специальных типа адресных префиксов, VIRTUAL_NETWORK и INTERNET, что позволяет быстро описать большую группу адресных префиксов.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
         -Type Inbound -Priority 110 -Action Allow `
         -SourceAddressPrefix INTERNET -SourcePortRange '*' `
         -DestinationAddressPrefix VIRTUAL_NETWORK `
         -DestinationPortRange '3389' `
         -Protocol *
    ```

4. Это правило пропускает входящий интернет-трафик к веб-серверу. Это правило не влияет на маршрутизацию, а только пропускает трафик к серверу IIS01. Таким образом, правило пропустит интернет-трафик к веб-серверу, а обработка остальных правил прекратится. (Правило с приоритетом 140 блокирует весь остальной входящий интернет-трафик.) Если вам нужно обрабатывать только HTTP-трафик, область применения этого правила можно дополнительно сузить, пропуская трафик только к конечному порту 80.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
         -Type Inbound -Priority 120 -Action Allow `
         -SourceAddressPrefix Internet -SourcePortRange '*' `
         -DestinationAddressPrefix $VMIP[0] `
         -DestinationPortRange '*' `
         -Protocol *
    ```

5. Это правило разрешает трафик от сервера IIS01 к серверу AppVM01. Одно из следующих после него правил блокирует весь остальной трафик от подсети FrontEnd к подсети BackEnd. Это правило можно улучшить, указав в нем номер порта (если он известен). Например, если сервер IIS обращается только к серверу SQL Server на сервере AppVM01, диапазон конечных портов следует изменить с «*» (все) на 1433 (порт SQL). Таким образом, вы уменьшите количество направлений возможной атаки на сервер AppVM01, если веб-приложение будет скомпрометировано.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
        -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
    ```

6. Это правило блокирует интернет-трафик ко всем серверам в сети. В сочетании с правилами, имеющими приоритет 110 и 120, это правило пропускает входящий интернет-трафик только к брандмауэру и портам RDP на серверах. Остальной трафик блокируется. Это резервное правило для блокирования всех непредвиденных потоков.
    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $VNetName VNet from the Internet" `
        -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *
    ```
7. Последнее правило блокирует трафик из подсети FrontEnd в подсеть BackEnd. Это правило обрабатывает только входящий трафик, поэтому исходящие подключения (из внутренней к интерфейсной) разрешены.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
        -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix `
        -DestinationPortRange '*' `
        -Protocol * 
    ```

## <a name="traffic-scenarios"></a>Варианты прохождения трафика
#### <a name="allowed-internet-to-web-server"></a>Запрос из Интернета к веб-серверу (*разрешено*)
1. Интернет-пользователь запрашивает страницу HTTP от FrontEnd001.CloudApp.Net (облачная служба, открытая для доступа из Интернета)
2. Облачная служба направляет трафик через открытую конечную точку на порте 80 на веб-сервер IIS01.
3. Подсеть Frontend начинает обработку правила для входящего трафика.
   1. Правило 1 группы безопасности сети (DNS) не применяется — переход к следующему правилу.
   2. Правило 2 группы безопасности сети (RDP) не применяется — переход к следующему правилу.
   3. Правило 3 группы безопасности сети (интернет-подключение к IIS01) применяется — трафик разрешается, дальнейшая обработка правил прекращается.
4. Трафик достигает внутреннего IP-адреса веб-сервера IIS01 (10.0.1.5).
5. IIS01 ожидает передачу данных из Интернета, получает этот запрос и начинает его обработку.
6. IIS01 запрашивает сведения у сервера SQL Server на сервере AppVM01.
7. В подсети Frontend нет правил для обработки исходящего трафика, поэтому трафик разрешается.
8. Подсеть BackEnd начинает обработку правил для входящего трафика.
   1. Правило 1 группы безопасности сети (DNS) не применяется — переход к следующему правилу.
   2. Правило 2 группы безопасности сети (RDP) не применяется — переход к следующему правилу.
   3. Правило 3 группы безопасности сети (Подключение из Интернета к брандмауэру) не применяется, переход к следующему правилу
   4. Правило 4 группы безопасности сети (трафик от IIS01 к AppVM01) применяется — трафик разрешается, дальнейшая обработка правил прекращается.
9. Сервер AppVM01 получает SQL-запрос и отправляет ответ.
10. Так как правил для исходящего трафика во внутренней подсети нет, отправка ответа разрешается.
11. Подсеть Frontend начинает обработку правила для входящего трафика.
    1. Так как в группе безопасности сети нет правил, которые могут применяться ко входящему трафику из подсети Backend в подсеть Frontend, все правила группы безопасности игнорируются.
    2. Системное правило по умолчанию, которое разрешает трафик между подсетями, разрешает прохождение этого трафика, поэтому трафик разрешается.
12. Сервер IIS получает ответ SQL, завершает HTTP-запрос и отправляет ответ запрашивающему
13. Так как для подсети FrontEnd нет исходящих правил, то отправка ответа разрешена, и интернет-пользователь получает запрашиваемую веб-страницу.

#### <a name="allowed-rdp-to-backend"></a>(*Разрешено*) Запрос сеанса RDP к подсети BackEnd
1. Администратор сервера через Интернет создает сеанс удаленного рабочего стола (RDP) с AppVM01 на BackEnd001.CloudApp.Net:xxxxx, где xxxxx — номер порта, назначенный случайным образом для протокола удаленного рабочего стола на AppVM01 (назначенный порт можно найти на портале Azure или определить с помощью PowerShell).
2. Подсеть BackEnd начинает обработку правила для входящего трафика:
   1. Правило 1 группы безопасности сети (DNS) не применяется — переход к следующему правилу.
   2. Правило 2 группы безопасности сети (RDP) применяется — трафик разрешается, дальнейшая обработка правил прекращается.
3. Правил для исходящего трафика нет, поэтому применяются правила по умолчанию и обратный трафик разрешается.
4. Начинается сеанс RDP.
5. AppVM01 запрашивает имя пользователя и пароль.

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>DNS-запрос веб-сервера к серверу DNS (*разрешено*)
1. Веб-серверу IIS01 нужен веб-канал данных с сайта www.data.gov, но ему необходимо разрешить адрес.
2. В конфигурации виртуальной сети в качестве основного DNS-сервера указан сервер DNS01 (10.0.2.4 в подсети Backend). Веб-сервер IIS01 отправляет DNS-запрос серверу DNS01.
3. В подсети Frontend нет правил для обработки исходящего трафика — трафик разрешается.
4. Подсеть BackEnd начинает обработку входящего правила:
   * Правило 1 группы безопасности сети (DNS) применяется — трафик разрешается, дальнейшая обработка правил прекращается.
5. DNS-сервер получает запрос.
6. Указанный адрес отсутствует в кэше DNS-сервера, поэтому он отправляет запрос к корневому DNS-серверу в Интернете.
7. В подсети BackEnd нет правил для исходящего трафика, поэтому трафик разрешается.
8. DNS-сервер в Интернете отправляет ответ. Так как этот сеанс инициирован внутри системы, ответ пропускается.
9. DNS-сервер кэширует ответ и отправляет ответ на исходный запрос обратно на сервер IIS01.
10. В подсети BackEnd нет правил для исходящего трафика, поэтому трафик разрешается.
11. Подсеть FrontEnd начинает обработку входящего правила:
    1. Так как в группе безопасности сети нет правил, которые могут применяться ко входящему трафику из подсети Backend в подсеть Frontend, все правила группы безопасности игнорируются.
    2. Системное правило по умолчанию, которое разрешает трафик между подсетями, разрешает прохождение этого трафика, поэтому трафик разрешается.
12. Сервер IIS01 получает ответ от сервера DNS01.

#### <a name="allowed-web-server-access-file-on-appvm01"></a>Обращение веб-сервера к файлу на сервере AppVM01 (*разрешено*)
1. IIS01 запрашивает файл на AppVM01
2. В подсети Frontend нет правил для обработки исходящего трафика — трафик разрешается.
3. Подсеть BackEnd начинает обработку правил для входящего трафика.
   1. Правило 1 группы безопасности сети (DNS) не применяется — переход к следующему правилу.
   2. Правило 2 группы безопасности сети (RDP) не применяется — переход к следующему правилу.
   3. Правило 3 группы безопасности сети (интернет-подключение к IIS01) не применяется — переход к следующему правилу.
   4. Правило 4 группы безопасности сети (трафик от IIS01 к AppVM01) применяется — трафик разрешается, дальнейшая обработка правил прекращается.
4. Сервер AppVM01 получает запрос и отправляет ответ в виде файла (предполагается, что доступ авторизован).
5. Так как правил для исходящего трафика во внутренней подсети нет, отправка ответа разрешается.
6. Подсеть Frontend начинает обработку правила для входящего трафика.
   1. Так как в группе безопасности сети нет правил, которые могут применяться ко входящему трафику из подсети Backend в подсеть Frontend, все правила группы безопасности игнорируются.
   2. Системное правило по умолчанию, которое разрешает трафик между подсетями, разрешает прохождение этого трафика, поэтому трафик разрешается.
7. Сервер IIS получает запрос

#### <a name="denied-web-to-backend-server"></a>Запрос из Интернета к внутреннему серверу (*запрещено*)
1. Интернет-пользователь пытается получить доступ к файлу на сервере AppVM01 через службу BackEnd001.CloudApp.Net.
2. Так как здесь нет открытых конечных точек для файловых ресурсов, этот запрос не будет передан облачной службе и не поступит на сервер.
3. Если бы конечные точки по какой-либо причине были открыты, правило 5 группы безопасности сети (Запрос из Интернета к виртуальной сети) заблокировало бы этот трафик

#### <a name="denied-web-dns-look-up-on-dns-server"></a>DNS-запрос из Интернета к серверу DNS (*запрещено*)
1. Интернет-пользователь пытается выполнить запрос внутренней записи DNS на DNS01 через службу BackEnd001.CloudApp.Net
2. Так как для DNS-сервера не открыты конечные точки, этот запрос не будет передан облачной службе и не достигнет сервера.
3. Если бы конечные точки по какой-либо причине были открыты, правило 5 группы безопасности сети (Запрос из Интернета к виртуальной сети) заблокировало бы этот трафик (Обратите внимание, что правило 1 (DNS) в этом случае не было бы применено по двум причинам. Во-первых, адрес источника находится в Интернете, а это правило применяется только к адресам, источником которых является локальная виртуальная сеть. Во-вторых, это разрешающее правило, поэтому оно никогда не будет запрещать трафик.)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Запрещено*) Запрос из Интернета к SQL через брандмауэр
1. Интернет-пользователь запрашивает данные SQL из FrontEnd001.CloudApp.Net (облачная служба, открытая для доступа через Интернет)
2. Так как для данных SQL не открыты конечные точки, этот запрос не будет передан облачной службе и не достигнет брандмауэра.
3. Если конечные точки будут открыты по какой-то причине, подсеть FrontEnd начнет обработку правил для входящего трафика.
   1. Правило 1 группы безопасности сети (DNS) не применяется — переход к следующему правилу.
   2. Правило 2 группы безопасности сети (RDP) не применяется — переход к следующему правилу.
   3. Правило 3 группы безопасности сети (интернет-подключение к IIS01) применяется — трафик разрешается, дальнейшая обработка правил прекращается.
4. Трафик достигает внутреннего IP-адреса сервера IIS01 (10.0.1.5).
5. Сервер IIS01 не ожидает передачу данных на порту 1433, поэтому ответ на запрос отсутствует.

## <a name="conclusion"></a>Заключение
Этот пример демонстрирует достаточно простой и эффективный способ изоляции внутренней подсети от входящего трафика.

Дополнительные примеры и общие сведения о периметре безопасности сети можно найти [здесь][HOME].

## <a name="references"></a>Ссылки
### <a name="main-script-and-network-config"></a>Основной сценарий и конфигурация сети
Сохраните полный сценарий в файл сценария PowerShell. Сохраните конфигурацию сети в файл с именем NetworkConf1.xml.
При необходимости измените определенные пользователем переменные и выполните сценарий.

#### <a name="full-script"></a>Полный сценарий
Используя определенные пользователем переменные, этот сценарий выполнит следующие действия.

1. Подключение к подписке Azure
2. Создайте учетную запись хранения.
3. Создаст виртуальную сеть и две подсети, которые указаны в файле конфигурации сети.
4. Создаст четыре сервера виртуальных машин под управлением Windows.
5. Настроит группу безопасности сети:
   * Создание группы безопасности сети
   * добавит в нее правила;
   * привяжет группу безопасности сети к соответствующим подсетям.

Этот сценарий PowerShell должен запускаться локально на ПК или сервере, подключенном к Интернету.

> [!IMPORTANT]
> При запуске этого сценария могут выдаваться предупреждения и другие информационные сообщения PowerShell. Обращать внимание следует только на сообщения об ошибках, выделенные красным цветом.
> 
>

```PowerShell
<# 
 .SYNOPSIS
  Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)

 .DESCRIPTION
  This script will build out a sample DMZ setup containing:
   - A default storage account for VM disks
   - Two new cloud services
   - Two Subnets (FrontEnd and BackEnd subnets)
   - One server on the FrontEnd Subnet
   - Three Servers on the BackEnd Subnet
   - Network Security Groups to allow/deny traffic patterns as declared

  Before running script, ensure the network configuration file is created in
  the directory referenced by $NetworkConfigFile variable (or update the
  variable to reflect the path and file name of the config file being used).

 .Notes
  Security requirements are different for each use case and can be addressed in a
  myriad of ways. Please be sure that any sensitive data or applications are behind
  the appropriate layer(s) of protection. This script serves as an example of some
  of the techniques that can be used, but should not be used for all scenarios. You
  are responsible to assess your security needs and the appropriate protections
  needed, and then effectively implement those protections.

  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
   IIS01      - 10.0.1.5

  BackEnd Service (BackEnd subnet 10.0.2.0/24)
   DNS01      - 10.0.2.4
   AppVM01    - 10.0.2.5
   AppVM02    - 10.0.2.6

#>

# Fixed Variables
    $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
    $VMName = @()
    $ServiceName = @()
    $VMFamily = @()
    $img = @()
    $size = @()
    $SubnetName = @()
    $VMIP = @()

# User-Defined Global Variables
  # These should be changes to reflect your subscription and services
  # Invalid options will fail in the validation section

  # Subscription Access Details
    $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

  # VM Account, Location, and Storage Details
    $LocalAdmin = "theAdmin"
    $DeploymentLocation = "Central US"
    $StorageAccountName = "vmstore02"

  # Service Details
    $FrontEndService = "FrontEnd001"
    $BackEndService = "BackEnd001"

  # Network Details
    $VNetName = "CorpNetwork"
    $FESubnet = "FrontEnd"
    $FEPrefix = "10.0.1.0/24"
    $BESubnet = "BackEnd"
    $BEPrefix = "10.0.2.0/24"
    $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"

  # VM Base Disk Image Details
    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

  # NSG Details
    $NSGName = "MyVNetSG"

# User-Defined VM Specific Configuration
    # Note: To ensure proper NSG Rule creation later in this script:
    #       - The Web Server must be VM 0
    #       - The AppVM1 Server must be VM 1
    #       - The DNS server must be VM 3
    #
    #       Otherwise the NSG rules in the last section of this
    #       script will need to be changed to match the modified
    #       VM array numbers ($i) so the NSG Rule IP addresses
    #       are aligned to the associated VM IP addresses.

    # VM 0 - The Web Server
      $VMName += "IIS01"
      $ServiceName += $FrontEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $FESubnet
      $VMIP += "10.0.1.5"

    # VM 1 - The First Application Server
      $VMName += "AppVM01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.5"

    # VM 2 - The Second Application Server
      $VMName += "AppVM02"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.6"

    # VM 3 - The DNS Server
      $VMName += "DNS01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.4"

# ----------------------------- #
# No User-Defined Variables or  #
# Configuration past this point #
# ----------------------------- #    

  # Get your Azure accounts
    Add-AzureAccount
    Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
    Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

  # Create Storage Account
    If (Test-AzureName -Storage -Name $StorageAccountName) { 
        Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
        Return}
    Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
          New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

  # Update Subscription Pointer to New Storage Account
    Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
    Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

# Validation
$FatalError = $false

If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
     Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
     $FatalError = $true}

If (Test-AzureName -Service -Name $FrontEndService) { 
    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

If (Test-AzureName -Service -Name $BackEndService) { 
    Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

If (-Not (Test-Path $NetworkConfigFile)) { 
    Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The network configuration file was found" -ForegroundColor Green
        If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
            Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
            $FatalError = $true}
        Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

If ($FatalError) {
    Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
    Return}
Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

# Create VNET
    Write-Host "Creating VNET" -ForegroundColor Cyan 
    Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

# Create Services
    Write-Host "Creating Services" -ForegroundColor Cyan
    New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
    New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

# Build VMs
    $i=0
    $VMName | Foreach {
        Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
        New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
            Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
            Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
            Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
            Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
            Remove-AzureEndpoint -Name "PowerShell" | `
            New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
            # Note: A Remote Desktop endpoint is automatically created when each VM is created.
        $i++
    }
    # Add HTTP Endpoint for IIS01
    Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM

# Configure NSG
    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

  # Build the NSG
    Write-Host "Building the NSG" -ForegroundColor Cyan
    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

  # Add NSG Rules
    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
        -SourceAddressPrefix Internet -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
        -Protocol *

    # Assign the NSG to the Subnets
        Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

# Optional Post-script Manual Configuration
  # Install Test Web App (Run Post-Build Script on the IIS Server)
  # Install Backend resource (Run Post-Build Script on the AppVM01)
  Write-Host
  Write-Host "Build Complete!" -ForegroundColor Green
  Write-Host
  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
  Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
  Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
  Write-Host
```

#### <a name="network-config-file"></a>Файл конфигурации сети
Измените в этом XML-файле расположение и сохраните его. Затем добавьте ссылку на файл в переменную $NetworkConfigFile в приведенном выше сценарии.

```XML
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="DNS01" IPAddress="10.0.2.4" />
        <DnsServer name="Level3" IPAddress="209.244.0.3" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="CorpNetwork" Location="Central US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="FrontEnd">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="BackEnd">
            <AddressPrefix>10.0.2.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
        <DnsServersRef>
          <DnsServerRef name="DNS01" />
          <DnsServerRef name="Level3" />
        </DnsServersRef>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

#### <a name="sample-application-scripts"></a>Примеры сценариев приложения
Пример приложения для этого и других примеров сети периметра можно скачать по [этой ссылке][SampleApp].

## <a name="next-steps"></a>Дополнительная информация
* Обновите и сохраните XML-файл
* Запустите сценарий PowerShell для создания среды
* Установите пример приложения
* Проверьте прохождение разных потоков трафика через эту сеть периметра.

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Входящая сеть периметра с группой безопасности сети"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

