
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Часто задаваемые вопросы: обратный DNS для IP-адреса, назначенного Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>Каковы затраты на обратные записи DNS?

Они бесплатны!  Никакие дополнительные затраты на обратные записи или запросы DNS не требуются.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Будут ли мои обратные записи DNS разрешаться из Интернета?

Да. После того, как вы задали свойство обратного DNS для облачной службы, Azure управляет всеми операциями делегирования DNS и зонами DNS, необходимыми для разрешения этой обратной записи DNS для всех пользователей Интернета.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-cloud-services"></a>Будет ли создана обратная запись DNS по умолчанию для моих облачных служб?

Нет. Обратный DNS является включаемой функцией. Если вы не захотите настроить обратные записи DNS, они не будут созданы по умолчанию.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Каков формат полного доменного имени (FQDN)?

Полные доменные имена указываются в прямом порядке и должны завершаться точкой (например, app1.contoso.com).

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>Что произойдет, если при проверке указанной мной обратной записи DNS произойдет ошибка?

Если обратный DNS не пройдет проверку, операция службы управления завершится ошибкой. В этом случае исправьте значение обратного DNS, как указано, и повторите попытку.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>Можно ли управлять обратным DNS для веб-сайта Azure?

Для веб-сайтов Azure обратный DNS не поддерживается. Обратный DNS поддерживается для ролей PaaS и виртуальных машин IaaS Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-cloud-service"></a>Можно ли настроить несколько обратных записей DNS для моей облачной службы?

Нет. Azure поддерживает одну обратную запись DNS для каждой облачной службы Azure. Тем не менее, у каждой облачной службы Azure может быть собственная обратная запись DNS.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Можно ли отправлять электронные сообщения во внешние домены из служб вычислений Azure?

Нет. [Службы вычислений Azure не поддерживают отправку электронных сообщений на внешние домены](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
