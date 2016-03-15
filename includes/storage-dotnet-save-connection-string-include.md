### Сохранение данных учетной записи хранения в файле app.config.

Затем сохраните данные учетной записи хранения в файле app.config вашего проекта. Замените содержимое файла app.config текстом из следующего примера: введите вместо `myaccount` имя учетной записи хранения, а вместо `mykey` — ключ учетной записи хранения.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

<!---HONumber=AcomDC_0309_2016-->