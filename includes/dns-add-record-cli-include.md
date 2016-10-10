#### Создание набора записей типа A с одной записью

Чтобы создать набор записей, используйте `azure network dns record-set create`. Укажите группу ресурсов, имя зоны, относительное имя набора записей, тип записи и срок жизни.

	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

После создания набора записей A добавьте в набор записей IPv4-адрес с помощью команды `azure network dns record-set add-record`.

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### Создание набора записей типа AAAA с одной записью

	azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### Создание набора записей типа CNAME с одной записью

В записях CNAME можно использовать только одно строковое значение.


	azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

	azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### Создание набора записей типа MX с одной записью

Чтобы создать запись MX на вершине зоны (в данном случае "contoso.com"), в этом примере мы используем имя набора записей "@". Так обычно и делается при создании записей MX.

	azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

	azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### Создание набора записей типа NS с одной записью

	azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

	azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### Создание набора записей типа PTR с одной записью  
В этом случае "my-arpa-zone.com" представляет зону ARPA вашего диапазона IP-адресов. Каждая запись PTR в этой зоне соответствует IP-адресу в этом диапазоне.

	azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"   

#### Создание набора записей типа SRV с одной записью

Если вы создаете запись SRV в корне зоны, в имени записи можно указать \_service и \_protocol. Нет необходимости включать в имя записи символ "@".


	azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### Создание набора записей типа TXT с одной записью

	azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"

<!---HONumber=AcomDC_0928_2016-->