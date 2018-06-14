---
title: Пример сети периметра. Создание сети периметра для защиты приложений с брандмауэром и группами безопасности сети | Документация Майкрософт
description: Построение сети периметра с брандмауэром и группами безопасности сети
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: cc0e8a3fa749eb2e6f65ef92c2d3cb404cfc8bc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23126932"
---
# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Пример 2. Построение сети периметра для защиты приложений с брандмауэром и группами безопасности сети
[Вернуться на страницу с советами и рекомендациями по построению периметра безопасности][HOME]

В этом примере будет создана сеть периметра с брандмауэром, четырьмя серверами Windows Server и группами безопасности сети. В нем также будут рассмотрены все используемые команды для более глубокого понимания каждого этапа. В разделе «Сценарий прохождения трафика» также подробно описано поэтапное прохождение трафика через уровни защиты в сети периметра. Наконец, в разделе ссылок содержится весь код и приведены инструкции по созданию этой среды для тестирования и экспериментов с различными сценариями. 

![Входящая сеть периметра с виртуальным сетевым устройством и группой безопасности сети][1]

## <a name="environment-description"></a>Описание среды
В этом примере используется подписка, которая содержит такие компоненты:

* две облачные службы: FrontEnd001 и BackEnd001;
* виртуальная сеть CorpNetwork с двумя подсетями (FrontEnd и BackEnd);
* одна группа безопасности сети, которая применяется к обеим подсетям;
* виртуальное сетевое устройство, в этом примере — брандмауэр Barracuda NextGen Firewall, подключенный к подсети FrontEnd;
* сервер Windows Server, который представляет веб-сервер приложений (IIS01);
* два сервера Windows Server, представляющих тыловые серверы приложений (AppVM01, AppVM02);
* сервер Windows Server, который представляет DNS-сервер (DNS01).

> [!NOTE]
> Хотя в этом примере используется брандмауэр Barracuda NextGen Firewall, вместо него может быть использовано множество других виртуальных сетевых устройств.
> 
> 

В разделе справочной информации приведен сценарий PowerShell, который создает большую часть описанной выше среды. Сценарий создает также виртуальные машины и сети, но подробного описания этого процесса нет в этом документе.

Чтобы создать среду, сделайте следующее.

1. Сохраните XML-файл конфигурации сети, который можно найти в разделе справочной информации (предварительно укажите в файле имена, расположение и IP-адреса в соответствии с нужным сценарием).
2. Измените пользовательские переменные в сценарии в соответствии со средой, в которой он будет выполняться (подписки, имена служб и т. д.).
3. Запустите сценарий в PowerShell.

**Примечание.** Регион, указанный в сценарии PowerShell, должен соответствовать региону, указанному в XML-файле конфигурации сети.

После успешного выполнения сценария можно выполнить следующие действия.

1. Настройте правила брандмауэра (см. раздел "Правила брандмауэра" ниже).
2. В разделе справочной информации приведены два сценария для настройки веб-сервера и сервера приложений с простым веб-приложением, с помощью которых можно протестировать эту конфигурацию сети периметра.

В следующем разделе приводится объяснение для большинства инструкций сценариев, касающихся групп безопасности сети.

## <a name="network-security-groups-nsg"></a>Группы безопасности сети
В этом примере создается группа безопасности сети, после чего в нее загружаются шесть правил. 

> [!TIP]
> В общем, сначала создаются конкретные правила разрешения, а затем более общие правила запрета. Порядок выполнения правил задается назначенным приоритетом. Когда система находит правило, применимое к пакету трафика, остальные правила игнорируются. Правила группы безопасности сети могут применяться ко входящему или исходящему трафику (относительно подсети).
> 
> 

Декларативно для входящего трафика определяются следующие правила.

1. Внутренний трафик DNS (порт 53) разрешается.
2. Трафик RDP (порт 3389) из Интернета к любой виртуальной машине разрешается
3. Трафик HTTP (порт 80) из Интернета к виртуальному сетевому устройству (брандмауэр) разрешается
4. Весь трафик (для всех портов) от IIS01 к AppVM1 разрешается
5. Весь трафик (все порты) из Интернета ко всей виртуальной сети (обе подсети) запрещается.
6. Весь трафик (все порты) из подсети FrontEnd в подсеть BackEnd запрещается.

