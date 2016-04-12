<properties 
	pageTitle="Отправка пользовательского веб-приложения Java в Azure" 
	description="В этом учебнике объясняется, как отправить пользовательское веб-приложение Java в веб-приложения службы приложений Azure." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="robmcm"/>

# Отправка пользовательского веб-приложения Java в Azure

В этом разделе объясняется, как отправить пользовательское веб-приложение Java в веб-приложения [службы приложений Azure]. Информация, приведенная здесь, применима к любому веб-сайту или веб-приложению Java, также даны несколько примеров для конкретных приложений.

Обратите внимание, что Azure предоставляет средства для создания веб-приложений Java с помощью пользовательского интерфейса настройки портала Azure, как описано в разделе [Создание веб-приложения Java в службе приложений Azure](web-sites-java-get-started.md). Этот учебник предназначен для ситуаций, в которых не требуется использовать пользовательский интерфейс настройки портала Azure или Azure Marketplace.

## Инструкции по настройке

Ниже описаны параметры, которые следует задать для пользовательских веб-приложений Java в Azure.

- HTTP-порт, используемый процессом Java, назначается динамически. Процесс должен использовать порт из переменной среды `HTTP_PLATFORM_PORT`.
- Все прослушивающие порты, кроме одного прослушивателя HTTP, должны быть отключены. В Tomcat сюда относятся порты завершения работы, HTTPS и AJP.
- Контейнер должен быть настроен на использование исключительно трафика IPv4.
- В конфигурации для приложения необходимо задать команду **startup**.
- Приложения, которым требуются каталоги с разрешением на запись, должны находиться в каталоге контента веб-приложения Azure, которым является каталог **D:\\home**. Переменная среды `HOME` ссылается на D:\\home.  

При необходимости можно задать переменные среды в файле web.config.

## Конфигурация httpPlatform в Web.config

Ниже приведена информация о формате **httpPlatform**, используемом в web.config.
                                 
**arguments** (значение по умолчанию — ""). Аргументы для исполняемого файла или скрипта, указанного в параметре **processPath**.

Примеры (показаны с включенным **processPath**):

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"
    
    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP\_PLATFORM\_PORT% -Djetty.base=";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115"; -jar ";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar";"


**processPath** — путь к исполняемому файлу или скрипту, который запустит процесс прослушивания для HTTP-запросов.

Примеры:


    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
                                                                                       
**rapidFailsPerMinute** (значение по умолчанию — 10). Допустимое количество сбоев в минуту для процесса, указанного в **processPath**. Если этот предел превышен, **HttpPlatformHandler** остановит запуск процесса в течение оставшейся части минуты.
                                    
**requestTimeout** (значение по умолчанию — "00:02:00"). Период ожидания **HttpPlatformHandler** ответа от процесса, прослушивающего `%HTTP_PLATFORM_PORT%`.

**startupRetryCount** (значение по умолчанию — 10). Допустимое число попыток **HttpPlatformHandler** запустить процесс, указанный в **processPath**. Для получения дополнительных сведений см. описание **startupTimeLimit**.

**startupTimeLimit** (значение по умолчанию — 10 секунд). Период ожидания **HttpPlatformHandler** запуска исполняемым файлом или скриптом процесса прослушивания порта. Если этот предел превышен, **HttpPlatformHandler** завершит этот процесс и попробует запустить его столько раз, сколько указано в **startupRetryCount**.
                                                                                      
**stdoutLogEnabled** (значение по умолчанию — "true"). Если значение равно true, **stdout** и **stderr** для процесса, указанного в параметре **processPath**, будут перенаправлены в файл, указанный в **stdoutLogFile** (см. раздел **stdoutLogFile**).
                                    
**stdoutLogFile** (значение по умолчанию — "d:\\home\\LogFiles\\httpPlatformStdout.log"). Абсолютный путь к файлу, для которого будут регистрироваться **stdout** и **stderr** из процесса, указанного в **processPath**.
                                    
> [AZURE.NOTE] `%HTTP_PLATFORM_PORT%` — специальный заполнитель, который должен быть указан как часть **arguments** или как часть списка **httpPlatform** **environmentVariables**. **HttpPlatformHandler** заменит это значение на сформированный системой порт, чтобы указанный в **processPath** процесс мог прослушивать этот порт.

