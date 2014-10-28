<properties linkid="develop-java-how-to-twilio-phone-call" urlDisplayName="How to Make a Phone Call from Twilio in Java" pageTitle="How to Make a phone call from Twilio (Java) - Azure" metaKeywords="Azure Twilio call, Twilio call website, Azure Twilio Java" description="Learn how to make a phone call from a web page using Twilio in a Java application on Azure." metaCanonical="" services="" documentationCenter="Java" title="How to Make a Phone Call Using Twilio in a Java Application on Azure" authors="MicrosoftHelp@twilio.com; robmcm" videoId="" scriptId="" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm"></tags>

# Осуществление телефонных звонков с использованием Twilio в Java-приложении Azure

В следующем примере показано, как выполнить звонок с веб-страницы, размещенной в Azure, с помощью службы Twilio. В полученном приложении пользователю предлагается ввести нужные данные для телефонного звонка, как показано на следующем снимке экрана.

![Форма вызова Azure с помощью Twilio и Java][Форма вызова Azure с помощью Twilio и Java]

Чтобы использовать код, представленный в этом разделе, выполните следующие действия:

1.  Получите учетную запись Twilio и маркер проверки подлинности. Перед началом работы с Twilio вы можете ознакомиться с ценовой политикой на странице [][]<http://www.twilio.com/pricing></a>. Также вы можете подписаться на страницу [][1]<https://www.twilio.com/try-twilio></a>. Дополнительные сведения о предоставляемых службой Twilio API-интерфейсах см. на странице [][2]<http://www.twilio.com/api></a>.
2.  Проверьте свой номер телефона, который будет использоваться в Twilio как идентификатор объекта, выполняющего исходящий вызов. Дополнительные сведения о проверке номера телефона см. на странице [][3]<https://www.twilio.com/user/account/phone-numbers/verified>\#</a>. Вместо использования существующего номера вы можете приобрести телефонный номер Twilio.
    В этом примере в поле **From** файла callform.jsp (см. описание далее) используется проверенный номер телефона.
3.  Получите JAR-файл Twilio. По адресу [][4]<https://github.com/twilio/twilio-java></a> можно загрузить источники GitHub и создать свои собственные JAR-файлы или загрузить готовый JAR-файл (с зависимостями или без них).
    В этом разделе приводится код, написанный с использованием стандартного JAR-файла TwilioJava-3.3.8-with-dependencies.