С учетом этих правил, связанных с каждой подсетью, если HTTP-запрос поступил из Интернета на веб-сервер, должны быть применены оба правила 3 (Разрешить) и 5 (Запретить), но так как правило 3 имеет более высокий приоритет, то будет применено только оно, и правило 5 не задействуется. Таким образом запрос HTTP сможет достичь брандмауэра. Если тот же трафик пытается достичь сервера DNS01, то сначала будет применено правило 5 (Запретить), и трафику не будет разрешено пройти на сервер. Правило 6 (запретить) блокирует трафик из подсети FrontEnd в подсеть BackEnd (за исключением трафика, разрешенного правилами 1 и 4). Это позволяет защитить сеть BackEnd при компрометации веб-приложения на сервере FrontEnd. В этом случае злоумышленник получит ограниченный доступ к «защищенной» сети BackEnd (только к ресурсам, предоставляемым на сервере AppVM01).

Для исходящего трафика стандартное правило разрешает любой трафик в Интернет. В этом примере мы разрешаем исходящий трафик и не меняем для него никаких правил. Для блокировки трафика в обоих направлениях требуется определяемая пользователем маршрутизация. Она рассматривается в другом примере, который можно найти в [основном документе о периметре безопасности][HOME].

Описанные выше правила групп безопасности сети очень похожи на правила, приведенные в статье [Пример 1. Построение простой сети периметра с группами безопасности сети][Example1]. Пожалуйста, ознакомьтесь с описанием групп безопасности сети в этом документе, чтобы получить подробное представление о каждом правиле группы безопасности сети и его атрибутах.

## <a name="firewall-rules"></a>Правила брандмауэра
Для управления брандмауэром и создания необходимых конфигураций на компьютер необходимо установить клиент управления. Инструкции по управлению устройством приведены в документации брандмауэра (или другого виртуального сетевого устройства). В оставшейся части этого раздела описывается конфигурация самого брандмауэра с помощью клиента управления поставщика (т. е. без использования портала Azure или PowerShell).

