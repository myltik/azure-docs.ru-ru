---
title: "Настройка SSL-соединений для безопасного подключения к базе данных Azure для MySQL | Документация Майкрософт"
description: "Инструкции по настройке базы данных Azure для MySQL и связанных приложений для правильного использования SSL-соединений."
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 07/28/2017
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 77e1b6266a2cf47949fa06358ec003f6b6b38065
ms.contentlocale: ru-ru
ms.lasthandoff: 08/09/2017

---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Настройка SSL-подключений в приложении для безопасного подключения к базе данных Azure для MySQL
База данных Azure для MySQL поддерживает подключение сервера базы данных Azure для MySQL к клиентским приложениям с помощью протокола SSL (Secure Sockets Layer). Применение SSL-соединений между сервером базы данных и клиентскими приложениями обеспечивает защиту от атак "злоумышленник в середине" за счет шифрования потока данных между сервером и приложением.

## <a name="step-1-obtain-ssl-certificate"></a>Шаг 1. Получение SSL-сертификата
Скачайте сертификат, который необходим для взаимодействия с сервером базы данных Azure для MySQL по протоколу SSL ([https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)), и сохраните файл сертификата на локальном диске (в этом руководстве мы использовали расположение C:\SSL).
**Для браузеров Microsoft Internet Explorer и Microsoft Edge:** по завершении скачивания переименуйте сертификат в BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Шаг 2. Привязка SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Подключение к серверу с помощью MySQL Workbench по протоколу SSL
Настройте MySQL Workbench, чтобы безопасно подключаться по протоколу SSL. В MySQL Workbench в диалоговом окне "Setup New Connection" (Настройка нового подключения) перейдите на вкладку **SSL**. Введите расположение файла **BaltimoreCyberTrustRoot.crt.pem** в поле **SSL CA File:** (Файл SSL-сертификата центра сертификации).
![Сохранение настроенного элемента](./media/howto-configure-ssl/mysql-workbench-ssl.png)

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Подключение к серверу с помощью интерфейса командной строки MySQL по протоколу SSL
С помощью интерфейса командной строки MySQL выполните следующую команду.
```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -u Username@mysqlserver4demo -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Шаг 3. Применение SSL-соединений в Azure 
### <a name="using-azure-portal"></a>Использование портала Azure
С помощью портала Azure перейдите к серверу базы данных Azure для MySQL и щелкните **Безопасность подключения**. Воспользуйтесь переключателем для включения или отключения параметра **Enforce SSL connection** (Применять SSL-соединение). Нажмите кнопку **Сохранить**. Для повышения безопасности корпорация Майкрософт рекомендует всегда включать параметр **Enforce SSL connection** (Применять SSL-соединение).
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Использование Azure CLI
Вы также можете включить или отключить параметр **ssl-enforcement** с помощью Azure CLI, используя значения Enabled и Disabled соответственно.
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```

## <a name="step-4-verify-ssl-connection"></a>Шаг 4. Проверка SSL-соединения
Выполните команду mysql **status**, чтобы проверить наличие подключения к серверу MySQL с помощью протокола SSL.
```dos
mysql> status
```
Убедитесь, что подключение зашифровано, просмотрев выходные данные. Должно отображаться следующее: **SSL: Cipher in use is AES256-SHA** (SSL: используется шифр AES256-SHA). 

## <a name="sample-code"></a>Пример кода
### <a name="php"></a>PHP
```
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'myserver4demo.mysql.database.azure.com', 'myadmin@myserver4demo', 'yourpassword', 'quickstartdb', 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python"></a>Python
```
try:
conn = mysql.connector.connect(user='myadmin@myserver4demo',password='yourpassword',database='quickstartdb',host='myserver4demo.mysql.database.azure.com',ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
 print(err)
```

## <a name="next-steps"></a>Дальнейшие действия
Сведения о вариантах подключения приложений см. в статье [Библиотеки подключений для базы данных Azure для MySQL](concepts-connection-libraries.md).

