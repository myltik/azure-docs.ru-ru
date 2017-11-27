[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Можно ли использовать корневой ЦС собственной внутренней системы PKI для подключения "точка — сеть"?

Да. Раньше поддерживались только самозаверяющие корневые сертификаты. Вы по-прежнему можете загружать до 20 корневых сертификатов.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Какие инструменты можно использовать для создания сертификатов?

Можно использовать решение Enterprise PKI (внутренняя инфраструктура открытых ключей), Azure PowerShell, MakeCert и OpenSSL.

### <a name="certsettings"></a>Есть инструкции по выбору параметров сертификата?

* **Внутренняя инфраструктура открытых ключей и решение Enterprise PKI:** см. инструкции по [созданию сертификатов](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** см. инструкции по работе с [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md).

* **MakeCert:** см. инструкции по работе с [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md).

* **OpenSSL:** 

    * При экспорте сертификатов преобразуйте корневой сертификат в кодировку Base64.

    * Для сертификата клиента:

      * При создании закрытого ключа укажите длину 4096.
      * При создании сертификата для параметра *-extensions* укажите значение *usr_cert*.