Инструкции по загрузке клиента и подключению к брандмауэру Barracuda, который используется в этом примере, см в статье [Администрирование Barracuda NG](https://techlib.barracuda.com/NG61/NGAdmin)

В брандмауэре необходимо создать правила перенаправления. Поскольку в этом примере входящий трафик только пересылается на брандмауэр, а затем на веб-сервер, потребуется только одно правило перенаправления NAT. В брандмауэре Barracuda NextGen Firewall, используемом в этом примере, это будет правило назначения NAT (Dst NAT) для передачи этого трафика.

Для создания следующего правила (или для проверки существующих правил по умолчанию) откройте панель мониторинга администратора Barracuda NG, перейдите на вкладку конфигурации и щелкните "Набор правил" в разделе "Рабочая конфигурация". Откроется сетка "Основные правила", на которой будут показаны существующие активные и деактивированные правила брандмауэра. В правом верхнем углу этой сетки расположена небольшая зеленая кнопка "+". Нажмите ее, чтобы создать новое правило. (Примечание. Брандмауэр может быть "заблокирован" для внесения изменений. Если вы видите кнопку "Заблокировано" и не можете создать или изменить правила, нажмите эту кнопку, чтобы "разблокировать" набор правил и разрешить редактирование) Если вы хотите изменить существующее правило, выберите это правило, щелкните правой кнопкой мыши и выберите "Изменить правило".

Создайте новое правило и укажите имя правила, например WebTraffic. 

Значок правила назначения NAT выглядит следующим образом: ![Значок назначения NAT][2].

Само правило должно выглядеть примерно так:

![Правило брандмауэра][3]

Здесь трафик для всех входящих адресов, который попадает в брандмауэр, пытаясь обратиться к HTTP (порт 80 или порт 443 для HTTPS), будет отправлен на "Локальный IP-интерфейс DHCP1" брандмауэра и перенаправлен на веб-сервер с IP-адресом 10.0.1.5. Так как трафик направлялся на порт 80 и будет отправлен на порт 80 веб-сервера, изменять порт не нужно. Однако если бы наш веб-сервер слушал порт 8080, пришлось бы указать целевой список 10.0.1.5:8080, чтобы входящий порт 80 брандмауэра был преобразован во входящий порт 8080 на веб-сервере.

Для правила назначения из Интернета также нужно указать способ подключения. Наиболее подходящий способ — "Динамический SNAT". 

Несмотря на то что было создано только одно правило, важно правильно задать его приоритет. Если это новое правило находится в нижней части сетки брандмауэра (ниже правила BLOCKALL), то оно никогда не будет задействовано. Убедитесь, что созданное правило для веб-трафика находится выше правила BLOCKALL.

После создания правила его необходимо отправить в брандмауэр и затем активировать. Без этого правило не вступит в силу. Процесс отправки и активации описан в следующем разделе.

## <a name="rule-activation"></a>Активация правил
Если правило было добавлено в набор правил, этот набор правил необходимо передать в брандмауэр и активировать.

![Активация правила брандмауэра][4]

В правом верхнем углу клиента управления расположена группа кнопок. Нажмите кнопку "Отправить изменения", чтобы отправить измененные правила в брандмауэр, а затем нажмите кнопку "Активировать".

После активации набора правил брандмауэра подготовка среды этого примера завершена. При необходимости можно запустить сценарии для добавления приложения в эту среду, приведенные в разделе ссылок. Это позволит протестировать описанные ниже сценарии прохождения трафика.

> [!IMPORTANT]
> Важно понимать, что вы не обращаетесь напрямую к веб-серверу. Когда браузер запрашивает страницу HTTP из FrontEnd001.CloudApp.Net, конечная точка HTTP (порт 80) передает этот трафик брандмауэру, а не веб-серверу. Затем брандмауэр в соответствии с правилом, созданным выше, передает этот запрос на веб-сервер.
> 
> 

## <a name="traffic-scenarios"></a>Сценарии прохождения трафика
#### <a name="allowed-web-to-web-server-through-firewall"></a>(Разрешено) Запрос из Интернета к веб-серверу через брандмауэр
1. Интернет-пользователь запрашивает страницу HTTP из FrontEnd001.CloudApp.Net (облачная служба, открытая для доступа через Интернет)
2. Облачная служба передает трафик через открытую конечную точку на порте 80 на локальный интерфейс брандмауэра 10.0.1.4:80
3. Подсеть Frontend начинает обработку правила для входящего трафика.
   1. Правило 1 группы безопасности сети (DNS) не применяется — переход к следующему правилу.
   2. Правило 2 группы безопасности сети (RDP) не применяется, переход к следующему правилу
   3. Правило 3 группы безопасности сети (Подключение из Интернета к брандмауэру) применяется, трафик разрешается, обработка правил заканчивается
4. Трафик достигает внутреннего IP-адреса брандмауэра (10.0.1.4).
5. Правило перенаправления брандмауэра видит, что это трафик на порт 80, и перенаправляет его на веб-сервер IIS01
6. IIS01 прослушивает веб-трафик, получает этот запрос и начинает обработку запроса
7. IIS01 запрашивает сведения у сервера SQL Server на сервере AppVM01.
8. В подсети Frontend нет правил для обработки исходящего трафика — трафик разрешается.
9. Подсеть BackEnd начинает обработку правил для входящего трафика.
   1. Правило 1 группы безопасности сети (DNS) не применяется — переход к следующему правилу.
   2. Правило 2 группы безопасности сети (RDP) не применяется — переход к следующему правилу.
   3. Правило 3 группы безопасности сети (Подключение из Интернета к брандмауэру) не применяется, переход к следующему правилу
   4. Правило 4 группы безопасности сети (трафик от IIS01 к AppVM01) применяется — трафик разрешается, дальнейшая обработка правил прекращается.
10. Сервер AppVM01 получает SQL-запрос и отправляет ответ.
11. Поскольку правил для исходящего трафика в подсети BackEnd нет, отправка ответа разрешается.
12. Подсеть Frontend начинает обработку правила для входящего трафика.
    1. Так как в группе безопасности сети нет правил, которые могут применяться ко входящему трафику из подсети Backend в подсеть Frontend, все правила группы безопасности игнорируются.
    2. Системное правило по умолчанию, которое разрешает трафик между подсетями, разрешает прохождение этого трафика, поэтому трафик разрешается.
13. Сервер IIS получает ответ SQL, завершает HTTP-запрос и отправляет ответ запрашивающему
14. Так как это сеанс NAT от брандмауэра, местом назначения ответа (первоначально) является брандмауэр
15. Брандмауэр получает ответ от веб-сервера и перенаправляет его обратно пользователю Интернета
16. Так как для подсети FrontEnd нет исходящих правил, то отправка ответа разрешена, и интернет-пользователь получает запрашиваемую веб-страницу.

#### <a name="allowed-rdp-to-backend"></a>(Разрешено) Запрос сеанса RDP к подсети BackEnd
1. Администратор сервера в Интернете запрашивает сеанс удаленного рабочего стола (RDP) с AppVM01 на BackEnd001.CloudApp.Net:xxxxx, где xxxxx — номер порта, назначенный случайным образом для протокола удаленного рабочего стола AppVM01 (назначенный порт можно найти на портале Azure или определить с помощью PowerShell)
2. Поскольку брандмауэр прослушивает только адрес FrontEnd001.CloudApp.Net, он не включается в этот поток трафика
3. Подсеть BackEnd начинает обработку правила для входящего трафика:
   1. Правило 1 группы безопасности сети (DNS) не применяется — переход к следующему правилу.
   2. Правило 2 группы безопасности сети (RDP) применяется — трафик разрешается, дальнейшая обработка правил прекращается.
4. Правил для исходящего трафика нет, поэтому применяются правила по умолчанию и обратный трафик разрешается.
5. Начинается сеанс RDP.
6. Сервер AppVM01 запрашивает имя пользователя и пароль.

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>DNS-запрос веб-сервера к серверу DNS (разрешено) 
1. Веб-серверу IIS01 нужен веб-канал данных с сайта www.data.gov, но ему необходимо разрешить адрес.
2. В конфигурации виртуальной сети в качестве основного DNS-сервера указан сервер DNS01 (10.0.2.4 в подсети Backend). Веб-сервер IIS01 отправляет DNS-запрос серверу DNS01.
3. В подсети Frontend нет правил для обработки исходящего трафика — трафик разрешается.
4. Подсеть BackEnd начинает обработку входящего правила:
   1. Правило 1 группы безопасности сети (DNS) применяется — трафик разрешается, дальнейшая обработка правил прекращается.
5. DNS-сервер получает запрос.
6. Указанный адрес отсутствует в кэше DNS-сервера, поэтому он отправляет запрос к корневому DNS-серверу в Интернете.
7. В подсети BackEnd нет правил для исходящего трафика, поэтому трафик разрешается.
8. DNS-сервер в Интернете отправляет ответ. Так как этот сеанс инициирован внутри системы, ответ пропускается.
9. DNS-сервер кэширует ответ и отправляет ответ на исходный запрос обратно на сервер IIS01.
10. В подсети BackEnd нет правил для исходящего трафика, поэтому трафик разрешается.
11. Подсеть FrontEnd начинает обработку входящего правила:
    1. Так как в группе безопасности сети нет правил, которые могут применяться ко входящему трафику из подсети Backend в подсеть Frontend, все правила группы безопасности игнорируются.
    2. Системное правило по умолчанию, которое разрешает трафик между подсетями, разрешает прохождение этого трафика, поэтому трафик разрешается.
12. IIS01 получает ответ от DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Разрешено) Веб-сервер обращается к файлу на AppVM01
1. IIS01 запрашивает файл на AppVM01
2. В подсети Frontend нет правил для обработки исходящего трафика — трафик разрешается.
3. Подсеть BackEnd начинает обработку правил для входящего трафика.
   1. Правило 1 группы безопасности сети (DNS) не применяется — переход к следующему правилу.
   2. Правило 2 группы безопасности сети (RDP) не применяется — переход к следующему правилу.
   3. Правило 3 группы безопасности сети (Подключение из Интернета к брандмауэру) не применяется, переход к следующему правилу
   4. Правило 4 группы безопасности сети (трафик от IIS01 к AppVM01) применяется — трафик разрешается, дальнейшая обработка правил прекращается.
