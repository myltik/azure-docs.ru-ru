---
title: "Вопросы и ответы об Azure DNS | Документация Майкрософт"
description: "Часто задаваемые вопросы об Azure DNS."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: jonatul
ms.translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f365574a12047f6952209dc3883af32a2e9ecd1e
ms.contentlocale: ru-ru
ms.lasthandoff: 04/22/2017

---

# <a name="azure-dns-faq"></a>Вопросы и ответы об Azure DNS

## <a name="about-azure-dns"></a>Сведения об Azure DNS

### <a name="what-is-azure-dns"></a>Что такое Azure DNS?

Служба доменных имен, или DNS, отвечает за преобразование (или разрешение) имени веб-сайта или службы в IP-адрес. Azure DNS является службой размещения для доменов DNS, предоставляющей разрешение имен с помощью инфраструктуры Microsoft Azure. Размещая домены в Azure, вы можете управлять своими записями DNS с помощью тех же учетных данных, API и инструментов и оплачивать использование, как и другие службы Azure.

Домены DNS в Azure DNS размещаются в глобальной сети DNS-серверов Azure. Мы используем адресацию любому устройству сети, чтобы на каждый запрос DNS отвечал ближайший доступный DNS-сервер. Это обеспечивает высокую производительность и высокий уровень доступности для вашего домена.

Служба Azure DNS работает на основе Azure Resource Manager. Таким образом она использует преимущества Resource Manager, такие как управление доступом на основе ролей, журналы аудита и блокировка ресурсов. Доменами и записями можно управлять с помощью портала Azure, командлетов Azure PowerShell и кроссплатформенного Azure CLI. Приложения, для которых необходимо автоматическое управление DNS, можно интегрировать со службой с помощью REST API и пакетов SDK.

### <a name="how-much-does-azure-dns-cost"></a>Сколько стоит использование Azure DNS?

Модель выставления счетов за использование Azure DNS основана на количестве зон DNS, размещенных в Azure DNS, и количестве полученных запросов DNS. На основе использования предоставляются скидки.