## Развертывание

Веб-приложения на основе Java можно легко развернуть с помощью большинства средств, которые используются для веб-приложений на основе служб IIS. Полностью поддерживаются механизмы развертывания FTP, Git и Kudu, а также встроенная функция SCM для веб-приложений. WebDeploy работает как протокол, однако, поскольку код Java разрабатывается не в Visual Studio, WebDeploy не подходит для развертывания веб-приложений Java.

## Примеры конфигурации приложений

Для следующих приложений в качестве примера приводится файл web.config и конфигурация приложения, чтобы показать, как обеспечить работу приложений Java в веб-приложениях службы приложений.

### Tomcat
Хотя существует два варианта Tomcat, поставляемых с веб-приложениями службы приложений, по-прежнему доступна возможность отправки экземпляров, настроенных для определенного клиента. Ниже приведен пример установки Tomcat с другой виртуальной машиной Java (JVM).

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat" 
	        arguments="">
	      <environmentVariables>
	        <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	        <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\bin\tomcat" />
	        <environmentVariable name="JRE_HOME" value="%HOME%\site\wwwroot\bin\java" /> <!-- optional, if not specified, this will default to %programfiles%\Java -->
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

На стороне Tomcat необходимо внести несколько изменений в конфигурацию. В server.xml необходимо задать следующие параметры:

-	Порт завершения работы = -1.
-	Порт соединителя HTTP = {port.http}.
-	Адрес соединителя HTTP = "127.0.0.1".
-	Закомментируйте соединители HTTPS и AJP.
-	Параметр IPv4 можно также задать в файле catalina.properties, добавив в него `java.net.preferIPv4Stack=true`
    
Вызовы Direct3d в веб-приложениях службы приложений не поддерживаются. Если ваше приложение выполняет такие вызовы, то для их отключения добавьте следующий параметр Java: `-Dsun.java2d.d3d=false`

### Jetty

Как в случае с Tomcat, клиенты могут отправлять собственные экземпляры для Jetty. Если выполняется полная установка Jetty, конфигурация будет выглядеть следующим образом:

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
	         arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115"; -jar ";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar";"
	        startupTimeLimit="20"
		  startupRetryCount="10"
		  stdoutLogEnabled="true">
	    </httpPlatform>
	  </system.webServer>
	</configuration>

Необходимо изменить конфигурацию Jetty в файле start.ini, чтобы задать `java.net.preferIPv4Stack=true`.

### Springboot
Чтобы запустить приложение Springboot, необходимо отправить JAR- или WAR-файл и добавить следующий файл web.config. Файл web.config необходимо поместить в папку wwwroot. Настройте аргументы в файле web.config, чтобы он указывал на JAR-файл. В следующем примере JAR-файл также расположен в папке wwwroot.

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%JAVA_HOME%\bin\java.exe"
	        arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar ";%HOME%\site\wwwroot\my-web-project.jar";">
	    </httpPlatform>
	  </system.webServer>
	</configuration>


### Hudson

В нашем тесте использовался WAR-файл Hudson 3.1.2 и экземпляр Tomcat 7.0.50 по умолчанию, но не использовался пользовательский интерфейс настройки. Поскольку Hudson представляет собой средство построения программного обеспечения, рекомендуется установить его на выделенных экземплярах, где в веб-приложении можно установить флаг **AlwaysOn**.

