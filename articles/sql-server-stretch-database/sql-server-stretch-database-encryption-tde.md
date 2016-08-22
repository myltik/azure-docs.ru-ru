<properties
   pageTitle="Включение прозрачного шифрования данных (TDE) для базы данных SQL Server Stretch в Azure | Microsoft Azure"
   description="Включение прозрачного шифрования данных (TDE) для базы данных SQL Server Stretch в Azure"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# Включение прозрачного шифрования данных (TDE) для базы данных Stretch в Azure
> [AZURE.SELECTOR]
- [Портал Azure](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Прозрачное шифрование данных (TDE) помогает защититься от угрозы вредоносных атак за счет шифрования и расшифровки базы данных, связанных резервных копий и файлов журналов транзакций при хранении в реальном времени, не внося изменения в само приложение.

При использовании TDE хранилище всей базы данных шифруется с помощью симметричного ключа, который называется ключом шифрования базы данных. Ключ шифрования базы данных защищается встроенным сертификатом сервера. Каждый сервер Azure обладает уникальным встроенным сертификатом. Корпорация Майкрософт автоматически изменяет эти сертификаты не реже, чем раз в 90 дней. Общее описание TDE см. в статье [Прозрачное шифрование данных (TDE)].

##Включение шифрования

Чтобы включить прозрачное шифрование для базы данных Azure, где хранятся данные, перенесенные из Базы данных SQL Server Stretch, выполните следующее.

1. Откройте базу данных на [портале Azure](https://portal.azure.com).
2. В колонке базы данных нажмите кнопку **Параметры**.
3. Выберите параметр **Прозрачное шифрование данных**. ![][1]
4. Выберите параметр **Включить**, а затем **Сохранить** ![][2].


##Отключение шифрования

Чтобы отключить прозрачное шифрование для базы данных Azure, где хранятся данные, перенесенные из Базы данных SQL Server Stretch, выполните следующее.

1. Откройте базу данных на [портале Azure](https://portal.azure.com).
2. В колонке базы данных нажмите кнопку **Параметры**.
3. Выберите параметр **Прозрачное шифрование данных**.
4. Выберите параметр **Отключить**, а затем **Сохранить**.




.<!--Anchors-->
[Прозрачное шифрование данных (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


.<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


.<!--Link references-->

<!---HONumber=AcomDC_0810_2016-->