<properties 
	pageTitle="Отправка настраиваемого веб-сайта Java в Azure" 
	description="В этом учебнике показано, как передать настраиваемый веб-сайт на Java в Azure." 
	services="web-sites" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

# Отправка настраиваемого веб-сайта Java в Azure

В этом разделе объясняется, как отправить настраиваемый веб-сайт Java в среду Azure. Здесь также представлена информация, применимая к любому веб-сайту Java, и приведено несколько примеров для конкретных приложений.

Обратите внимание, что Azure предоставляет средства для создания веб-сайтов Java с помощью коллекции приложений Azure и пользовательского интерфейса настройки портала Azure, как описано в разделе [Приступая к работе с веб-сайтами Azure и Java
](web-sites-java-get-started.md). Этот учебник предназначен для сценариев, в которых не требуется пользовательский интерфейс настройки Azure или коллекция приложений Azure.  

# Инструкции по настройке

Ниже описаны параметры, которые следует задать для настраиваемых веб-сайтов Java в среде Azure.

- HTTP-порт, используемый процессом Java, назначается динамически.  Процесс должен использовать порт из переменной среды `HTTP_PLATFORM_PORT`.
- Все прослушивающие порты, кроме одного прослушивателя HTTP, должны быть отключены.  В Tomcat сюда относятся порты завершения работы, HTTPS и AJP.
- Контейнер должен быть настроен на использование исключительно трафика IPv4.
- В конфигурации для приложения необходимо задать команду **startup**.
- Приложения, которым требуются каталоги с разрешением на запись, должны находиться в каталоге контента веб-сайта Azure, т. е. в каталоге **D:\home**.  Переменная среды `HOME` ссылается на D:\home.  

При необходимости можно задать переменные среды в файле web.config.

# Конфигурация httpPlatform в Web.config

Ниже приведена информация о формате **httpPlatform**, используемом в web.config.
                                 
**arguments** (значение по умолчанию - ""). Аргументы для исполняемого файла или сценария, указанного в параметре **processPath**.

Примеры (показаны с включенным **processPath**):

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"
    
    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP\_PLATFORM\_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"


**processPath** - путь к исполняемому файлу или сценарию, который запустит процесс прослушивания для HTTP-запросов.


Примеры:


    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
                                                                                       
**rapidFailsPerMinute** (значение по умолчанию - "10"). Допустимое количество сбоев в минуту для процесса, указанного в **processPath**. Если этот предел превышен, **HttpPlatformHandler** остановит запуск процесса в течение оставшейся части минуты.
                                    
**requestTimeout** (значение по умолчанию - "00:02:00"). Период ожидания **HttpPlatformHandler** ответа от процесса, прослушивающего %HTTP_PLATFORM_PORT%.

**startupRetryCount** (значение по умолчанию - "10"). Допустимое число попыток **HttpPlatformHandler** запустить процесс, указанный в **processPath**. Для получения дополнительных сведений см. описание **startupTimeLimit**.

**startupTimeLimit** (значение по умолчанию - 10 секунд). Период ожидания **HttpPlatformHandler** запуска исполняемым файлом или скриптом процесса прослушивания порта.  Если этот предел превышен, **HttpPlatformHandler** завершит этот процесс и попробует запустить его столько раз, сколько указано в **startupRetryCount**.
                                                                                      
**stdoutLogEnabled** (значение по умолчанию - "true"). Если значение равно true, **stdout** и **stderr** для процесса, указанного в параметре **processPath**, будут перенаправлены в файл, указанный в **stdoutLogFile** (см. раздел **stdoutLogFile**).
                                    
**stdoutLogFile** (значение по умолчанию - "d:\home\LogFiles\httpPlatformStdout.log"). Абсолютный путь к файлу, для которого будут регистрироваться **stdout** и **stderr** из процесса, указанного в **processPath**.
                                    
> [AZURE.NOTE] `%HTTP_PLATFORM_PORT%` - специальный заполнитель, который должен быть указан как часть **arguments** или как часть списка **httpPlatform** **environmentVariables**. **HttpPlatformHandler** заменит это значение на сформированный системой порт, чтобы указанный в **processPath** процесс мог прослушивать этот порт.

# Развертывание

Веб-сайты на основе Java можно легко развернуть с помощью большинства средств, которые используются для веб-приложений на основе служб IIS.  Полностью поддерживаются механизмы развертывания FTP, Git и Kudu, а также встроенная функция SCM для веб-сайтов. WebDeploy работает как протокол, однако, поскольку код Java разрабатывается не в Visual Studio, WebDeploy не подходит для сценариев развертывания веб-сайтов Java.

# Примеры конфигурации приложений

Для следующих приложений в качестве примера приводится файл web.config и конфигурация приложения. Этот пример покажет вам, как обеспечить работу приложений Java на веб-сайтах Azure.

