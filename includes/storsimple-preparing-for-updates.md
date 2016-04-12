<!--author=jgerend last changed: 03/16/16-->

## Подготовка к обновлению
Перед проверкой и установкой обновлений нужно выполнить следующие шаги.


1. Создайте резервную копию данных на устройстве.

2. Убедитесь, что фиксированные IP-адреса вашего контроллера поддерживают маршрутизацию и могут подключаться к Интернету. Эти IP-адреса будут использоваться для передачи обновлений на ваше устройство. Это можно проверить, выполнив следующий командлет на каждом контроллере через интерфейс Windows PowerShell.

 	`Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `

	**Пример выходных данных для тестирования соединения, когда фиксированные IP-адреса могут подключаться к Интернету**


		Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

	    Source	  Destination 	IPV4Address      IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200

		Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

	    Source	  Destination 	  IPV4Address    IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200

Успешно завершив эти проверки, переходите к поиску и установке обновлений.

<!---HONumber=AcomDC_0323_2016-->