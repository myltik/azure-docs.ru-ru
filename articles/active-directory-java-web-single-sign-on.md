<properties linkid="develop-java-how-to-guides-web-sso" urlDisplayName="Web SSO" pageTitle="Single sign-on with Azure Active Directory (Java)" metaKeywords="Azure Java web app, Azure single sign-on, Azure Java Active Directory" description="Learn how to create a Java web application that uses single sign-on with Azure Active Directory." metaCanonical="" services="active-directory" documentationCenter="Java" title="Web Single Sign-On with Java and Azure Active Directory" authors="mbaldwin" solutions="" manager="mbaldwin" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="mbaldwin" />

# Единый вход с помощью Java и Azure Active Directory

## <a name="introduction"></a> Введение

В этом учебнике показано, как разработчики Java могут использовать Azure Active Directory, чтобы включить единый вход для пользователей Office 365. Вы научитесь:

-   Подготовка веб-приложения в клиенте заказчика
-   Защита приложения с помощью WS-Federation

### Предварительные требования

В этом учебнике используется конкретный сервер приложений, но опытный разработчик Java может применить описанный ниже процесс и в других средах. Для данного учебника необходимы следующие компоненты среды разработки:

-   [Пример Java-кода для Azure Active Directory][Пример Java-кода для Azure Active Directory]
-   [Среда выполнения Java версии 1.6][Среда выполнения Java версии 1.6]
-   [Сервер приложений JBoss версии 7.1.1.Final][Сервер приложений JBoss версии 7.1.1.Final]
-   [Интегрированная среда разработки JBoss Developer Studio][Интегрированная среда разработки JBoss Developer Studio]
-   Службы IIS 7.5 с поддержкой SSL
-   Windows PowerShell
-   [Командлеты PowerShell для Office 365][Командлеты PowerShell для Office 365]

### Оглавление

-   [Введение][Введение]
-   [Шаг 1: Создание приложения Java][Шаг 1: Создание приложения Java]
-   [Шаг 2: Подготовка приложения в клиенте каталога компании][Шаг 2: Подготовка приложения в клиенте каталога компании]
-   [Шаг 3: Защита приложения с использованием WS-Federation для входа сотрудников][Шаг 3: Защита приложения с использованием WS-Federation для входа сотрудников]
-   [Сводка][Сводка]

## <a name="createapp"></a>Шаг 1: Создание приложения Java

На этом шаге рассказывается, как создать простое приложение Java, которое будет представлять защищенный ресурс. Доступ к этому ресурсу будет предоставляться через федеративную проверку подлинности под управлением службы STS компании; этот вопрос будет рассмотрен подробно далее в этом же модуле.

1.  Откройте новый экземпляр JBoss Developer Studio.
2.  В меню **Файл** выберите **Создать**, затем нажмите **Проект...**.
3.  В диалоговом окне **Новый проект** разверните папку **Maven**, выберите **Проект Maven** и нажмите кнопку **Далее**.
4.  В диалоговом окне **Новый проект Maven** установите флажок **Создать простой проект (пропустить выбор архетипа)**, затем нажмите кнопку **Далее**.
5.  На следующей странице диалогового окна **Новый проект Maven** введите *sample* в полях **ИД группы** и **ИД артефакта**. Выберите **war** в раскрывающемся меню **Упаковка** и нажмите кнопку **Готово**.
6.  После этих действий будет создан новый проект Maven. В расположенном слева меню **обозревателя проектов** разверните проект **sample**, щелкните правой кнопкой мыши файл **pom.xml**, выберите **Открыть с помощью**, затем щелкните **Текстовый редактор**.
7.  В файле **pom.xml** добавьте следующий XML-код в разделе *\<project\>*:

        <repositories>
            <repository>
                <id>jboss-public-repository-group</id>
                <name>JBoss Public Maven Repository Group</name>
                <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                <layout>default</layout>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>never</updatePolicy>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>never</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <pluginRepositories>
            <pluginRepository>
                <id>jboss-public-repository-group</id>
                <name>JBoss Public Maven Repository Group</name>
                <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                <layout>default</layout>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </pluginRepository>
        </pluginRepositories>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>2.0.2</version>
                    <configuration>
                        <source>1.6</source>
                        <target>1.6</target>
                    </configuration>
                </plugin>
            </plugins>
        </build> 

    После ввода этого XML-кода сохраните файл **pom.xml**.

