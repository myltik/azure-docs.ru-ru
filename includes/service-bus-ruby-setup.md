## <a name="create-a-ruby-application"></a>Создание приложения Ruby
Инструкции см. в разделе [Создание приложения Ruby в Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Настройка приложения для использования служебной шины
Для использования служебной шины скачайте и используйте пакет Azure Ruby, который содержит набор библиотек, взаимодействующих со службами REST хранилища.

### <a name="use-rubygems-to-obtain-the-package"></a>Использование RubyGems для получения пакета
1. Используйте интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix).
2. Введите "gem install azure" в окне командной строки, чтобы установить пакеты и зависимости.

### <a name="import-the-package"></a>Импорт пакета
Используйте свой любимый текстовый редактор, чтобы добавить следующий код в начало файла Ruby, где планируется использовать хранилище.

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Настройка подключения к Service Bus
Чтобы задать значения пространства имен, имя ключа, ключ, подписавшего пользователя и узел, используйте следующий код:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Задайте для пространства имен только созданное значение, а не весь URL-адрес. Например, используйте **yourexamplenamespace**, а не yourexamplenamespace.servicebus.windows.net.