4. Сервер AppVM01 получает запрос и отправляет ответ в виде файла (предполагается, что доступ авторизован).
5. Поскольку правил для исходящего трафика в подсети BackEnd нет, отправка ответа разрешается.
6. Подсеть Frontend начинает обработку правила для входящего трафика.
   1. Так как в группе безопасности сети нет правил, которые могут применяться ко входящему трафику из подсети Backend в подсеть Frontend, все правила группы безопасности игнорируются.
   2. Системное правило по умолчанию, которое разрешает трафик между подсетями, разрешает прохождение этого трафика, поэтому трафик разрешается.
7. Сервер IIS получает запрос

#### <a name="denied-web-direct-to-web-server"></a>(Запрещено) Запрос из Интернета напрямую к веб-серверу
Так как веб-сервер, IIS01 и брандмауэр находятся в одной и той же облачной службе, они имеют один и тот же общедоступный IP-адрес, открытый для доступа через Интернет. Поэтому весь трафик HTTP будет направлен брандмауэру. Хотя запрос был бы успешно обработан, отправить запрос напрямую на веб-сервер нельзя — сначала он должен пройти через брандмауэр. См. первый сценарий в этом разделе для потока трафика.

#### <a name="denied-web-to-backend-server"></a>(Запрещено) Запрос из Интернета к серверу BackEnd
1. Интернет-пользователь пытается получить доступ к файлу на AppVM01 через службу BackEnd001.CloudApp.Net
2. Так как для файлового ресурса конечные точки не открыты, этот запрос не будет передан облачной службе и не поступит на сервер.
3. Если бы конечные точки по какой-либо причине были открыты, правило 5 группы безопасности сети (Запрос из Интернета к виртуальной сети) заблокировало бы этот трафик

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Запрещено) DNS-запрос из Интернета к серверу DNS
1. Интернет-пользователь пытается выполнить запрос внутренней записи DNS на DNS01 через службу BackEnd001.CloudApp.Net
2. Так как для DNS-сервера не открыты конечные точки, этот запрос не будет передан облачной службе и не достигнет сервера.
3. Если бы конечные точки по какой-либо причине были открыты, правило 5 группы безопасности сети (Запрос из Интернета к виртуальной сети) заблокировало бы этот трафик (Обратите внимание, что правило 1 (DNS) в этом случае не было бы применено по двум причинам. Во-первых, адрес источника находится в Интернете, а это правило применяется только к адресам, источником которых является локальная виртуальная сеть. Во-вторых, это разрешающее правило, поэтому оно никогда не будет запрещать трафик.)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Запрещено) Запрос из Интернета к SQL через брандмауэр
1. Интернет-пользователь данные SQL из FrontEnd001.CloudApp.Net (облачная служба, открытая для доступа через Интернет)
2. Так как для данных SQL не открыты конечные точки, этот запрос не будет передан облачной службе и не достигнет брандмауэра.
3. Если конечные точки будут открыты по какой-то причине, подсеть FrontEnd начнет обработку правил для входящего трафика.
   1. Правило 1 группы безопасности сети (DNS) не применяется — переход к следующему правилу.
   2. Правило 2 группы безопасности сети (RDP) не применяется — переход к следующему правилу.
   3. Правило 2 группы безопасности сети (Подключение из Интернета к брандмауэру) применяется, трафик разрешается, обработка правил заканчивается