8.  Щелкните правой кнопкой мыши по проекту **sample** и выберите **Maven**, затем щелкните **Обновить проект Maven**. В диалоговом окне **Обновить проект Maven** нажмите кнопку **ОК**. Это действие обновит проект с учетом изменений в файле **pom.xml**.

9.  Щелкните правой кнопкой мыши по проекту **sample**, выберите **Создать**, затем щелкните **JSP-файл**.

10. В диалоговом окне **Новый JSP-файл** измените путь для нового файла на *sample/src/main/webapp*. Присвойте файлу имя **index.jsp** и нажмите кнопку **Готово**.

11. Новый файл **index.jsp** откроется автоматически. Замените автоматически сгенерированный код приведенным ниже кодом, затем сохраните файл.

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index</title>
        </head>
        <body>
            <h3>Index Page</h3>
        </body>
        </html> 

12. Щелкните правой кнопкой мыши по проекту **sample**, выберите **Запуск от имени**, затем щелкните **Запуск на сервере**.

13. В диалоговом окне **Запуск на сервере** убедитесь, что выбран элемент **JBoss Enterprise Application Platform 6.x**, затем нажмите кнопку **Готово**.

## <a name="provisionapp"></a>Шаг 2: Подготовка приложения в клиенте каталога компании

На этом шаге рассказывается, как администратор пользователя Azure Active Directory готовит приложения Java в клиенте пользователя и настраивает единый вход. После выполнения этого шага сотрудники компании могут проходить проверку подлинности в веб-приложении, используя свои учетные записи Office 365.

Процесс подготовки начинается с создания нового субъекта-службы для приложения. Azure Active Directory использует субъекты-службы для регистрации и проверки подлинности приложений в каталоге.

1.  Загрузите и установите командлеты PowerShell для Office 365, если это еще не сделано.
2.  В меню **Пуск** запустите **модуль Microsoft Online Services для консоли Windows PowerShell**. Эта консоль предоставляет среду командной строки для настройки атрибутов клиента Office 365, например для создания и изменения субъектов-служб.
3.  Чтобы импортировать необходимый модуль **MSOnlineExtended**, введите следующую команду и нажмите клавишу ВВОД.

        Import-Module MSOnlineExtended -Force

4.  Чтобы подключиться к каталогу Office 365, необходимо предоставить учетные данные администратора компании. Введите следующую команду и нажмите клавишу ВВОД, а затем введите свои учетные данные в командной строке:

        Connect-MsolService

