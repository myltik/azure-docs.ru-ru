<properties 
   pageTitle="Как управлять обратными записями DNS для служб с помощью Azure CLI в Resource Manager | Microsoft Azure"
   description="Как управлять обратными записями DNS или записями типа PTR для служб Azure с помощью Azure CLI в Resource Manager."
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/09/2016"
   ms.author="s-malone" />

# Как управлять обратными записями DNS для служб с помощью Azure CLI

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]<BR>[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](dns-reverse-dns-record-operations-classic-ps.md).

## Проверка обратных записей DNS 
Чтобы гарантировать, что третья сторона не сможет создать сопоставление обратных записей DNS с вашими доменами DNS, Azure позволяет создавать только обратные записи DNS, для которых выполняется одно из следующих условий:

- ReverseFqdn совпадает с Fqdn ресурса общедоступного IP-адреса, для которого он был указан, либо с Fqdn любого общедоступного IP-адреса в той же подписке, т. е. ReverseFqdn — contosoapp1.northus.cloudapp.azure.com.

- При переадресации значение ReverseFqdn разрешается в имя или IP-адрес общедоступного IP-адреса, для которого оно было указано, либо в Fqdn или IP-адрес любого общедоступного IP-адреса в той же подписке, т. е. ReverseFqdn — app1.contoso.net, представляющий собой псевдоним CName для contosoapp1.northus.cloudapp.azure.com.

Проверки выполняются только в том случае, если задается или меняется свойство обратного DNS для общедоступного IP-адреса. Периодическая повторная проверка не выполняется.

## Добавление обратного DNS для существующих общедоступных IP-адресов
Вы можете добавить обратный DNS для существующего общедоступного IP-адреса с помощью команды azure network public-ip set.

	azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

Если вы хотите добавить обратный DNS для существующего общедоступного IP-адреса, у которого еще нет DNS-имени, необходимо также указать DNS-имя. Для этого можно использовать команду azure network public-ip set.

	azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## Создание общедоступного IP-адреса с обратным DNS
Вы можете добавить новый общедоступный IP-адрес с заданным свойством обратного DNS с помощью команды azure network public-ip create.

	azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
 
## Просмотр обратного DNS существующих общедоступных IP-адресов
Вы можете просмотреть значение, настроенное для существующего общедоступного IP-адреса, с помощью команды azure network public-ip show.

	azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS 

## Удаление обратного DNS для существующих общедоступных IP-адресов
Вы можете удалить свойство обратного DNS для существующего общедоступного IP-адреса с помощью команды azure network public-ip set. Для этого нужно указать пустое значение свойства ReverseFqdn.

	azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “” 

[AZURE.INCLUDE [ЧАСТО ЗАДАВАЕМЫЕ ВОПРОСЫ](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]

<!---HONumber=AcomDC_0316_2016-->