## Tomcat
Существует два варианта Tomcat, поставляемых с веб-сайтами Azure, однако по-прежнему доступна возможность отправки экземпляров, настроенных для определенного клиента.  Ниже приведен пример установки Tomcat с другой JVM.

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

На стороне Tomcat необходимо внести несколько изменений в конфигурацию.  В server.xml необходимо задать следующие параметры:

-	Порт завершения работы = -1.
-	Порт соединителя HTTP = {port.http}.
-	Адрес соединителя HTTP = "127.0.0.1".
-	Закомментируйте соединители HTTPS и AJP.
-	Параметр IPv4 можно также задать в файле catalina.properties, добавив в него  `java.net.preferIPv4Stack=true`.
    
Вызовы Direct3d на веб-сайтах Azure не поддерживаются. Если ваше приложение выполняет такие вызовы, то для отключения их добавьте следующий параметр Java: `-Dsun.java2d.d3d=false`

## Jetty

Как в случае с Tomcat, клиенты могут отправлять собственные экземпляры для Jetty. Если выполняется полная установка Jetty, конфигурация будет выглядеть следующим образом:

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
	         arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"
	        startupTimeLimit="20"
		  startupRetryCount="10"
		  stdoutLogEnabled="true">
	    </httpPlatform>
	  </system.webServer>
	</configuration>

Необходимо изменить конфигурацию Jetty в файле start.ini, чтобы задать  `java.net.preferIPv4Stack=true`.

## Hudson

В нашем тесте использовался WAR-файл Hudson 3.1.2 и экземпляр Tomcat 7.0.50 по умолчанию, но не использовался пользовательский интерфейс настройки.  Поскольку Hudson представляет собой средство построения программного обеспечения, рекомендуется установить его на выделенных экземплярах, где на сайте можно установить флаг **AlwaysOn**.

1. В корневом каталоге веб-сайта Azure, то есть **d:\home\site\wwwroot**, создайте каталог **webapps** (если он отсутствует) и поместите файл Hudson.war в каталог **d:\home\site\wwwroot\webapps**.
2. Скачайте средство apache maven 3.0.5 (совместимо с Hudson) и поместите его в каталог **d:\home\site\wwwroot**.
3. Создайте файл web.config в каталоге **d:\home\site\wwwroot** и вставьте в него следующее содержимое:
	
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

    На данном этапе можно перезапустить веб-сайт для вступления изменений в силу.   Подключитесь к http://ваш_сайт/hudson для запуска Hudson.

4. После самонастройки Hudson должен появиться следующий экран:

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)
    
5. Получение доступа к странице настройки Hudson: нажмите **Управление Hudson** и выберите пункт **Настройка системы**.
6. Настройте JDK, как показано ниже.

	![Hudson configuration](./media/web-sites-java-custom-upload/hudson2.png)

7. Настройте Maven, как показано ниже.

	![Maven configuration](./media/web-sites-java-custom-upload/maven.png)

8. Сохраните параметры. Теперь продукт Hudson должен быть настроен и готов к использованию.

Дополнительную информацию о Hudson см. по адресу [http://hudson-ci.org](http://hudson-ci.org).

## Liferay

Liferay поддерживается веб-сайтами Azure. Поскольку для работы Liferay может потребоваться значительный объем памяти, сайт должен выполняться в выделенном рабочем процессе среднего или крупного размера, который может предоставить достаточный объем памяти. Запуск Liferay занимает несколько минут. По этой причине рекомендуется установить для веб-сайта значение **Всегда включено**.  

После загрузки Liferay следующие файлы были изменены с помощью Liferay 6.1.2 Community Edition GA3 из одного пакета с Tomcat:

**Server.xml**

- Измените порт завершения работы на -1.
- Измените "HTTP connector" на 
		`<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />`
- Закомментируйте соединитель AJP.

В папке **liferay\tomcat-7.0.40\webapps\ROOT\WEB-INF\classes** создайте файл с именем **portal-ext.properties**. Этот файл должен содержать одну строку, как показано ниже:

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

В разделе **httpPlatform** для параметра **requestTimeout** задано значение "00:10:00".  Его можно уменьшить, однако в этом случае повышается вероятность возникновения ошибок времени ожидания при начальной загрузке Liferay.  При изменении этого значения следует также изменить параметр **connectionTimeout** в файле server.xml на стороне tomcat.  

Следует отметить, что переменная среды JRE_HOME в приведенном выше файле web.config указывает на 64-разрядную версию JDK. По умолчанию используется 32-разрядная версия, но поскольку Liferay может потребоваться большой объем памяти, рекомендуется использовать 64-разрядную версию JDK.

После внесения этих изменений перезапустите веб-сайт с Liferay, а затем откройте адрес http://ваш_сайт.  Портал Liferay доступен из корневого каталога веб-сайта. 

Дополнительную информацию о Liferay см. на сайте [http://www.liferay.com](http://www.liferay.com).









<!--HONumber=42-->