4.  Добавьте JAR-файл в путь построения Java.
5.  Если вы используете Eclipse для создания Java-приложения, включите JAR-файл Twilio в файл развертывания приложения (WAR) с помощью компонента сборки развертывания Eclipse. Если среда Eclipse не используется, включите JAR-файл Twilio в ту же роль Azure, которой принадлежит ваше Java-приложение, после чего добавьте в приложение путь к классу.
6.  Убедитесь, что в хранилище ключей cacerts содержится сертификат Equifax Secure Certificate Authority с отпечатком MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (серийный номер 35:DE:F4:CF, отпечаток SHA1 D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Это сертификат центра сертификации (ЦС) для службы [][5]<https://api.twilio.com></a>, который был вызван при использовании интерфейсов API Twilio. Дополнительные сведения о добавлении этого сертификата центра сертификации в хранилище JDK cacert см. в разделе [Добавление сертификата в хранилище сертификатов ЦС Java][Добавление сертификата в хранилище сертификатов ЦС Java].

Кроме того, если вы не используете среду Eclipse, мы настоятельно рекомендуем вам ознакомиться с разделом [Создание приложения "Hello World" с использованием подключаемого модуля Azure для Eclipse с помощью Java (Microsoft Open Technologies)][Создание приложения "Hello World" с использованием подключаемого модуля Azure для Eclipse с помощью Java (Microsoft Open Technologies)], а также другими способами размещения Java-приложений в Azure.

## Создание веб-формы для выполнения звонка

В следующем коде показано, как создать веб-форму, позволяющую извлечь данные пользователя для выполнения звонка. В этом примере создается новый динамический веб-проект **TwilioCloud**, в который в качестве JSP-файла добавляется файл **callform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## Создание кода для выполнения звонка

Следующий код вызывается после заполнения формы пользователем, отображаемой файлом callform.jsp, создает сообщение звонка, а затем сам звонок. В этом примере JSP-файлу присваивается имя **makecall.jsp**, после чего он добавляется в проект **TwilioCloud**. Вместо заполнителей **accountSID** и **authToken** в приведенном ниже коде следует указать вашу учетную запись Twilio и маркер проверки подлинности.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");
     
         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Помимо звонка makecall.jsp отображает конечную точку Twilio, версию API-интерфейса и сведения о состоянии звонка. Пример показан на следующем снимке экрана:

![Ответ на звонок Azure с помощью Twilio и Java][Ответ на звонок Azure с помощью Twilio и Java]

## Выполнение приложения

Ниже приводятся пошаговые инструкции по запуску приложения. Подробное описание этих шагов см. в разделе [Создание приложения Hello World с использованием подключаемого модуля Azure для Eclipse с помощью Java (Microsoft Open Technologies)][Создание приложения "Hello World" с использованием подключаемого модуля Azure для Eclipse с помощью Java (Microsoft Open Technologies)].

1.  Экспортируйте WAR-файл TwilioCloud в папку **approot** Azure.
2.  Измените файл **startup.cmd**, чтобы распаковать WAR-файл TwilioCloud.
3.  Выполните компиляцию приложения для эмулятора среды выполнения приложений.
4.  Запустите развертывание в эмуляторе среды выполнения приложений.
5.  Откройте браузер и введите адрес **<http://localhost:8080/TwilioCloud/callform.jsp>**.
6.  Введите в форму значения, нажмите **Выполнить звонок** и просмотрите результаты в файле makecall.jsp.

Завершив подготовку к развертыванию в Azure, выполните повторную компиляцию для развертывания в облаке, далее выполните развертывание в Azure и запустите <http://*your_hosted_name>*.cloudapp.net/TwilioCloud/callform.jsp в браузере (замените атрибут* именем\_вашего\_размещенного\_приложения\*).

## Дальнейшие действия

В этом коде демонстрируются базовые функциональные возможности службы Twilio в Java в Azure. Возможно, перед развертыванием в рабочей среде Azure потребуется добавить в него дополнительные обработчики ошибок и другие функции. Например:

-   Вместо веб-формы для хранения номеров телефона и текста звонков вы можете использовать хранилище BLOB-объектов Azure или базу данных SQL Azure. Дополнительные сведения об использовании BLOB-объектов Azure в Java см. в разделе [Использование службы хранилища BLOB-объектов Azure из Java][Использование службы хранилища BLOB-объектов Azure из Java]. Дополнительные сведения об использовании базы данных SQL в Java см. в разделе [Использование базы данных SQL в Java][Использование базы данных SQL в Java].
-   С помощью **RoleEnvironment.getConfigurationSettings** вы можете извлечь идентификатор учетной записи Twilio и маркер проверки подлинности из параметров конфигурации развертывания, не прописывая их в makecall.jsp в виде фиксированных значений. Дополнительные сведения о классе **RoleEnvironment** см. в разделе [Использование библиотеки среды выполнения служб Azure в JSP][Использование библиотеки среды выполнения служб Azure в JSP], а также в документации по пакету среды выполнения служб Azure по адресу [][6]<http://dl.windowsazure.com/javadoc></a>.
-   Код makecall.jsp назначает предоставляемый Twilio URL-адрес [][7]<http://twimlets.com/message></a> переменной **Url**. Этот URL-адрес представляет собой ответ в формате языка разметки Twilio (TwiML), содержащий инструкции по обработке звонка в Twilio. Например, возвращаемый ответ TwiML может содержать команду **\<Say\>**, которая задает голосовое воспроизведение текста вызываемому абоненту. Вместо предоставляемого Twilio URL-адреса вы можете построить собственную службу, отвечающую на запросы Twilio. Дополнительные сведения см. в разделе [Использование Twilio для поддержки голосовых возможностей и SMS в Java][Использование Twilio для поддержки голосовых возможностей и SMS в Java]. Дополнительные сведения о TwiML см. на странице [][8]<http://www.twilio.com/docs/api/twiml></a>. Дополнительные сведения о команде **\<Say\>** и других командах Twilio см. на странице [][9]<http://www.twilio.com/docs/api/twiml/say></a>.
-   Рекомендации по безопасности Twilio приведены на странице [][10]<https://www.twilio.com/docs/security></a>.

Дополнительные сведения о Twilio см. на странице [][11]<https://www.twilio.com/docs></a>.

## См. также

-   [Использование Twilio для поддержки голосовых возможностей и SMS в Java][Использование Twilio для поддержки голосовых возможностей и SMS в Java]
-   [Добавление сертификата в хранилище сертификатов ЦС Java][Добавление сертификата в хранилище сертификатов ЦС Java]

  [Форма вызова Azure с помощью Twilio и Java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
  []: http://www.twilio.com/pricing
  [1]: http://www.twilio.com/try-twilio
  [2]: http://www.twilio.com/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [4]: http://github.com/twilio/twilio-java
  [5]: http://api.twilio.com
  [Добавление сертификата в хранилище сертификатов ЦС Java]: ../java-add-certificate-ca-store
  [Создание приложения "Hello World" с использованием подключаемого модуля Azure для Eclipse с помощью Java (Microsoft Open Technologies)]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh690944.aspx
  [Ответ на звонок Azure с помощью Twilio и Java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
  [Использование службы хранилища BLOB-объектов Azure из Java]: http://www.windowsazure.com/ru-ru/develop/java/how-to-guides/blob-storage/
  [Использование базы данных SQL в Java]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh749029.aspx
  [Использование библиотеки среды выполнения служб Azure в JSP]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh690948.aspx
  [6]: http://dl.windowsazure.com/javadoc
  [7]: http://twimlets.com/message
  [Использование Twilio для поддержки голосовых возможностей и SMS в Java]: ../partner-twilio-java-how-to-use-voice-sms
  [8]: http://www.twilio.com/docs/api/twiml
  [9]: http://www.twilio.com/docs/api/twiml/say
  [10]: http://www.twilio.com/docs/security
  [11]: http://www.twilio.com/docs
