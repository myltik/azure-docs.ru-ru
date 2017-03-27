## <a name="prerequisites"></a>Предварительные требования

Если это еще не сделано, можно получить [бесплатную пробную подписку Azure](https://azure.microsoft.com/pricing/free-trial/) и подключить [Azure CLI 1.0](../articles/cli-install-nodejs.md) [к учетной записи Azure](../articles/xplat-cli-connect.md). Убедитесь, что Azure CLI работает в режиме Resource Manager следующим образом:

```azurecli
azure config mode arm
```

## <a name="create-the-scale-set"></a>Создание масштабируемого набора

Теперь создайте масштабируемый набор с помощью команды `azure vmss quick-create`. В следующем примере создается масштабируемый набор Linux `myVMSS` с пятью экземплярами виртуальных машин в группе ресурсов `myResourceGroup`.

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q Canonical:UbuntuServer:16.04-LTS:latest
```

В следующем примере создается масштабируемый набор Windows с такой же конфигурацией:

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest
```

Если вы хотите настроить расположение или URN образа, ознакомьтесь с командами `azure location list` и `azure vm image {list-publishers|list-offers|list-skus|list|show}`.

Эта команда возвращается после развертывания масштабируемого набора. На этом этапе масштабируемый набор будет иметь подсистему балансировки нагрузки с правилами NAT, сопоставляющими порт 50 000 + i подсистемы балансировки нагрузки с портом 22 на виртуальной машине с номером i. Таким образом, выяснив полное доменное имя подсистемы балансировки нагрузки, мы сможем подключиться к виртуальным машинам по протоколу SSH.

```bash
# (if you decide to run this as a script, please invoke using bash)

# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g negatvmssrg | grep negatvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is 
# associated to it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LOAD-BALANCER-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g negatvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g negatvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can use ssh on port 50,000+i to connect to VM i (where i is 0-indexed)
#
# example to connct via ssh into VM "0":
ssh -p 50000 negat@$FQDN
```