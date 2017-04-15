---
title: "Подключение к базе данных SQL с помощью SQL Server Management Studio в Azure RemoteApp | Документация Майкрософт"
description: "С помощью данного учебника вы узнаете, как использовать SQL Server Management Studio в Azure RemoteApp для обеспечения безопасности и улучшения производительности при подключении к базе данных SQL"
services: sql-database
documentationcenter: 
author: adhurwit
manager: jhubbard
ms.assetid: 1052c83c-e7f5-4736-922f-216194d8874b
ms.service: sql-database
ms.custom: manage-how-to
ms.workload: data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: adhurwit
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 92cad42344fb6f77b3088f74ea0e37a542bd0378
ms.lasthandoff: 04/03/2017


---
# <a name="use-sql-server-management-studio-in-azure-remoteapp-to-connect-to-sql-database"></a>Подключение к базе данных SQL с помощью SQL Server Management Studio в Azure RemoteApp

> [!IMPORTANT]
> Мы выводим удаленное приложение Azure RemoteApp из эксплуатации. Дополнительные сведения см. в [объявлении](https://go.microsoft.com/fwlink/?linkid=821148).
>

## <a name="introduction"></a>Введение
В этом руководстве показано, как использовать SQL Server Management Studio (SSMS) в Azure RemoteApp для подключения к базе данных SQL. Здесь приведены пошаговые инструкции по настройке SQL Server Management Studio в Azure RemoteApp, описаны преимущества и показаны функции безопасности, которые можно использовать в Azure Active Directory.

**Предполагаемое время выполнения:** 45 минут.

## <a name="ssms-in-azure-remoteapp"></a>SSMS в Azure RemoteApp
Azure RemoteApp — это служба удаленных рабочих столов в Azure, предоставляющая приложения. Узнать о ней больше можно здесь: [Что такое Azure RemoteApp?](../remoteapp/remoteapp-whatis.md)

При запуске в Azure RemoteApp SSMS обеспечивает те же возможности, что и при локальном запуске.

![Снимок экрана: выполнение SSMS в Azure RemoteApp][1]

## <a name="benefits"></a>Преимущества
Использование SSMS в Azure RemoteApp дает ряд преимуществ, в том числе перечисленные далее.

* Порту 1433 на сервере Azure SQL Server не нужно предоставлять внешний доступ (за пределами Azure).
* Нет необходимости добавлять и удалять IP-адреса в брандмауэре сервера Azure SQL Server, как раньше.
* Все подключения к Azure RemoteApp выполняются по протоколу HTTPS к порту 443 с помощью зашифрованного протокола удаленного рабочего стола.
* Предусмотрена возможность масштабирования и поддержка многопользовательского режима.
* При использовании SSMS в одном регионе с базой данных SQL повышается производительность.
* Можно проводить аудит использования Azure RemoteApp с помощью выпуска Azure Active Directory Premium Edition, который поддерживает функцию отчетности о действиях пользователя.
* Можно включить многофакторную проверку подлинности (MFA).
* Доступ к SSMS можно получить в любом месте с помощью одного из поддерживаемых клиентов Azure RemoteApp, включая iOS, Android, Mac, Windows Phone и ПК под управлением Windows.

## <a name="create-the-azure-remoteapp-collection"></a>Создание коллекции Azure RemoteApp
Ниже приведены шаги по созданию коллекции Azure RemoteApp с помощью SSMS.

### <a name="1-create-a-new-windows-vm-from-image"></a>1. Создание новой виртуальной машины Windows из образа
Чтобы создать новую виртуальную машину, используйте образ Windows Server Remote Desktop Session Host Windows Server 2012 R2 из коллекции.

### <a name="2-install-ssms-from-sql-express"></a>2. Установка SSMS из SQL Express
На новой виртуальной машине перейдите на эту страницу скачивания: [Microsoft® SQL Server® 2014 Express](https://www.microsoft.com/download/details.aspx?id=42299).

Здесь можно скачать только SSMS. После загрузки перейдите в каталог установки и запустите программу установки SSMS.

Также необходимо установить SQL Server 2014 с пакетом обновления 1. Файл для скачивания доступен здесь: [Microsoft SQL Server 2014 с пакетом обновления 1 (SP1)](https://www.microsoft.com/download/details.aspx?id=46694).

SQL Server 2014 с пакетом обновления 1 включает основные функциональные возможности для работы с базой данных SQL Azure.

### <a name="3-run-validate-script-and-sysprep"></a>3. Запуск сценария проверки и средства SysPrep
На рабочем столе виртуальной машины вы найдете сценарий PowerShell с именем Validate (проверка). Запустите его двойным щелчком мыши. Сценарий проверит, готова ли виртуальная машина для удаленного размещения приложений. После завершения проверки вам будет предложено запустить средство SysPrep. Запустите его.

Завершив работу, средство SysPrep также завершит работу виртуальной машины.

Дополнительные сведения о создании образа Azure RemoteApp см. [в этой записи блога](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx).

### <a name="4-capture-image"></a>4. Запись образа
Если виртуальная машина остановлена, найдите ее на текущем портале и запишите ее образ.

Дополнительные сведения о записи образа виртуальной машины Windows Azure, созданной с помощью классической модели развертывания, см. [здесь](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

### <a name="5-add-to-azure-remoteapp-template-images"></a>5. Добавление образа к образам шаблонов Azure RemoteApp
В разделе Azure RemoteApp на текущем портале перейдите на вкладку «Образы шаблона» и нажмите кнопку «Добавить». Во всплывающем окне выберите «Импортировать образ из библиотеки виртуальных машин» и укажите только что созданный образ.

### <a name="6-create-cloud-collection"></a>6. Создание облачной коллекции
На текущем портале создайте новую облачную коллекцию Azure RemoteApp. Выберите только что импортированный образ шаблона с установленным на нем набором SSMS.

![Создание облачной коллекции][2]

### <a name="7-publish-ssms"></a>7. Публикация SSMS
На вкладке «Публикация» новой облачной коллекции в меню «Пуск» выберите пункт «Опубликовать приложение» и укажите SSMS из списка.

![Публикация приложения][5]

### <a name="8-add-users"></a>8. Добавление пользователей
На вкладке «Доступ пользователей» можно выбрать пользователей для предоставления доступа к этой коллекции Azure RemoteApp, которая включает только SSMS.

![Добавить пользователя][6]

### <a name="9-install-the-azure-remoteapp-client-application"></a>9. Установка клиентского приложения Azure RemoteApp
Клиент Azure RemoteApp можно скачать и установить отсюда: [Скачивание | Azure RemoteApp](https://www.remoteapp.windowsazure.com/en/clients.aspx)

## <a name="configure-azure-sql-server"></a>Настройка Azure SQL Server
Теперь осталось разрешить использование служб Azure в брандмауэре. При использовании этого решения вам больше не понадобится добавлять какие-либо IP-адреса, чтобы открыть брандмауэр. Сетевой трафик, разрешенный для SQL Server, поступает из других служб Azure.

![Разрешение Azure][4]

## <a name="multi-factor-authentication-mfa"></a>Многофакторная проверка подлинности (MFA)
Многофакторную проверку подлинности можно включить специально для этого приложения. Перейдите на вкладку «Приложения» в Azure Active Directory. Здесь вы найдете запись для Microsoft Azure RemoteApp. Если вы выберете приложение, а затем начнете его настраивать, откроется показанная ниже страница, на которой можно включить многофакторную проверку подлинности для этого приложения.

![Включение MFA][3]

## <a name="audit-user-activity-with-azure-active-directory-premium"></a>Аудит действий пользователей с помощью Azure Active Directory Premium
Если у вас нет выпуска Azure AD Premium, необходимо включить его в разделе «Лицензии» каталога. Если выпуск Premium включен, вы сможете назначить пользователям уровень «Премиум».

При переходе к пользователю в Azure Active Directory можно перейти на вкладку «Действия», чтобы просмотреть сведения о входе в Azure RemoteApp.

## <a name="next-steps"></a>Дальнейшие действия
Выполнив все указанные выше шаги, вы сможете запустить клиент Azure RemoteApp и выполнить вход от имени назначенного пользователя. Среди ваших приложений отобразится SSMS. Вы сможете запустить это приложение, как если бы оно было установлено на компьютере с доступом к Azure SQL Server.

Дополнительные сведения о подключении к базе данных SQL см. в статье [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md).

Пока что у нас всё. Вот и все!

<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png
