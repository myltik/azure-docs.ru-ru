В следующей таблице показаны привязки, которые поддерживаются в двух основных версиях среды выполнения службы "Функции Azure".

| type | 1.x | 2.x | Триггер | Входные данные | Выходные данные |  
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Хранилище BLOB-объектов](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔<sup>1</sup>|✔|✔|✔|  
| [База данных Cosmos](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|  
| [Сетка событий](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |  
| [Концентраторы событий](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|  
| [Внешний файл](../articles/azure-functions/functions-bindings-external-file.md)<sup>2</sup>    |✔|| |✔|✔|  
| [Внешняя таблица](../articles/azure-functions/functions-bindings-external-table.md)<sup>2</sup>  |✔|| |✔|✔|  
| [HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔<sup>1</sup>|✔| |✔|
| [Таблицы Excel<br/>Microsoft Graph](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Файлы OneDrive<br/>Microsoft Graph](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Электронная почта Outlook<br/>Microsoft Graph](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [События<br/>Microsoft Graph](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Маркеры безопасности<br/>Microsoft Graph](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Мобильные приложения](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔|✔| |✔|✔|  
| [Центры уведомлений](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Хранилище очередей](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔<sup>1</sup>|✔| |✔|  
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Служебная шина](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|  
| [Хранилище таблиц](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔<sup>1</sup>| |✔|✔|  
| [Таймер](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|
| [Объекты Webhook](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔||✔| |✔|
  
<sup>1</sup> Все привязки, кроме HTTP, таймера и службы хранилища Azure, должны быть зарегистрированы в версиях 2.x. Ознакомьтесь с разделом [Регистрация расширений привязки](../articles/azure-functions/functions-triggers-bindings.md#register-binding-extensions).

<sup>2</sup> Экспериментальная функция &mdash; не поддерживается и может быть удалена в будущем.
