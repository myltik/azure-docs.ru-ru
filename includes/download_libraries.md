## Клиентские библиотеки Azure для Java — скачивание вручную

Библиотеки Azure для Java распространяются по [Лицензии Apache, версия 2.0][license]. Чтобы загрузить ZIP-файл с библиотеками и всеми зависимостями, нажмите [здесь][zip-download]. Программный продукт предоставлен Microsoft Open Technologies, Inc. Лицензию и другие сведения см. в файлах license.txt и ThirdPartyNotices.txt внутри ZIP-архива.

## Библиотеки Azure для Java — Maven

Если проект уже настроен для сборки с использованием Maven, добавьте следующую зависимость в файл pom.xml.

	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-compute</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-network</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-sql</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-storage</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-websites</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-media</artifactId>
	    <version>0.6.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-servicebus</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-serviceruntime</artifactId>
	    <version>0.6.0</version>
	</dependency>


В элементе `<version>` вместо номера версии в этом примере введите допустимые номера версий, которые можно получить в [Хранилище библиотек Azure в Maven](http://go.microsoft.com/fwlink/?LinkID=286274).

[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=253887

<!---HONumber=July15_HO3-->