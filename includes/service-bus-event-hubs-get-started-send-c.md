## Отправка сообщений концентраторам событий
В этом разделе мы напишем приложение на языке C для отправки событий в концентратор событий. Мы будем использовать библиотеку Proton AMQP из проекта [Apache Qpid](http://qpid.apache.org/). Эта процедура аналогична использованию очередей и разделов служебной шины с AMQP на языке C, как показано [здесь](https://code.msdn.microsoft.com/windowsazure/Using-Apache-Qpid-Proton-C-afd76504). Дополнительные сведения см. в [документации Qpid Proton](http://qpid.apache.org/proton/index.html).

1. На странице [Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) щелкните ссылку **Установка Qpid Proton** и следуйте инструкциям в зависимости от среды. Предполагается среда Linux, например [Azure Linux VM](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-tutorial/) с Ubuntu 14.04.

2. Для компиляции библиотеки Proton установите следующие пакеты:

		sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev

3. Скачайте [библиотеку Qpid Proton](http://qpid.apache.org/proton/index.html) и извлеките ее, например:
		
		wget http://apache.fastbull.org/qpid/proton/0.7/qpid-proton-0.7.tar.gz
		tar xvfz qpid-proton-0.6.tar.gz

4. Создайте каталог построения, скомпилируйте и установите:

		mkdir build
		cd build
		cmake -DCMAKE_INSTALL_PREFIX=/usr ..
		sudo make install

5. В рабочем каталоге создайте новый файл с именем **sender.c** со следующим содержимым. Не забудьте заменить значение для имени концентратора событий и пространства имен (последнее обычно представляется как `{event hub name}-ns`). Также необходимо заменить версию ключа **SendRule**, закодированную как URL-адрес, созданную ранее. Выполнить кодировку как URL-адрес можно [здесь](http://www.w3schools.com/tags/ref_urlencode.asp).

		#include "proton/message.h"
		#include "proton/messenger.h"
		
		#include <getopt.h>
		#include <proton/util.h>
		#include <sys/time.h>
		#include <stddef.h>
		
		#include <stdio.h>
		#include <string.h>
		#include <unistd.h>
		#include <stdlib.h>
		
		#define check(messenger)                                                     \
		  {                                                                          \
		    if(pn_messenger_errno(messenger))                                        \
		    {                                                                        \
		      printf("check\n");													 \
		      die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
		    }                                                                        \
		  }  
		  
		pn_timestamp_t time_now(void)
		{
		  struct timeval now;
		  if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
		  return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
		}  
		
		void die(const char *file, int line, const char *message)
		{
		  printf("Dead\n");
		  fprintf(stderr, "%s:%i: %s\n", file, line, message);
		  exit(1);
		}
		
		int sendMessage(pn_messenger_t * messenger) {
			char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
			char * msgtext = (char *) "Hello from C!";
				
			pn_message_t * message;
			pn_data_t * body;
			message = pn_message();
		
			pn_message_set_address(message, address);
			pn_message_set_content_type(message, (char*) "application/octect-stream");
			pn_message_set_inferred(message, true);
			
			body = pn_message_body(message);
			pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
			
			pn_messenger_put(messenger, message);
			check(messenger);
			pn_messenger_send(messenger, 1);
			check(messenger);
		
			pn_message_free(message);
		}
		
		int main(int argc, char** argv) {
			printf("Press Ctrl-C to stop the sender process\n");
			
			pn_messenger_t *messenger = pn_messenger(NULL);
			pn_messenger_set_outgoing_window(messenger, 1);
			pn_messenger_start(messenger);
			
			while(true) {
				sendMessage(messenger);
				printf("Sent message\n");
				sleep(1);
			}
			
			// release messenger resources
			pn_messenger_stop(messenger);
			pn_messenger_free(messenger);
			
			return 0;
		}

6. Скомпилируйте файл при условии **gcc**:

		gcc sender.c -o sender -lqpid-proton

> [AZURE.NOTE] В приведенном выше коде исходящее окно 1 используется для скорейшей принудительной отправки сообщений. Обычно приложение предпримет попытку сгруппировать сообщения для увеличения пропускной способности. Дополнительные сведения о том, как использовать библиотеку Qpid Proton в этой и других средах, а также из платформ, для которых предоставляются привязки (в настоящее время Perl, PHP, Python и Ruby), см. на странице [Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html).
<!--HONumber=47-->
