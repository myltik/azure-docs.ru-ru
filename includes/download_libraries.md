#Загрузка пакета SDK Azure для Java

##Клиентские библиотеки Azure для Java – загрузка вручную 

Библиотеки Azure для Java распространяются по [Лицензии Apache, версия 2.0][license]. Чтобы загрузить ZIP-файл с библиотеками и всеми зависимостями, нажмите [здесь][zip-download].  Предоставлено Microsoft Open Technologies, Inc. Лицензию и другие сведения см. в файлах license.txt и ThirdPartyNotices.txt внутри ZIP-архива.

##Библиотеки Azure для Java – Maven

Если проект уже настроен для использования Maven для построения, добавьте следующую зависимость в файл pom.xml.

	<dependency>
    	<groupId>com.microsoft.windowsazure</groupId>
    	<artifactId>microsoft-windowsazure-api</artifactId>
    	<version>n.n.n</version>
	</dependency>

В элементе `<version>` замените *n.n.n* номером действующей версии, который можно получить в [Хранилище библиотек Azure по Maven](http://go.microsoft.com/fwlink/?LinkID=286274).

[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-download]:  http://go.microsoft.com/fwlink/?LinkId=253887

