<properties 
   pageTitle="Начало работы с прозрачным шифрованием данных (TDE) в хранилище данных SQL | Microsoft Azure" 
   description="Начало работы с прозрачным шифрованием данных (TDE) в хранилище данных SQL" 
   services="sql-data-warehouse" 
   documentationCenter="" 
   authors="twounder" 
   manager="barbkess" 
   editor=""/>

<tags 
   ms.service="sql-data-warehouse" 
   ms.workload="data-management" 
   ms.tgt_pltfrm="na" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.date="01/07/2016" 
   ms.author="mausher;barbkess;sonyama"/>
 
# Начало работы с прозрачным шифрованием данных (TDE) в хранилище данных SQL
> [AZURE.SELECTOR]
- [Azure Classic Portal](sql-data-warehouse-encryption-tde.md)
- [TSQL](sql-data-warehouse-encryption-tde-tsql.md)

Прозрачное шифрование данных (TDE) хранилища данных SQL Azure помогает защититься от угрозы вредоносных атак за счет шифрования и расшифровки базы данных, связанных резервных копий и файлов журналов транзакций при хранении в реальном времени, не внося изменения в само приложение.

При использовании TDE хранилище всей базы данных шифруется с помощью симметричного ключа, который называется ключом шифрования базы данных. В Базе данных SQL ключ шифрования базы данных защищается встроенным сертификатом сервера. Каждый сервер Базы данных SQL обладает уникальным встроенным сертификатом. Корпорация Майкрософт автоматически изменяет эти сертификаты не реже, чем раз в 90 дней. Общее описание TDE см. в статье [Прозрачное шифрование данных (TDE)].

##Включение шифрования

Чтобы включить прозрачное шифрование данных для хранилища данных SQL, выполните указанные ниже действия.

1. Откройте базу данных на [классическом портале Azure](https://portal.azure.com).
2. В колонке базы данных нажмите кнопку **Параметры**.	
3. Выберите параметр **Прозрачное шифрование данных**. ![][1] 
4. Выберите параметр **Вкл**. ![][2] 
5. Щелкните **Сохранить**. ![][3]  

##Отключение шифрования

Чтобы отключить прозрачное шифрование данных для хранилища данных SQL, выполните указанные ниже действия.

1. Откройте базу данных на [классическом портале Azure](https://portal.azure.com).
2. В колонке базы данных нажмите кнопку **Параметры**.	
3. Выберите параметр **Прозрачное шифрование данных**. ![][1] 
4. Выберите параметр **Выкл.**. ![][4] 
5. Щелкните **Сохранить**. ![][5]  




<!--Anchors-->
[Прозрачное шифрование данных (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

<!---HONumber=AcomDC_0114_2016-->