5.  Теперь необходимо создать новый субъект-службу для приложения. Введите следующую команду и нажмите клавишу ВВОД:

        New-MsolServicePrincipal -ServicePrincipalNames @("javaSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    На этом шаге выводится информация следующего вида:

        The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
        DisplayName           : Federation Sample Java Website
        ServicePrincipalNames : {javaSample/localhost}
        ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
        AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
        TrustedForDelegation  : False
        AccountEnabled        : True
        KeyType               : Symmetric
        KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
        StartDate             : 12/01/2012 08:00:00 a.m.
        EndDate               : 12/01/2013 08:00:00 a.m.
        Usage                 : Verify 

    > [WACOM.NOTE]
    > Сохраните эти выходные данные, уделив особенное внимание сгенерированному симметричному ключу. Этот ключ отображается только во время создания субъекта-службы, в дальнейшем вы его получить не сможете. Другие значения необходимы, чтобы с помощью Graph API считывать данные из каталога и записывать данные в каталог.

6.  На последнем шаге задается URL-адрес ответа для вашего приложения. На URL-адрес ответа отправляются ответы после попыток проверки подлинности. Введите следующие команды и нажмите клавишу ВВОД:

        $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost:8443/sample" 

        Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

Теперь веб-приложение в каталоге подготовлено, и сотрудники компании могут его использовать для единого входа.

## <a name="protectapp"></a>Шаг 3: Защита приложения с использованием WS-Federation для входа сотрудников

На этом шаге показано, как добавить поддержку федеративного входа с помощью Windows Identity Foundation (WIF) и библиотеки **waad-federation**, загруженной вами в составе пакета примера кода на этапе предварительных требований. Вы также научитесь добавлять страницу входа и настраивать отношения доверия между приложением и клиентом каталога.

1.  В JBoss Developer Studio щелкните **Файл**, затем **Импорт**.

2.  В диалоговом окне **Импорт** разверните папку **Maven**, щелкните **Существующие проекты Maven** и нажмите кнопку **Далее**.

3.  В диалоговом окне **Импорт проектов Maven** задайте для параметра **Корневой каталог** путь к папке, в которую вы загрузили библиотеку **waad-federation** в примере кода. Затем установите флажок рядом с файлом **pom.xml** из проекта **waad-federation** и нажмите кнопку **Готово**.

4.  Разверните проект **sample**, щелкните правой кнопкой по файлу **pom.xml**, выберите **Открыть с помощью**, затем щелкните **Текстовый редактор**.

5.  В файле **pom.xml** добавьте следующий XML-код в разделе *\<project\>*, после чего сохраните его:

        <dependencies>
            <dependency>
                <groupId>javax.servlet</groupId>
                <artifactId>servlet-api</artifactId>
                <version>3.0-alpha-1</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.samples.waad.federation</groupId>
                <artifactId>waad-federation</artifactId>
                <version>0.0.1-SNAPSHOT</version>
            </dependency>
        </dependencies> 

6.  Щелкните правой кнопкой мыши по проекту **sample**, выберите **Maven**, затем щелкните **Обновить проект**. В диалоговом окне **Обновить проект Maven** нажмите кнопку **ОК**. Это действие обновит проект с учетом изменений в файле **pom.xml**.

7.  Щелкните правой кнопкой мыши по проекту **sample**, выберите **Создать**, затем щелкните **Фильтр**.

8.  В диалоговом окне **Создать фильтр** введите *FederationFilter* для элемента **Имя класса**, затем нажмите кнопку **Готово**.

9.  Откроется автоматически созданный файл **FederationFilter.java**. Замените содержащийся в нем код приведенным ниже кодом и сохраните файл.

        import java.io.IOException;
        import javax.servlet.Filter;
        import javax.servlet.FilterChain;
        import javax.servlet.FilterConfig;
        import javax.servlet.ServletException;
        import javax.servlet.ServletRequest;
        import javax.servlet.ServletResponse;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import java.util.regex.*;
        import com.microsoft.samples.federation.FederatedLoginManager; import com.microsoft.samples.federation.URLUTF8Encoder;

        public class FederationFilter implements Filter {
            private String loginPage;
            private String allowedRegex;
            public void init(FilterConfig config) throws ServletException {
                this.loginPage = config.getInitParameter("login-page-url");
                this.allowedRegex = config.getInitParameter("allowed-regex");
            }
            public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
                    if (request instanceof HttpServletRequest) { 
                        HttpServletRequest httpRequest = (HttpServletRequest) request;
                        if (!httpRequest.getRequestURL().toString().contains(this.loginPage)) {
                            FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(httpRequest);
                            boolean allowedUrl = Pattern.compile(this.allowedRegex).matcher(httpRequest.getRequestURL().toString()).find();
                            if (!allowedUrl && !loginManager.isAuthenticated()) {
                                HttpServletResponse httpResponse = (HttpServletResponse) response;
                                String encodedReturnUrl = URLUTF8Encoder.encode(httpRequest.getRequestURL().toString());
                                httpResponse.setHeader("Location", this.loginPage + "?returnUrl=" + encodedReturnUrl);
                                httpResponse.setStatus(302);
                                return;
                            }
                        }
                    }
                chain.doFilter(request, response);
            }
            public void destroy() {
            }
        } 

10. В **обозревателе проектов** разверните последовательно папки **src**, **main** и **webapp**. Щелкните правой кнопкой мыши файл **web.xml**, выберите **Открыть с помощью**, затем щелкните **Текстовый редактор**.