4. Трафик достигает внутреннего IP-адреса брандмауэра (10.0.1.4)
5. Брандмауэр не имеет правил перенаправления для SQL и отклоняет трафик

## <a name="conclusion"></a>Заключение
Это относительно простой способ защиты приложения с помощью брандмауэра и изоляции внутренней подсети от входящего трафика.

Дополнительные примеры и общие сведения о периметре безопасности сети можно найти [здесь][HOME].

## <a name="references"></a>Ссылки
### <a name="main-script-and-network-config"></a>Основной сценарий и конфигурация сети
Сохраните полный сценарий в файл сценария PowerShell. Сохраните конфигурацию сети в файле с именем NetworkConf2.xml.
При необходимости измените определенные пользователем переменные. Запустите сценарий, а затем следуйте инструкциям по настройке правил брандмауэра, приведенным выше.

#### <a name="full-script"></a>Полный сценарий
Используя определенные пользователем переменные, этот сценарий выполнит следующие действия.

1. Подключение к подписке Azure
2. Создание новой учетной записи хранения
3. Создаст виртуальную сеть и две подсети, которые указаны в файле конфигурации сети.
4. Создаст четыре виртуальные машины под управлением Windows Server.
5. Настроит группу безопасности сети:
   * создаст группу безопасности сети;
   * добавит в нее правила;
   * привяжет группу безопасности сети к соответствующим подсетям.

Этот сценарий PowerShell должен запускаться локально на ПК или сервере, подключенном к Интернету.

> [!IMPORTANT]
> При запуске этого сценария могут выдаваться предупреждения и другие информационные сообщения PowerShell. Обращать внимание следует только на сообщения об ошибках, выделенные красным цветом.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
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
       myFirewall - 10.0.1.4
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

    # User Defined Global Variables
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
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
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
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
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
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
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
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>Файл конфигурации сети
Измените в этом XML-файле расположение и сохраните его. Затем добавьте ссылку на файл в переменную $NetworkConfigFile в приведенном выше сценарии.

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

#### <a name="sample-application-scripts"></a>Сценарии примера приложения
Пример приложения для этого и других примеров сети периметра можно скачать по [этой ссылке][SampleApp].

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Входящая сеть периметра с группой безопасности сети"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Значок NAT назначения"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Правило брандмауэра"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Активация правила брандмауэра"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
