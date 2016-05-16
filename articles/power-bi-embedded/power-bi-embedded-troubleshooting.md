<properties
   pageTitle="Устранение неполадок в предварительной версии Microsoft Power BI Embedded"
   description="Устранение неполадок в предварительной версии Microsoft Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="04/25/2016"
   ms.author="derrickv"/>

# Устранение неполадок в предварительной версии Microsoft Power BI Embedded
В этой статье содержатся сведения об устранении неполадок **Power BI Embedded**.

<a name="connection-string"/>
## Настройка строк подключения SQL Server
Чтобы задать строку подключения SQL Server, необходимо следовать определенному формату. Ниже приведен пример строки подключения для SQL Server.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

Дополнительные сведения о строках подключения SQL Server см. в следующих статьях:

-	[Строки подключения SQL Server](https://msdn.microsoft.com/library/jj653752.aspx)
-	[SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>
## Настройка учетных данных
При наличии учетных данных для среды разработки или промежуточной среды, например имени пользователя и пароля, может потребоваться изменить учетные данные в соответствии с рабочим решением.

## См. также
- [Приступая к работе с примером Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md)
- [Что такое Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)

<!---HONumber=AcomDC_0504_2016-->