11. В файл **web.xml** будет добавлен фильтр для обработки защищенных и незащищенных страниц. Не прошедшие проверку пользователи будут перенаправляться на страницу входа, указанную в качестве параметра фильтра **login-page-url**. Но если URL-адрес соответствует регулярному выражению, указанному в параметре фильтра **allowed-regex**, он не фильтруется. Добавьте следующий XML-код в разделе *\<web-app\>*, а затем сохраните файл **web.xml**.

        <filter>
            <filter-name>FederationFilter</filter-name>
            <filter-class>FederationFilter</filter-class>
            <init-param>
                <param-name>login-page-url</param-name>
                <param-value>/sample/login.jsp</param-value>
            </init-param>
            <init-param>
                <param-name>allowed-regex</param-name>
                <param-value>(\/sample\/login.jsp|\/sample\/wsfed-saml|\/sample\/oauth)</param-value>
            </init-param>
        </filter>
        <filter-mapping>
            <filter-name>FederationFilter</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping> 

12. Чтобы создать страницу входа, щелкните правой кнопкой мыши по проекту **sample**, выберите **Создать**, затем щелкните **JSP-файл**.

13. В диалоговом окне **Новый JSP-файл** измените путь для нового файла на *sample/src/main/webapp*. Присвойте файлу имя **login.jsp** и нажмите кнопку **Готово**.

14. Новый файл **login.jsp** откроется автоматически. Замените автоматически сгенерированный код приведенным ниже кодом, затем сохраните файл.

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"> 
            <title>Login Page</title>
        </head>
        <body>
            <h3>Login Page</h3>
            <a href="<%=FederatedLoginManager.getFederatedLoginUrl(request.getParameter("returnUrl"))%>"><%=FederatedConfiguration.getInstance().getStsFriendlyName()%></a>
        </body>
        </html> 

15. В **обозревателе проектов** разверните папку **/src/main** проекта **sample**. Щелкните правой кнопкой мыши папку **resources**, выберите **Создать**, затем щелкните **Другое**.

16. В диалоговом окне **Создать** разверните папку **JBoss Tools Web**, щелкните **Файл свойств**, затем нажмите кнопку **Далее**.

17. В диалоговом окне **Создать файл** присвойте файлу имя **federation**, затем нажмите кнопку **Готово**.

18. В **обозревателе проектов** разверните папку **src/main/resources** проекта **sample**. Щелкните правой кнопкой мыши файл **federation.properties**, выберите **Открыть с помощью**, затем щелкните **Текстовый редактор**.

19. В файле **federation.properties** добавьте перечисленные ниже элементы конфигурации и сохраните изменения.

        federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
        federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
        federation.trustedissuers.friendlyname=Fabrikam
        federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
        federation.realm=spn:7829c758-2bef-43df-a685-717089474505
        federation.reply=https://localhost:8443/sample/wsfed-saml 

    > [WACOM.NOTE]
    > Значения **audienceuris** и **realm** должны иметь префикс spn:.

20. Теперь необходимо создать новый сервлет. Щелкните правой кнопкой мыши проект **sample**, выберите **Создать**, затем щелкните **Другое** и **Сервлет**.

21. В диалоговом окне **Создать сервлет** укажите для элемента **Имя класса** значение *FederationServlet* и нажмите кнопку **Готово**.

22. Автоматически откроется файл **FederationServlet.java**. Замените его содержимое приведенным ниже кодом, затем сохраните файл.

        import java.io.IOException;
        import javax.servlet.ServletException;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import com.microsoft.samples.federation.FederatedAuthenticationListener;
        import com.microsoft.samples.federation.FederatedLoginManager;
        import com.microsoft.samples.federation.FederatedPrincipal;
        import com.microsoft.samples.federation.FederationException;

        public class FederationServlet extends HttpServlet {
            private static final long serialVersionUID = 1L;

            protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                String token = request.getParameter("wresult").toString();

                if (token == null) {
                    response.sendError(400, "You were supposed to send a wresult parameter with a token");
                }
                FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(request, new SampleAuthenticationListener());

                try {
                    loginManager.authenticate(token, response);
                } catch (FederationException e) {
                    response.sendError(500, "Oops! and error occurred.");
                }
            }

            private class SampleAuthenticationListener implements FederatedAuthenticationListener {
                @Override
                public void OnAuthenticationSucceed(FederatedPrincipal principal) {
                    // ***
                    // do whatever you want with the principal object that contains the token's claims
                    // ***
                }
            }
        } 

