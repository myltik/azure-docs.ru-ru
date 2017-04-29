
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>Часто задаваемые вопросы: размещение зоны ARPA в Azure DNS

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Можно ли разместить зоны ARPA для блоков IP-адресов, назначенных поставщиком услуг Интернета, в DNS Azure?

Да. Размещение зоны ARPA для диапазонов IP-адресов в Azure DNS не поддерживается полностью.

[Создайте зону в Azure DNS](../articles/dns/dns-getstarted-create-dnszone.md), затем свяжитесь с работать с поставщиком услуг Интернета, чтобы [делегировать зону](../articles/dns/dns-domain-delegation.md).  Вы можете управлять записями типа PTR для каждого обратного просмотра точно так же, как и другими записями другого типа.

Вы также можете [импортировать существующую зону обратного просмотра с помощью интерфейса командной строки Azure](../articles/dns/dns-import-export.md).

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>Сколько стоит размещение моей зоны ARPA?

Размещение зоны ARPA в Azure DNS для блокировки назначенного поставщиком услуг Интернета IP-адреса оплачивается по [стандартным тарифам Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Могу ли я разместить в Azure DNS зоны ARPA для адресов IPv4 и IPv6?

Да.
