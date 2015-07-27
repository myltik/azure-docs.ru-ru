## Отправка сообщений в центры событий
В этом разделе мы напишем консольное приложение Java для отправки событий в центр событий. Мы будем использовать поставщик JMS AMQP из [проекта Apache Qpid](http://qpid.apache.org/). Эта процедура аналогична использованию очередей и разделов служебной шины с AMQP на языке Java, как показано [здесь](../articles/service-bus/service-bus-java-how-to-use-jms-api-amqp.md). Дополнительные сведения см. в [документации по Qpid JMS](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html) и [службе сообщений Java](http://www.oracle.com/technetwork/java/jms/index.html).

1. В Eclipse установите [набор средств Azure для Eclipse](https://msdn.microsoft.com/library/azure/hh690946.aspx), в том числе клиентские библиотеки Qpid JMS AMQP.

2. В Eclipse создайте новый проект Java с именем **Sender**.

3. В обозревателе пакетов Eclipse щелкните правой кнопкой мыши проект **Sender** и выберите **Свойства**. В левой части диалогового окна щелкните **Путь к сборке Java**, откройте вкладку **Библиотеки**, а затем нажмите кнопку **Добавить библиотеку**. Выберите **Пакет для клиентских библиотек Apache Qpid для JMS (от MS Open Tech)**, нажмите кнопку **Далее**, а затем нажмите кнопку **Готово**.

	![][8]

4. Создайте файл с именем **servicebus.properties** в корневом каталоге проекта **Sender** со следующим содержимым. Не забудьте заменить следующие значения:
	- имя центра событий;
	- имя пространства имен (обычно это `{event hub name}-ns`);
	- ключ **SendRule**, закодированный в URL-адресе (вы записали его при создании центра событий). Вы можете закодировать его в URL-адрес [здесь](http://www.w3schools.com/tags/ref_urlencode.asp).

			# servicebus.properties - sample JNDI configuration

			# Register a ConnectionFactory in JNDI using the form:
			# connectionfactory.[jndi_name] = [ConnectionURL]
			connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

			# Register some queues in JNDI using the form
			# queue.[jndi_name] = [physical_name]
			# topic.[jndi_name] = [physical_name]
			queue.EventHub = {event hub name}

5. Создайте новый класс с именем **Sender**. Добавьте следующие операторы `import`:

		import java.io.BufferedReader;
		import java.io.IOException;
		import java.io.InputStreamReader;
		import java.io.UnsupportedEncodingException;
		import java.util.Hashtable;

		import javax.jms.BytesMessage;
		import javax.jms.Connection;
		import javax.jms.ConnectionFactory;
		import javax.jms.Destination;
		import javax.jms.JMSException;
		import javax.jms.MessageProducer;
		import javax.jms.Session;
		import javax.naming.Context;
		import javax.naming.InitialContext;
		import javax.naming.NamingException;

6. Затем добавьте следующий код в:

		public static void main(String[] args) throws NamingException,
				JMSException, IOException, InterruptedException {
			// Configure JNDI environment
			Hashtable<String, String> env = new Hashtable<String, String>();
			env.put(Context.INITIAL_CONTEXT_FACTORY,
					"org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
			env.put(Context.PROVIDER_URL, "servicebus.properties");
			Context context = new InitialContext(env);

			ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

			Destination queue = (Destination) context.lookup("EventHub");

			// Create Connection
			Connection connection = cf.createConnection();

			// Create sender-side Session and MessageProducer
			Session sendSession = connection.createSession(false,
					Session.AUTO_ACKNOWLEDGE);
			MessageProducer sender = sendSession.createProducer(queue);

			System.out.println("Press Ctrl-C to stop the sender process");
			System.out.println("Press Enter to start now");
			BufferedReader commandLine = new java.io.BufferedReader(
					new InputStreamReader(System.in));
			commandLine.readLine();

			while (true) {
				sendBytesMessage(sendSession, sender);
				Thread.sleep(200);
			}
		}

		private static void sendBytesMessage(Session sendSession, MessageProducer sender) throws JMSException, UnsupportedEncodingException {
	        BytesMessage message = sendSession.createBytesMessage();
	        message.writeBytes("Test AMQP message from JMS".getBytes("UTF-8"));
	        sender.send(message);
	        System.out.println("Sent message");
	    }



<!-- Links -->
[Azure Management Portal]: https://manage.windowsazure.com/


<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png

<!---HONumber=July15_HO3-->