1. В корневом каталоге веб-приложения, например **d:\\home\\site\\wwwroot**, создайте каталог **webapps** (если он отсутствует) и поместите файл Hudson.war в каталог **d:\\home\\site\\wwwroot\\webapps**.
2. Загрузите средство apache maven 3.0.5 (совместимо с Hudson) и поместите его в каталог **d:\\home\\site\\wwwroot**.
3. Создайте файл web.config в каталоге **d:\\home\\site\\wwwroot** и вставьте в него следующее содержимое:
	
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		  <system.webServer>
		    <handlers>
		      <add name="httppPlatformHandler" path="*" verb="*" 
		modules="httpPlatformHandler" resourceType="Unspecified" />
		    </handlers>
		    <httpPlatform processPath="%AZURE_TOMCAT7_HOME%\bin\startup.bat"
		startupTimeLimit="20"
		startupRetryCount="10">
		<environmentVariables>
		  <environmentVariable name="HUDSON_HOME" 
		value="%HOME%\site\wwwroot\hudson_home" />
		  <environmentVariable name="JAVA_OPTS" 
		value="-Djava.net.preferIPv4Stack=true -Duser.home=%HOME%/site/wwwroot/user_home -Dhudson.DNSMultiCast.disabled=true" />
		</environmentVariables>            
		    </httpPlatform>
		  </system.webServer>
		</configuration>

    На данном этапе можно перезапустить веб-приложение для вступления изменений в силу. Подключитесь к http://yourwebapp/hudson для запуска Hudson.

4. После самонастройки Hudson должен появиться следующий экран:

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)
    
5. Перейдите к странице конфигурации Hudson: выберите **Управление Hudson** и **Настройка системы**.
6. Настройте JDK, как показано ниже.

	![Конфигурация Hudson](./media/web-sites-java-custom-upload/hudson2.png)

7. Настройте Maven, как показано ниже.

	![Конфигурация Maven](./media/web-sites-java-custom-upload/maven.png)

8. Сохраните параметры. Теперь продукт Hudson должен быть настроен и готов к использованию.

Дополнительные сведения о Hudson см. на сайте [http://hudson-ci.org](http://hudson-ci.org).

### Liferay

Liferay поддерживается в веб-приложениях службы приложений. Поскольку для работы Liferay может потребоваться значительный объем памяти, веб-приложение должно выполняться в выделенном рабочем процессе среднего или крупного размера, который может предоставить достаточный объем памяти. Запуск Liferay занимает несколько минут. По этой причине рекомендуется установить для веб-приложения значение **Всегда включено**.

После загрузки Liferay следующие файлы были изменены с помощью Liferay 6.1.2 Community Edition GA3 из одного пакета с Tomcat:

**Server.xml**

- Измените порт завершения работы на -1.
- Измените соединитель HTTP на `<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />`
- Закомментируйте соединитель AJP.

В папке **liferay\\tomcat-7.0.40\\webapps\\ROOT\\WEB-INF\\classes** создайте файл с именем **portal-ext.properties**. Этот файл должен содержать одну строку, как показано ниже:

    liferay.home=%HOME%/site/wwwroot/liferay

На том же уровне структуры каталогов, где находится папка tomcat-7.0.40, создайте файл с именем **web.config** со следующим содержимым:

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	<add name="httpPlatformHandler" path="*" verb="*"
	     modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\tomcat-7.0.40\bin\catalina.bat" 
	                  arguments="run" 
	                  startupTimeLimit="10" 
	                  requestTimeout="00:10:00" 
	                  stdoutLogEnabled="true">
	      <environmentVariables>
	  <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	  <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\tomcat-7.0.40" />
	        <environmentVariable name="JRE_HOME" value="D:\Program Files\Java\jdk1.7.0_51" /> 
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

В разделе **httpPlatform** для параметра **requestTimeout** задано значение "00:10:00". Его можно уменьшить, однако в этом случае повышается вероятность возникновения ошибок времени ожидания при начальной загрузке Liferay. При изменении этого значения следует также изменить параметр **connectionTimeout** в файле server.xml на стороне tomcat.

Следует отметить, что переменная среды JRE\_HOME в приведенном выше файле web.config указывает на 64-разрядную версию JDK. По умолчанию используется 32-разрядная версия, но поскольку Liferay может потребоваться большой объем памяти, рекомендуется использовать 64-разрядную версию JDK.

После внесения этих изменений перезапустите веб-приложения с Liferay, а затем откройте http://yourwebapp. Портал Liferay доступен из корневого каталога веб-приложения.

## Дальнейшие действия

Дополнительные сведения о Liferay см. на сайте [http://www.liferay.com](http://www.liferay.com).

Дополнительные сведения о Java см. в [Центре разработчика Java](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 
<!-- External Links -->
[службы приложений Azure]: http://go.microsoft.com/fwlink/?LinkId=529714

<!---HONumber=AcomDC_0309_2016-->