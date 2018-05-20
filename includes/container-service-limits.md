| Ресурс | Ограничение по умолчанию |
| --- | :--- |
| Максимальное число узлов в кластере | 100 |
| Максимальное количество групп pod на один узел ([базовое сетевое взаимодействие с Kubenet][basic-networking]) | 110 |
| Максимальное количество групп pod на один узел ([расширенное сетевое взаимодействие с Azure CNI][advanced-networking]) | 30 |
| Максимальное число кластеров в подписке | 20<sup>1</sup> |

<sup>1</sup> Отправьте [запрос в службу поддержки Azure][azure-support] на увеличение лимита.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest