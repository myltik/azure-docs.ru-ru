# Загрузка пакета Azure SDK для Java

## Клиентские библиотеки Azure для Java — загрузка вручную

Библиотеки Azure для Java распространяются по [Лицензии Apache, версия 2.0][Лицензии Apache, версия 2.0]. Чтобы загрузить ZIP-файл с библиотеками и всеми зависимостями, нажмите [здесь][здесь]. Предоставлено Microsoft Open Technologies, Inc. Лицензию и другие сведения см. в файлах license.txt и ThirdPartyNotices.txt внутри ZIP-архива.

## Библиотеки Azure для Java — Maven

Если проект уже настроен для использования Maven для построения, добавьте следующую зависимость в файл pom.xml.

    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

В элементе "`<version>`" замените *n.n.n* номером действующей версии, который можно получить в [хранилище библиотек Azure по Maven][хранилище библиотек Azure по Maven].

  [Лицензии Apache, версия 2.0]: http://www.apache.org/licenses/LICENSE-2.0.html
  [здесь]: http://go.microsoft.com/fwlink/?LinkId=253887
  [хранилище библиотек Azure по Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