Дополнительные сведения см. на [странице цен на Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Что такое соглашение об уровне обслуживания для Azure DNS?

Мы гарантируем, что в 99,99 % случаев на допустимые запросы DNS поступит ответ хотя бы от одного DNS-сервера Azure DNS.

Дополнительные сведения см. на [странице соглашения об уровне обслуживания для Azure DNS](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Что такое "зона DNS"? Это то же самое, что и домен DNS? 

Домен — это уникальное имя в службе доменных имен, например contoso.com.

Зона DNS используется для размещения DNS-записей определенного домена. Например, домен contoso.com может содержать несколько записей DNS, включая mail.contoso.com (для почтового сервера) и www.contoso.com (для веб-сайта). Он будет размещен в зоне DNS contoso.com.

Доменное имя — это *просто имя*, а зона DNS — это ресурс данных, содержащий записи DNS для доменного имени. Azure DNS позволяет размещать зону DNS и управлять записями DNS для домена в Azure. Также предоставляются DNS-серверы для ответа на запросы DNS из Интернета.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Нужно ли приобретать доменное имя DNS для использования Azure DNS? 

Не обязательно.

Вам не потребуется приобретать домен для размещения зоны DNS в Azure DNS. Вы можете создать зону DNS в любой момент, не владея доменным именем. Запросы DNS для данной зоны будут разрешаться только в том случае, если они направлены на DNS-серверы Azure, назначенные этой зоне.

Нужно будет приобрести доменное имя, если требуется привязать зону DNS к глобальной иерархии DNS. Это позволит запросам DNS из любой точки мира находить вашу зону DNS и получать в ответ записи DNS.

## <a name="azure-dns-features"></a>Возможности Azure DNS

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS поддерживает маршрутизацию трафика на основе DNS и отработку отказа конечных точек?

Маршрутизацию трафика на основе DNS и отработку отказа конечных точек обеспечивает диспетчер трафика Azure. Это отдельная служба Azure, которая может использоваться вместе с Azure DNS. Дополнительные сведения см. в разделе [Обзор диспетчера трафика](../traffic-manager/traffic-manager-overview.md).

Azure DNS поддерживает только размещение "статических" доменов DNS, в которых на каждый запрос DNS для определенной записи DNS всегда передается одинаковый ответ DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>Поддерживает ли Azure DNS регистрацию доменных имен?

№ Azure DNS в настоящее время не поддерживает приобретение доменных имен. Если вы хотите приобрести домены, вам потребуется использовать регистратор доменных имен стороннего поставщика. Регистратор обычно взимает небольшую годовую плату. Затем вы сможете разместить домены в Azure DNS, чтобы управлять записями DNS. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

Это функция, которую мы отслеживаем в очереди невыполненной работы. Вы можете воспользоваться нашим сайтом для отзывов и предложений, чтобы [поддержать эту функцию](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-private-domains"></a>Поддерживает ли Azure DNS "частные" домены?

Нет. В настоящее время Azure DNS поддерживает только домены для Интернета.

Это функция, которую мы отслеживаем в очереди невыполненной работы. Вы можете воспользоваться нашим сайтом для отзывов и предложений, чтобы [поддержать эту функцию](https://feedback.azure.com/forums/217313-networking/suggestions/10737696-enable-split-dns-for-providing-both-public-and-int).

Сведения о внутренних параметрах DNS в Azure см. в разделе [Разрешение имен для ВМ и экземпляров ролей](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="does-azure-dns-support-dnssec"></a>Поддерживает ли Azure DNS набор DNSSEC?

Нет. Сейчас Azure DNS не поддерживает DNSSEC.

Это функция, которую мы отслеживаем в очереди невыполненной работы. Вы можете воспользоваться нашим сайтом для отзывов и предложений, чтобы [поддержать эту функцию](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Поддерживает ли Azure DNS передачу зон (AXFR-IXFR)?

№ Сейчас Azure DNS не поддерживает передачу зон. Зоны DNS можно [импортировать в Azure DNS с помощью Azure CLI](dns-import-export.md). Затем записями DNS можно управлять с помощью [портала управления Azure DNS](dns-operations-recordsets-portal.md), нашего [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [пакета SDK](dns-sdk.md), [командлетов PowerShell](dns-operations-recordsets.md) или [программы командной строки](dns-operations-recordsets-cli.md).

Это функция, которую мы отслеживаем в очереди невыполненной работы. Вы можете воспользоваться нашим сайтом для отзывов и предложений, чтобы [поддержать эту функцию](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Поддерживает ли Azure DNS перенаправление URL-адресов?

№ Службы перенаправления URL-адресов не являются службами DNS. Они работают на уровне протокола HTTP, а не на уровне DNS. Некоторые поставщики DNS добавляют службу перенаправления URL-адресов в свои предложения. На данный момент эта функция не поддерживается Azure DNS.

Она отслеживается в очереди невыполненной работы. Вы можете воспользоваться нашим сайтом для отзывов и предложений, чтобы [поддержать эту функцию](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

## <a name="using-azure-dns"></a>Использование Azure DNS

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Можно ли реализовать совместное размещение домена с помощью Azure DNS и другого поставщика DNS?

Да. Azure DNS поддерживает совместное размещение доменов с другими службами DNS.

Для этого необходимо изменить записи NS для домена (которые определяют, какие поставщики получают запросы DNS для домена), указав серверы доменных имен обоих поставщиков. Эти записи NS нужно изменить в 3 местах: в Azure DNS, в другом поставщике и в родительской зоне (обычно для настройки используется регистратор доменных имен). Дополнительные сведения о делегировании DNS см. в разделе [Делегирование зон DNS с помощью Azure DNS](dns-domain-delegation.md).

Кроме того, необходимо обеспечить синхронизацию записей DNS для домена в обоих поставщиках DNS. Сейчас Azure DNS не поддерживает передачу зон DNS. Необходимо синхронизировать записи DNS с помощью [портала управления Azure DNS](dns-operations-recordsets-portal.md), нашего [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [пакета SDK](dns-sdk.md), [командлетов PowerShell](dns-operations-recordsets.md) или [программы командной строки](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-4-azure-dns-name-servers"></a>Мне следует делегировать мой домен на все четыре DNS-сервера Azure?

Да. Azure DNS назначает четыре сервера доменных имен каждой зоне DNS для изоляции сбоев и повышения устойчивости. Для соблюдения соглашения об уровне обслуживания Azure DNS необходимо делегировать домен на все четыре сервера доменных имен.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Как ограничивается использование Azure DNS?

При использовании Azure DNS применяются приведенные ниже ограничения по умолчанию.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Можно ли перемещать зону Azure DNS между группами ресурсов или подписками?

Да. Зоны Azure DNS можно перемещать между группами ресурсов или подписками.

Это не влияет на запросы DNS. Серверы доменных имен, назначенные зоне, остаются прежними, и запросы DNS обрабатываются в обычном режиме.

Дополнительные сведения и инструкции по перемещению зон DNS приведены в разделе [Перемещение ресурсов в новую группу ресурсов или подписку](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Сколько времени требуется, чтобы изменения в DNS вступили в силу?

Как правило, новые зоны DNS и записи DNS быстро появляются на DNS-серверах Azure, всего через несколько секунд.

Изменения существующих записей DNS могут дольше вступать в силу на DNS-серверах Azure, но все равно на это потребуется не больше 60 секунд. В этом случае кэширование DNS за пределами Azure DNS (DNS-клиентами и рекурсивными сопоставителями DNS) также может повлиять на время, необходимое для применения изменений DNS. Длительностью этого кэширования можно управлять с помощью свойства срока жизни (TTL) для каждого набора записей.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Как защитить мои зоны DNS от ненамеренного удаления?

Для управления Azure DNS используется Azure Resource Manager, что дает преимущества функций управления доступом Azure Resource Manager. Управление доступом на основе ролей можно использовать для контроля пользователей, которые имеют доступ на чтение или запись к зонам DNS и наборам записей. Можно блокировать ресурсы, чтобы предотвратить ненамеренное изменение или удаление зон DNS и наборов записей.

Дополнительные сведения см. в разделе [Как защитить зоны и записи DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Как настроить записи SPF в Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Как настроить международное доменное имя (IDN) в Azure DNS?

Международные доменные имена (IDN) создаются путем кодирования DNS-имен в формат [Punycode](https://en.wikipedia.org/wiki/Punycode). Запросы DNS выполняются с использованием этих имен в формате Punycode.

Международные доменные имена (IDN) в Azure DNS можно настроить, предварительно преобразовав имя зоны или имя набора записей в формат Punycode. Azure DNS в настоящее время не поддерживает встроенное преобразование в Punycode.

## <a name="next-steps"></a>Дальнейшие действия

[Дополнительные сведения об Azure DNS](dns-overview.md)
<br>
[Дополнительные сведения о зонах и записях DNS](dns-zones-records.md)
<br>
[Приступая к работе с Azure DNS](dns-getstarted-portal.md)


