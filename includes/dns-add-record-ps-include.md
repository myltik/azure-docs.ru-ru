### Создание набора записей типа AAAA с одной записью

	$rs = New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Создание набора записей типа CNAME с одной записью

	$rs = New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Создание набора записей типа MX с одной записью

Чтобы создать запись MX на вершине зоны (например, "contoso.com"), в этом примере мы используем имя набора записей "@". Так обычно и делается при создании записей MX.

	$rs = New-AzureRmDnsRecordSet -Name "@" -RecordType MX -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Создание набора записей типа NS с одной записью

	$rs = New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Создание набора записей типа SRV с одной записью

Если вы создаете запись SRV в корне зоны, в имени записи можно указать *\_service* и *\_protocol*. Нет необходимости включать в имя записи символы ".@".

	$rs = New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Создание набора записей типа TXT с одной записью

	$rs = New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	Set-AzureRmDnsRecordSet -RecordSet $rs
