### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Создание набора записей типа AAAA с одной записью
    $rs = New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="create-a-cname-record-set-with-a-single-record"></a>Создание набора записей типа CNAME с одной записью
    $rs = New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="create-an-mx-record-set-with-a-single-record"></a>Создание набора записей типа MX с одной записью
Чтобы создать запись MX на вершине зоны (в данном случае "contoso.com"), в этом примере мы используем имя набора записей "@". Так обычно и делается при создании записей MX.

    $rs = New-AzureRmDnsRecordSet -Name "@" -RecordType MX -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="create-an-ns-record-set-with-a-single-record"></a>Создание набора записей типа NS с одной записью
    $rs = New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Создание набора записей типа PTR с одной записью
В этом случае "my-arpa-zone.com" представляет зону ARPA вашего диапазона IP-адресов.  Каждая запись PTR в этой зоне соответствует IP-адресу в этом диапазоне.  

    $rs = New-AzureRmDnsRecordSet -Name "10" -RecordType PTR -Ttl 3600 -ZoneName my-arpa-zone.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ptrdname "myservice.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="create-an-srv-record-set-with-a-single-record"></a>Создание набора записей типа SRV с одной записью
Если вы создаете запись SRV в корне зоны, просто укажите в имени записи *_service* и *_protocol*. Нет необходимости включать в имя записи символ include"@".

    $rs = New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="create-a-txt-record-set-with-a-single-record"></a>Создание набора записей типа TXT с одной записью
    $rs = New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
    Set-AzureRmDnsRecordSet -RecordSet $rs


<!--HONumber=Oct16_HO2-->