23. В **обозревателе проектов** разверните папку **src/main/webapp/WEB-INF**. Щелкните правой кнопкой мыши файл **web.xml**, выберите **Открыть с помощью**, затем щелкните **Текстовый редактор**.

24. В файле **web.xml** замените значение **/FederationServlet** в разделе *url-pattern* значением **/ws-saml**. Например:

        <servlet>
            <description></description>
            <display-name>FederationServlet</display-name>
            <servlet-name>FederationServlet</servlet-name>
            <servlet-class>FederationServlet</servlet-class>
        </servlet>
        <servlet-mapping>
            <servlet-name>FederationServlet</servlet-name>
            <url-pattern>/wsfed-saml</url-pattern>
        </servlet-mapping> 

25. В **обозревателе проектов** разверните папку **src/main/webapp**. Щелкните правой кнопкой мыши файл **index.jsp**, выберите **Открыть с помощью**, затем щелкните **Текстовый редактор**.

26. В файле **index.jsp** замените имеющийся код приведенным ниже кодом, затем сохраните файл.

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index Page</title>
        </head>
        <body>
            <h3>Welcome <strong><%=FederatedLoginManager.fromRequest(request).getPrincipal().getName()%></strong>!</h3>
            <h2>Claim list:</h2>
            <ul> 
                <% for (Claim claim : FederatedLoginManager.fromRequest(request).getClaims()) { %>
                <li><%= claim.toString()%></li>
                <%  } %>
            </ul>
        </body>
        </html> 

27. В **обозревателе проектов** разверните папку **src/main/webapp/WEB-INF**. Щелкните правой кнопкой мыши файл **web.xml**, выберите **Открыть с помощью**, затем щелкните **Текстовый редактор**.

28. Теперь нужно включить SSL для приложения. В файле **web.xml** добавьте приведенный ниже раздел *\<security-constraint\>* в разделе *\<web-app\>*, а затем сохраните файл.

        <security-constraint>
            <web-resource-collection>
                <web-resource-name>SSL Forwarding</web-resource-name>
                <url-pattern>/*</url-pattern>
                <http-method>POST</http-method>
                <http-method>GET</http-method>
            </web-resource-collection>
            <user-data-constraint>
                <transport-guarantee>CONFIDENTIAL</transport-guarantee>
            </user-data-constraint>
        </security-constraint> 

    > [WACOM.NOTE]
    > Перед продолжением убедитесь, что сервер JBoss уже настроен на поддержку SSL.

29. Теперь мы готовы к полноценному запуску приложения. Щелкните правой кнопкой мыши проект **sample**, выберите **Запуск от имени**, затем щелкните **Запуск на сервере**. Примите указанные ранее значения и нажмите кнопку **Готово**.

30. Откроется страница входа в браузере JBoss. Если щелкнуть ссылку **Специальное предложение**, вы перейдете на страницу поставщика удостоверений Office 365, на которой можете выполнить вход, используя ваши учетные данные клиента каталога. Например, <*john.doe@fabrikam.onmicrosoft.com*>.

31. После входа вы будете снова перенаправлены на защищенную страницу (**sample/index.jsp**), но уже как пользователь, прошедший проверку подлинности.

## <a name="summary"></a>Сводка

В этом учебнике показано, как создать и настроить приложение Java для одного клиента, использующее возможности единого входа Azure Active Directory.

  [Пример Java-кода для Azure Active Directory]: https://github.com/WindowsAzure/azure-sdk-for-java-samples/tree/master/WAAD.WebSSO.JAVA
  [Среда выполнения Java версии 1.6]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [Сервер приложений JBoss версии 7.1.1.Final]: http://www.jboss.org/jbossas/downloads/
  [Интегрированная среда разработки JBoss Developer Studio]: https://devstudio.jboss.com/earlyaccess/
  [Командлеты PowerShell для Office 365]: http://onlinehelp.microsoft.com/ru-ru/office365-enterprises/ff652560.aspx
  [Введение]: #introduction
  [Шаг 1: Создание приложения Java]: #createapp
  [Шаг 2: Подготовка приложения в клиенте каталога компании]: #provisionapp
  [Шаг 3: Защита приложения с использованием WS-Federation для входа сотрудников]: #protectapp
  [Сводка]: #summary
