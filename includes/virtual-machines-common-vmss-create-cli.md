Масштабируемые наборы виртуальных машин позволяют управлять несколькими виртуальными машинами как единым набором. Если не вдаваться в подробности, масштабируемые наборы имеют следующие преимущества и недостатки:

Преимущества:

1. обеспечение высокой доступности; Каждый масштабируемый набор помещает свои виртуальные машины в группу доступности с 5 доменами сбоя (FD) и 5 доменами обновления (UD) для обеспечения доступности (дополнительные сведения о доменах сбоя и обновления см. в статье [Доступность виртуальных машин](../articles/virtual-machines/virtual-machines-linux-manage-availability.md)).
2. Простая интеграция с балансировщиком нагрузки Azure и шлюзом приложений.
3. Простая интеграция с функцией автоматического масштабирования Azure.
4. Упрощение развертывания, удаления виртуальных машин и управления ими.
5. Поддержка распространенных выпусков Windows и Linux, а также пользовательских образов.

Недостатки:

1. Невозможность подключения дисков данных к экземплярам виртуальных машин в масштабируемом наборе. Вместо этого следует использовать хранилище BLOB-объектов, службу файлов Azure, таблицы Azure или другое решение для хранения.

## Быстрое создание с помощью Azure CLI
Если это еще не сделано, можно подключить [бесплатную пробную подписку Azure](https://azure.microsoft.com/pricing/free-trial/) и [Azure CLI](../articles/xplat-cli-install.md) [к вашей учетной записи Azure](../articles/xplat-cli-connect.md). После этого можно выполнить следующие команды, чтобы быстро создать набор масштабирования виртуальных машин:

```bash
# make sure we are in resource manager mode 
azure config mode arm

# quick-create a VM scale set
#
# generic syntax:
# azure vmss quick-create -n VMSS-NAME -g RESOURCE-GROUP-NAME -l LOCATION -u USERNAME -p PASSWORD -C INSTANCE-COUNT -Q IMAGE-URN
#
# example:
azure vmss quick-create -n negatvmss -g negatvmssrg -l westus -u negat -p P4$$w0rd -C 5 -Q Canonical:UbuntuServer:14.04.4-LTS:latest
```

Если вы хотите настроить расположение или URN образа, воспользуйтесь командами `azure location list` и `azure vm image {list-publishers|list-offers|list-skus|list|show}`.

После возвращения данных этой командой создается масштабируемый набор. Этот масштабируемый набор будет иметь балансировщик нагрузки с правилами NAT, сопоставляющими порт 50 000+i балансировщика нагрузки с портом 22 на виртуальной машине с индексом i. Таким образом, после выяснения полного доменного имени балансировщика нагрузки мы сможем получить доступ к виртуальным машинам по протоколу SSH:

```bash
# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g nsgvmsrg | grep nsgvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is associated with it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LB-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g nsgvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g nsgvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can ssh on port 50,000+i to ssh into VM i (where i is 0-indexed)
#
# example to ssh into VM "0":
ssh -p 50000 $FQDN
```

## Дальнейшие действия
Общие сведения см. на [основной целевой странице для масштабируемых наборов виртуальных машин](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Документацию см. на [основной странице документации для масштабируемых наборов виртуальных машин](https://azure.microsoft.com/documentation/services/virtual-machines-scale-sets/).

Чтобы найти примеры шаблонов Azure Resource Manager с использованием масштабируемых наборов, выполните поиск строки "vmss" в [репозитории github с шаблонами быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates).

<!---HONumber=AcomDC_0406_2016-->