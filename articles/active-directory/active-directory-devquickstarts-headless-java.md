<properties
	pageTitle="Приступая к работе с Java в Azure AD | Microsoft Azure"
	description="Создание приложения командной строки Java, выполняющего вход для пользователей для доступа к API."
	services="active-directory"
	documentationCenter="java"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>


# Использование приложения командной строки Java для доступа к API с помощью Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD позволяет простым и удобным образом выполнять функции управления удостоверением вашего веб-приложения, обеспечивая единый вход и выход с помощью всего лишь нескольких строк кода. В веб-приложениях Java это можно делать с помощью реализации корпорации Майкрософт, поддерживаемой сообществом библиотеки ADAL4J.

  Мы будем использовать ADAL4J для следующего:
- Вход пользователя в приложение с использованием Azure AD как поставщика удостоверений.
- Отображение некоторой информации о пользователе.
- Выход пользователя из приложения.

Для этого необходимо:

1. зарегистрировать приложение в Azure AD;
2. настроить приложение для использования библиотеки ADAL4J;
3. использовать библиотеку ADAL4J для отправки запросов в службу Azure AD на вход и выход;
4. распечатать данные о пользователе.

Чтобы начать работу, [загрузите схему приложения](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) или [загрузите готовый пример](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\/archive/complete.zip). Вам также потребуется клиент Azure AD для регистрации приложения. Если у вас нет клиента, [узнайте, как его получить](active-directory-howto-tenant.md).

## 1\. Регистрация приложения в Azure AD
Чтобы ваше приложение могло осуществлять проверку подлинности пользователей, сначала необходимо зарегистрировать новое приложение в своем клиенте.

- Выполните вход на портале управления Azure.
- В левой панели навигации нажмите **Active Directory**.
- Выберите клиент, в котором нужно зарегистрировать приложение.
- Перейдите во вкладку **Приложения** и нажмите кнопку Добавить в нижней панели.
- Следуйте инструкциям на экране, а затем создайте новое **веб-приложение и/или WebAPI**.
    - **Имя** приложения отображает его описание конечным пользователям.
    - **URL-адрес входа** — это базовый URL-адрес вашего приложения. Схема по умолчанию — `http://localhost:8080/adal4jsample/`.
    - **URI идентификатора приложения** — это уникальный идентификатор вашего приложения. Соглашение заключается в использовании `https://<tenant-domain>/<app-name>`, например, `http://localhost:8080/adal4jsample/`
- После завершения регистрации Azure AD присваивает приложению уникальный идентификатор клиента. Это значение понадобится в следующих разделах, поэтому его стоит скопировать из вкладки «Настройка».

Войдите на портал приложения, создайте для своего приложения **секрет приложения** и скопируйте его. Скоро он вам понадобится.


## 2) Настройка приложения для использования библиотеки ADAL4J и всех необходимых компонентов с помощью Maven
Здесь мы настроим библиотеку ADAL4J для использования протокола проверки подлинности OpenID Connect. Кроме всего прочего, ADAL4J будет использоваться для выдачи запросов на вход и выход, управления сеансом пользователя и получения сведений о пользователе.

-	В корневом каталоге проекта откройте или создайте файл `pom.xml`, найдите строку `// TODO: provide dependencies for Maven` и замените ее следующим кодом:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>public-client-adal4j-sample</artifactId>
	<packaging>jar</packaging>
	<version>0.0.1-SNAPSHOT</version>
	<name>public-client-adal4j-sample</name>
	<url>http://maven.apache.org</url>
	<properties>
		<spring.version>3.0.5.RELEASE</spring.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>com.microsoft.azure</groupId>
			<artifactId>adal4j</artifactId>
			<version>1.1.2</version>
		</dependency>
		<dependency>
			<groupId>com.nimbusds</groupId>
			<artifactId>oauth2-oidc-sdk</artifactId>
			<version>4.5</version>
		</dependency>
		<dependency>
			<groupId>org.json</groupId>
			<artifactId>json</artifactId>
			<version>20090211</version>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>3.0.1</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
			<version>1.7.5</version>
		</dependency>
</dependencies>
	<build>
		<finalName>public-client-adal4j-sample</finalName>
		<plugins>
		        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<source>1.7</source>
					<target>1.7</target>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-dependency-plugin</artifactId>
				<executions>
					<execution>
						<id>install</id>
						<phase>install</phase>
						<goals>
							<goal>sources</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-resources-plugin</artifactId>
				<version>2.5</version>
				<configuration>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
			<plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
		</plugins>
	</build>

</project>


```



## 3) Создание файла Java PublicClient

Как упоминалось выше, мы будет использовать API Graph для получения данных о вошедшем в систему пользователе. Для этого нам нужно создать файл, который будет представлять **объект каталога**, и отдельный файл, который будет представлять **пользователя**, чтобы можно было использовать объектно-ориентированный шаблон Java.

1. Создайте файл с именем `DirectoryObject.java`, в котором будут храниться данные о любом объекте DirectoryObject. Вы можете использовать этот объект для любых других запросов Graph. Вставьте в файл следующий код:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


##Компиляция и запуск примера

Вернитесь в корневой каталог и выполните следующую команду, чтобы скомпилировать пример, который вы только что собрали с помощью `maven`. Эта команда будет использовать файл `pom.xml`, созданный для зависимостей.

`$ mvn package`

Теперь в каталоге `/targets` должен быть файл `adal4jsample.war`. Вы можете развернуть его в контейнер Tomcat и открыть URL-адрес

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
WAR-файлы очень просто развертывать с помощью последних версий серверов Tomcat. Просто перейдите в папку `http://localhost:8080/manager/` и следуйте инструкциям по передаче файла `adal4jsample.war`. Он будет автоматически развернут с правильной конечной точкой.

##Дальнейшие действия

Поздравляем! Теперь у нас есть рабочее приложение Java, которое позволяет проверять подлинность пользователей, безопасно вызывать методы веб-API по протоколу OAuth 2.0 и получать основные сведения о пользователе. Если же вы этого еще не сделали, пришло время добавить в клиент нескольких пользователей.

Готовый пример (без ваших значений конфигурации) [можно скачать в виде ZIP-файла здесь](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip) или клонировать с портала GitHub.

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

<!---HONumber=AcomDC_0921_2016-->