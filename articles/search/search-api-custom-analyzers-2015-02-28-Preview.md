<properties
	pageTitle="Пользовательские анализаторы (REST API службы поиска Azure, версия 2015-02-28-Preview) | Microsoft Azure"
	description="Пользовательские анализаторы (REST API службы поиска Azure, версия 2015-02-28-Preview)"
	services="search"
	documentationCenter=""
	authors="Yahnoosh"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.author="jlembicz"
	ms.date="02/18/2016" />

# Анализаторы (REST API службы поиска Azure, версия 2015-02-28-Preview)

> [AZURE.NOTE] Пользовательские анализаторы сейчас доступны только в виде предварительной версии. Для работы с ними вам необходимо использовать REST API службы поиска Azure версии 2015-02-28-Preview. Обратите внимание, что функциональные возможности предварительных версий не добавляются в пакет SDK для .NET, поэтому в данный момент можно использовать только REST API предварительной версии.

## Обзор

Говоря простым языком, роль полнотекстовой поисковой системы заключается в том, чтобы обрабатывать и хранить документы, используя метод, который позволяет эффективно составлять поисковые запросы и извлекать нужную информацию. В общих чертах все происходит следующим образом: из документов извлекаются важные слова, эти слова добавляются в индекс, который затем используется для поиска документов, содержащих слова из поискового запроса. Процесс извлечения слов из документов и поисковых запросов называется лексическим анализом. Компоненты, выполняющие лексический анализ, называются анализаторами. В службе поиска Azure на выбор доступны [стандартные языково-независимые анализаторы](#Analyzers) и [анализаторы для конкретных языков](https://msdn.microsoft.com/library/azure/dn879793.aspx). Кроме того, вы можете определять собственные, пользовательские анализаторы. Пользовательские анализаторы позволяют контролировать процесс преобразования текста в токены с возможностями индексации и поиска. Это пользовательская конфигурация, состоящая из одного стандартного токенизатора и как минимум одного фильтра токенов. Токенизатор отвечает за разбивку текста на токены, а фильтры — за изменение токенов, созданных токенизатором.

Чаще всего пользовательские анализаторы используют для таких целей.

- Фонетический поиск. Добавление фонетических фильтров, которые дают возможность искать информацию по звучанию, а не написанию слов.
- Отключение лексического анализа. Использование анализатора ключевых слов для создания полей с возможностью поиска, которые не анализируются.
- Быстрый поиск префиксов или суффиксов. Добавление фильтра граничных N-грамм для индексирования префиксов слов, что позволяет быстро сопоставлять префиксы. В сочетании с обратным фильтром токенов дает возможность сопоставлять суффиксы.
- Настраиваемая токенизация. Например, использование токенизатора по пробелам позволяет разбивать предложения на токены, используя в качестве разделителя пробел.
- Свертывание ASCII. Добавление фильтра свертывания ASCII для нормализации диакритических знаков (например, ö или ê) в условиях поиска.

Вы можете определить несколько пользовательских анализаторов с разными сочетаниями фильтров, но каждое поле может использовать только один анализатор для анализа индексации и один анализатор для анализа поиска.
 
В этой статье приводится список поддерживаемых анализаторов, токенизаторов и фильтров токенов. Здесь вы также найдете описание изменений в определении индекса и пример его использования. Общие сведения об анализаторах и областях их применения см. в статье [Custom Analyzers in Azure Search](link) (Пользовательские анализаторы в службе поиска Azure). Дополнительные сведения о технологии, которая лежит в основе реализации службы поиска Azure, см. в [сводной информации о пакете анализа (Lucene)](http://lucene.apache.org/core/4_10_3/core/org/apache/lucene/analysis/package-summary.html).


## Анализатор по умолчанию

По умолчанию используется [стандартный анализатор Apache Lucene](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html).

## Правила проверки

Имена пользовательских анализаторов, токенизаторов и фильтров токенов должны быть уникальными. Они не могут совпадать с именами стандартных анализаторов, токенизаторов или фильтров токенов.
 

## Определение индекса с пользовательскими анализаторами 

Пользовательские анализаторы определяются во время создания индекса. Синтаксис компонентов пользовательского анализа в определении индекса выглядит вот так: Полный пример можно просмотреть [здесь](#Example).

	{
	  	. . .
	 	(standard create index request body)
	 	. . .  
		"analyzers": (optional) [ 
		    {  
		       "name": "name of analyzer",  
		       "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer", 
		       "tokenizer": "tokenizer_name", (tokenizer_name is the name of a tokenizer from the [Tokenizers](#Tokenizers) table)
		       "tokenFilters": [
				  "token_filter_name", (token_filter_name is the name of a token filter from the [Token Filters](#TokenFilters) table)
				  "token_filter_name"
				] (token filters will be applied from left to right)
		    },
			{  
		       "name": "name of analyzer",  
		       "@odata.type": "#analyzer_type", 
		       "option1": value1,
		       "option2": value2,
		       ...  
		    }
		],  
		"tokenizers": (optional) [ 
		    {  
		       "name": "tokenizer_name",  
		       "@odata.type": "#tokenizer_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		],
		"tokenFilters": (optional) [ 
		    {  
		       "name": "token_filter_name",  
		       "@odata.type": "#token_filter_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		]	
	}

> [AZURE.NOTE] Пользовательские анализаторы, которые вы создаете, не отображаются на портале Azure. Добавить пользовательский анализатор можно только через программный код, который вызывает REST API, на этапе определения индекса.

##Атрибуты индекса для пользовательских анализаторов, токенизаторов и фильтров токенов 

В этом разделе указаны свойства конфигурации для раздела анализаторов, токенизаторов и фильтров токенов в определении индекса.

###Анализаторы

<table>
  <tbody>
    <tr>
      <td>
        <b>Имя</b>
      </td>
      <td>
		Может содержать только буквы, цифры, пробелы, дефисы и символы подчеркивания. Должно начинаться и заканчиваться буквой или цифрой. Максимальная длина не должна превышать 128&#160;символов.
      </td>
    </tr>
    <tr>
      <td>
        <b>Тип</b>
      </td>
      <td>
		Допустимые значения: #Microsoft.Azure.Search.CustomAnalyzer или имя анализатора из списка поддерживаемых анализаторов. См. столбец <b>analyzer_type</b> в таблице [Анализаторы](#Analyzers) ниже.
      </td>
	</tr>
    <tr>
      <td>
        <b>Tokenizer</b> (используется только с #Microsoft.Azure.Search.CustomAnalyzer)
      </td>
      <td>
		Обязательный параметр. Должен быть одним из стандартных токенизаторов, перечисленных в таблице [Токенизаторы](#Tokenizers) ниже, или любым пользовательским токенизатором, определенным в определении индекса.
      </td>
	</tr>
    <tr>
      <td>
        <b>TokenFilters</b> (используется только с #Microsoft.Azure.Search.CustomAnalyzer)
      </td>
      <td>
		Все фильтры токенов должны быть одним из стандартных фильтров токенов, перечисленных в таблице [Фильтры токенов](#TokenFilters) ниже, или любым пользовательским фильтром токенов, определенным в определении индекса.
      </td>
	</tr>
    <tr>
      <td>
        <b>Параметры</b>
      </td>
      <td>
		Используются только [допустимые параметры](#Analyzers) стандартного (не пользовательского) анализатора.
      </td>
	</tr>
  </tbody>
</table>

###Токенизаторы

Токенизатор разбивает непрерывный текст на последовательность токенов, например делит предложение на слова. Для одного пользовательского анализатора можно указать только один токенизатор. Если вам нужно больше токенизаторов, создайте несколько пользовательских анализаторов и в схеме индекса назначьте каждому полю по одному анализатору. В пользовательском анализаторе можно использовать стандартный токенизатор со стандартными или пользовательскими параметрами.

<table>
  <tbody>
    <tr>
      <td>
        <b>Имя</b>
      </td>
      <td>
		Может содержать только буквы, цифры, пробелы, дефисы и символы подчеркивания. Должно начинаться и заканчиваться буквой или цифрой. Максимальная длина не должна превышать 128&#160;символов.
      </td>
    </tr>
    <tr>
      <td>
        <b>Тип</b>
      </td>
      <td>
		Имя токенизатора из списка поддерживаемых токенизаторов. См. столбец <b>tokenizer_type</b> в таблице [Токенизаторы](#Tokenizers) ниже.
      </td>
	</tr>
     <tr>
      <td>
        <b>Параметры</b>
      </td>
      <td>
		Используются только [допустимые параметры](#Tokenizers) соответствующего типа анализатора.
      </td>
	</tr>
  </tbody>
</table>

###Фильтры токенов

Фильтр токенов используется для фильтрации или изменения токенов, созданных токенизатором. Например, вы можете указать специальный фильтр, который преобразует все символы в нижний регистр. Пользовательский анализатор может содержать несколько фильтров токенов. Фильтры токенов применяются в том порядке, в котором они указаны.

<table>
  <tbody>
    <tr>
      <td>
        <b>Имя</b>
      </td>
      <td>
		Может содержать только буквы, цифры, пробелы, дефисы и символы подчеркивания. Должно начинаться и заканчиваться буквой или цифрой. Максимальная длина не должна превышать 128&#160;символов.
      </td>
    </tr>
    <tr>
      <td>
        <b>Тип</b>
      </td>
      <td>
		Имя фильтра токенов из списка поддерживаемых фильтров. См. столбец <b>token_filter_type</b> в таблице [Фильтры токенов](#TokenFilters) ниже.
      </td>
	</tr>
     <tr>
      <td>
        <b>Параметры</b>
      </td>
      <td>
		Используются только [допустимые параметры](#TokenFilters) соответствующего типа фильтра токенов.
      </td>
	</tr>
  </tbody>
</table>

<a name="Example"></a>
##Пример определения индекса

В этом примере определения индекса используется одно поле и пользовательский анализатор my\_analyzer, который, в свою очередь, использует настраиваемый токенизатор my\_standard\_tokenizer и два фильтра токенов: фильтр нижнего регистра (lowercase) и настраиваемый фильтр свертывания ASCII (my\_asciifolding).

	{
	   "name":"myindex",
	   "fields":[
	      {
	         "name":"id",
	         "type":"Edm.String",
	         "key":true,
	         "searchable":false
	      },
	      {
	         "name":"text",
	         "type":"Edm.String",
	         "searchable":true,
	         "analyzer":"my_analyzer"
	      }
	   ],
	   "analyzers":[
	      {
	         "name":"my_analyzer",
	         "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
	         "tokenizer":"my_standard_tokenizer",
	         "tokenFilters":[
	            "my_asciifolding",
	            "lowercase"
	         ]
	      }
	   ],
	   "tokenizers":[
	      {
	         "name":"my_standard_tokenizer",
	         "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
	         "maxTokenLength":20
	      }
	   ],
	   "tokenFilters":[
	      {
	         "name":"my_asciifolding",
	         "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
	         "preserveOriginal":true
	      }
	   ]
	}

<a name="Analyzers"></a>
##Анализаторы

<table>
  <tbody>
	<thead>
	<tr>
		<td>analyzer_name</td>
		<td>analyzer_type</td>
		<td>Описание</td>
		<td>Параметры</td>
	</tr>
	</thead>
    <tr>
      <td>[keyword](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordAnalyzer</td>
	  <td>Обрабатывает все содержимое поля как один токен. Целесообразно использовать для таких данных, как почтовые индексы, идентификаторы и имена некоторых продуктов.</td>
	  <td></td>
    </tr>
    <tr>
      <td>[pattern](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.PatternAnalyzer</td>
	  <td>С помощью шаблона регулярного выражения гибко разделяет текст на термины.</td>
	  <td>
		- lowercase. Тип: логическое значение. Указывает, нужно ли преобразовывать слово в нижний регистр. Значение по умолчанию: True. [pattern](http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) Тип: строка. Шаблон регулярного выражения для сопоставления разделителей токенов. Значение по умолчанию: \w+. [flags](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) Тип: строка. Флаги регулярного выражения. Значение по умолчанию: пустая строка. - stopwords. Тип: массив строк. Список стоп-слов. Значение по умолчанию: пустой список.
	  </td>
    </tr>
    <tr>
      <td>[simple](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SimpleAnalyzer</td>	 
	  <td>Разделяет текст в местах небуквенных символов и преобразует термины в нижний регистр.</td>
	  <td></td>
    </tr>
    <tr>
      <td>[snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SnowballAnalyzer</td>
	  <td>Стандартный анализатор с [фильтром стемминга Snowball] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html).</td>
	  <td>
		- language. Тип: строка. Допустимые значения: danish, dutch, english, finnish, french, german, hungarian, italian, norwegian, portuguese, russian, spanish, swedish. - stopwords. Тип: массив строк. Список стоп-слов. Значение по умолчанию: пустой список.
	  </td>
    </tr>
    <tr>
      <td>[standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StandardAnalyzer</td>
	  <td>Стандартный анализатор Lucene. Состоит из стандартного токенизатора, фильтра нижнего регистра и фильтра стоп-слов.</td>
	  <td>
		- maxTokenLength. Тип: целое число. Максимальная длина токена. Значение по умолчанию: 255. Если длина токена превышает максимальное значение, токен разбивается на части. - stopwords. Тип: массив строк. Список стоп-слов. Значение по умолчанию: пустой список.
	  </td>
    </tr>
    <tr>
      <td>standardasciifolding.lucene</td>
      <td>#Microsoft.Azure.Search.StandardAsciiFoldingAnalyzer</td>	  
	  <td>Стандартный анализатор с фильтром свертывания ASCII</td>
	  <td></td>
    </tr>
    <tr>
      <td>[stop](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StopAnalyzer</td>
	  <td>Разделяет текст в местах небуквенных символов, преобразует термины в нижний регистр и применяет фильтр стоп-слов.</td>
	  <td>
		- stopwords. Тип: массив строк. Список стоп-слов. Значение по умолчанию: пустой список.
	  </td>
    </tr>
    <tr>
      <td>[whitespace](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceAnalyzer</td>
	  <td>Анализатор, в котором используется токенизатор по пробелам.</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="Tokenizers"></a>
##Токенизаторы

<table>
  <tbody>
	<thead>
	<tr>
		<td>tokenizer_name</td>
		<td>tokenizer_type</td>
		<td>Описание</td>
		<td>Параметры</td>
	</tr>
	</thead>
    <tr>
      <td>[classic](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenizer</td>
	  <td>Грамматический токенизатор, который можно использовать для документов, написанных на большинстве европейских языков.</td>
	  <td>
		- maxTokenLength. Тип: целое число. Максимальная длина токена. Значение по умолчанию: 255. Если длина токена превышает максимальное значение, токен разбивается на части. 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenizer</td>
	  <td>Токенизирует входные данные от края на n-граммы заданного размера.</td>
	  <td>
		- minGram. Тип: целое число. Значение по умолчанию: 1. - maxGram. Тип: целое число. Значение по умолчанию: 2. - tokenChars. Тип: массив строк. Классы символов, которые нужно хранить в токенах. Допустимые значения: letter, digit, whitespace, punctuation, symbol.
	  </td>	  
    </tr>
    <tr>
      <td>[keyword](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordTokenizer</td>
	  <td>Создает один токен из всего объема входных данных.</td>
	  <td>
		- bufferSize. Тип: целое число. Размер буфера чтения. Значение по умолчанию: 256.
	  </td>
    </tr>
    <tr>
      <td>[letter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LetterTokenizer</td>
	  <td>Разделяет текст в местах небуквенных символов.</td>
	  <td></td>
    </tr>
    <tr>
      <td>[lowercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenizer</td>
	  <td>Разделяет текст в местах небуквенных символов и преобразует термины в нижний регистр.</td>
	  <td></td>
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenizer</td>
	  <td>Токенизирует входные данные на n-граммы заданного размера.</td>
	  <td>
		- minGram. Тип: целое число. Значение по умолчанию: 1. - maxGram. Тип: целое число. Значение по умолчанию: 2. - tokenChars. Тип: массив строк. Классы символов, которые нужно хранить в токенах. Допустимые значения: letter, digit, whitespace, punctuation, symbol.
	  </td>
    </tr>
    <tr>
      <td>[path_hierarchy](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PathHierarchyTokenizer</td>
	  <td>Токенизатор для иерархий с подобием путей.</td>
	  <td>
		- delimiter. Тип: строка. Значение по умолчанию: /. - replacement. Тип: строка. Если параметр задан, символ-разделитель заменяется. Значение по умолчанию: delimiter. - bufferSize. Тип: целое число. Значение по умолчанию: 1024. - reverse. Тип: логическое значение. Если значение True, создает токен в обратном порядке. Значение по умолчанию: True. - skip. Тип: логическое значение. Начальные токены, которые нужно пропустить. Значение по умолчанию: 0.
	  </td>
    </tr>
    <tr>
      <td>[pattern](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PatternTokenizer</td>
	  <td>Этот токенизатор создает отдельные токены, выполняя сопоставление с шаблоном регулярного выражения.</td>
	  <td>
		 - pattern. Тип: строка. Шаблон регулярного выражения. Значение по умолчанию: \w+. [flags](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) Тип: строка. Флаги регулярного выражения. Значение по умолчанию: пустая строка. - group. Тип: целое число. Группа, которую нужно разобрать на токены. Значение по умолчанию: -1 (разделение).
	  </td>
    </tr>
    <tr>
      <td>[standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.StandardTokenizer</td>
	  <td>Разбивает текст [по правилам сегментирования текста Юникод] (http://unicode.org/reports/tr29/)</td>
	  <td>
		 - maxTokenLength. Тип: целое число. Максимальная длина токена. Значение по умолчанию: 255. Если длина токена превышает максимальное значение, токен разбивается на части. 
	  </td>
    </tr>
    <tr>
      <td>[uax_url_email](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.UaxEmailUrlTokenizer</td>
	  <td>Токенизирует адреса электронной почты и URL-адреса как один токен.</td>
	  <td>
		- maxTokenLength. Тип: целое число. Максимальная длина токена. Значение по умолчанию: 255. Если длина токена превышает максимальное значение, токен разбивается на части.
	  </td>
    </tr>
    <tr>
      <td>[whitespace](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceTokenizer</td>
	  <td>Разделяет текст в местах пробелов.</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="TokenFilters"></a>
##Фильтры токенов

<table>
  <tbody>
	<thead>
	<tr>
		<td>token_filter_name</td>
		<td>token_filter_type</td>
		<td>Описание</td>
		<td>Параметры</td>
	</tr>
	</thead>
    <tr>
      <td>[arabic_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ArabicNormalizationTokenFilter</td>
	  <td>Удаляет все знаки после апострофа (включая и сам апостроф).</td>
    </tr>
    <tr>
      <td>[apostrophe](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)</td>
      <td>#Microsoft.Azure.Search.ApostropheTokenFilter</td>
	  <td>Удаляет все символы после апострофа.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[asciifolding](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.AsciiFoldingTokenFilter</td>
	  <td>Преобразует буквы, цифры и символы Юникода, которые не являются первыми 127&#160;символами ASCII (блок Юникода "Основная латиница"), в их эквиваленты ASCII, если они существуют.</td>
	  <td>
		- preserveOriginal. Тип: логическое значение. Если значение True, исходный токен будет сохранен. Значение по умолчанию: False.
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_bigram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkBigramTokenFilter</td>
	  <td>Формирует биграммы ККЯ-терминов, которые создает стандартный токенизатор.</td>
	  <td>
		- ignoreScripts. Тип: логическое значение. Игнорируемые сценарии. Допустимые значения: han, hiragana, katakana, hangul. Значение по умолчанию: пустой список. - outputUnigrams. Тип: логическое значение. Установите значение True, если всегда нужно выводить биграммы и униграммы. Значение по умолчанию: False.
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_width](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkWidthTokenFilter</td>
	  <td>Выполняет нормализацию ККЯ-ширины. Свертывает варианты ASCII полной ширины в их эквиваленты в основной латинице, а варианты катаканы половинной ширины&#160;— в эквиваленты в кане.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[classic](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenFilter</td>
	  <td>Удаляет английские признаки принадлежности и точки в акронимах.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[common_grams](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)</td>
      <td>#Microsoft.Azure.Search.CommonGramTokenFilter</td>
	  <td>При индексировании создает биграммы для часто встречающихся терминов. Одиночные термины также индексируются, но них накладываются биграммы.</td>
	  <td>
		- commonWords. Тип: массив строк. Набор распространенных слов. Значение по умолчанию: пустой список. - ignoreCase. Тип: логическое значение. Если значение True, при сопоставлении с распространенными словами регистр не учитывается. Значение по умолчанию: False. - queryMode. Тип: логическое значение. Создает биграммы, а затем удаляет распространенные слова и отдельные термины, после которых стоит распространенное слово. Значение по умолчанию: False.
	  </td>	  
    </tr>
    <tr>
      <td>[delimited_payload_filter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/payloads/DelimitedPayloadTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DelimitedPayloadTokenFilter</td>
	  <td>Символы перед разделителем считаются токеном, а после разделителя&#160;— полезными данными. Например, если в качестве разделителя используется символ |, то в строке hello|world слово hello является токеном, а world&#160;— полезными данными. Для преобразования полезных данных соответствующим образом (из символов в байты) можно добавить кодировщик.</td>
	  <td>
		- delimiter. Тип: символ. Значение по умолчанию: |. - encoding. Тип: строка. Допустимые значения: int, float, identity. Значение по умолчанию: float.
	  </td>	  
    </tr>
    <tr>
      <td>[dictionary_decompounder](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DictionaryDecompounderTokenFilter</td>
	  <td>Разбивает составные слова, используемые во многих германских языках, на части.</td>
	  <td>
		- wordList. Тип: массив строк. Список слов для сравнения. Значение по умолчанию: пустой список. - minWordSize. Тип: целое число. Обрабатываются только слова, длина которых превышает указанное значение. Значение по умолчанию: 5. - minSubwordSize. Тип: целое число. Выводятся только подслова, длина которых превышает указанное значение. Значение по умолчанию: 2. - maxSubwordSize. Тип: целое число. Выводятся только подслова, длина которых меньше указанного значения. Значение по умолчанию: 15. - onlyLongestMatch. Тип: логическое значение. Выводится только самое длинное совпадающее подслово. Значение по умолчанию: False. 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenFilter</td>
	  <td>Создает n-граммы заданного размера, начиная с начала или конца входного токена.</td>
	  <td>
		- minGram. Тип: целое число. Значение по умолчанию: 1. - maxGram. Тип: целое число. Значение по умолчанию: 2. - side. Тип: строка. Указывает, с какой стороны входного токена нужно создать n-грамму. Допустимые значения: front и back.
	  </td>	  
    </tr>   
    <tr>
      <td>[elision](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)</td>
      <td>#Microsoft.Azure.Search.ElisionTokenFilter</td>
	  <td>Удаляет элизии. Например, слово l'avion ("самолет" с определенным артиклем) будет преобразовано в слово avion ("самолет" без артикля).</td>
	  <td>
		- articles. Тип: массив строк. Список удаляемых артиклей. Значение по умолчанию: пустой список.
	  </td>	  
    </tr>
    <tr>
      <td>[german_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.GermanNormalizationTokenFilter</td>
	  <td>Нормализует немецкие буквы в соответствии с эвристикой алгоритма [German2 Snowball] (http://snowball.tartarus.org/algorithms/german2/stemmer.html)</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[hindi_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.HindiNormalizationTokenFilter</td>
	  <td>Нормализует текст на хинди, удаляя некоторые различия в вариантах написания.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[indic_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.IndicNormalizationTokenFilter</td>
	  <td>Нормализует представление текста на индийских языках в Юникоде.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[keep](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTokenFilter</td>
	  <td>Фильтр токенов, который оставляет только токены, содержащие текст из указанного списка слов.</td>
	  <td>
		- keepWords. Тип: массив строк. Список слов, которые нужно оставить. Значение по умолчанию: пустой список. - keepWordsCase. Тип: логическое значение. Если значение True, сначала переводит все слова в нижний регистр. Значение по умолчанию: False.
	  </td>	  
    </tr>
    <tr>
      <td>[keep_types](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/TypeTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTypesTokenFilter</td>
	  <td>Оставляет токены, типы которых указаны в списке разрешенных типов.</td>
	  <td>
		- types. Тип: массив строк. Список оставляемых типов.
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_marker](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordMarkerTokenFilter</td>
	  <td>Отмечает термины как ключевые слова.</td>
	  <td>
		- keywords. Тип: массив строк. Список слов, которые нужно отметить как ключевые слова. Значение по умолчанию: пустой список. - ignoreCase. Тип: логическое значение. Если значение True, сначала переводит все слова в нижний регистр. Значение по умолчанию: False.
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_repeat](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordRepeatTokenFilter</td>
	  <td>На основе каждого входного токена создает два выходных: один в качестве ключевого слова, а другой&#160;— в качестве неключевого.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[kstem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)</td>
      <td>#Microsoft.Azure.Search.KStemTokenFilter</td>
	  <td>Высокопроизводительный фильтр KStem для английского языка.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[length](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)</td>
      <td>#Microsoft.Azure.Search.LengthTokenFilter</td>
	  <td>Удаляет слишком длинные или слишком короткие слова.</td>
	  <td>
		- min. Тип: целое число. Минимальное число. Значение по умолчанию: 0. - max. Тип: целое число. Максимальное число. Значение по умолчанию: максимальное целое число.
	  </td>	  
    </tr>
    <tr>
      <td>[limit](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)</td>
      <td>#Microsoft.Azure.Search.LimitTokenFilter</td>
	  <td>Ограничивает количество токенов при индексировании.</td>
	  <td>
		- maxTokenCount. Тип: целое число. Максимальное число создаваемых токенов. Значение по умолчанию: 1. - consumeAllTokens. Тип: логическое значение. Указывает, должны ли использоваться все токены из входных данных, даже когда достигнуто значение maxTokenCount. Значение по умолчанию: False.
	  </td>	  
    </tr>
    <tr>
      <td>[lowercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenFilter</td>
	  <td>Нормализует текст токенов в нижний регистр.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenFilter</td>
	  <td>Создает n-граммы заданного размера.</td>
	  <td>
		- minGram. Тип: целое число. Значение по умолчанию: 1. - maxGram. Тип: целое число. Значение по умолчанию: 2.
	  </td>	  
    </tr>
    <tr>
      <td>[pattern_capture](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.PatternCaptureTokenFilter</td>
	  <td>Использует регулярные выражения Java для создания нескольких токенов&#160;— по одному для каждой группы захвата в одном или нескольких шаблонах.</td>
	  <td>
		- patterns. Тип: массив строк. Список шаблонов для сопоставления с каждым токеном. - preserveOriginal. Тип: логическое значение. Если значение True, возвращается исходный токен, даже если есть совпадение с одним из шаблонов.
	  </td>	  
    </tr>
    <tr>
      <td>[persian_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.PersianNormalizationTokenFilter</td>
	  <td>Применяет нормализацию для персидского языка.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[phonetic]https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)</td>
      <td>#Microsoft.Azure.Search.PhoneticTokenFilter</td>
	  <td>Создает токены для фонетических совпадений.</td>
	  <td>
		- encoder. Тип: строка. Используемый фонетический кодировщик. Допустимые значения: metaphone, doublemetaphone, soundex, refinedsoundex, caverphone1, caverphone2, cologne, nysiis, koelnerphonetik, haasephonetik, beidermorse. Значение по умолчанию: metaphone. - replace. Тип: логическое значение. Если значение True, кодированные токены будут перезаписывать исходные. Если значение False, кодированные токены будут добавляться в качестве синонимов. Значение по умолчанию: True.
	  </td>	  
    </tr>
    <tr>
      <td>[porter_stem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)</td>
      <td>#Microsoft.Azure.Search.PorterStemTokenFilter</td>
	  <td>Преобразует поток токенов согласно [алгоритму стемминга Портера] (http://tartarus.org/~martin/PorterStemmer/)</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[reverse](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)</td>
      <td>#Microsoft.Azure.Search.ReverseTokenFilter</td>
	  <td>Перезаписывает строку токена в обратном порядке.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianNormalizationTokenFilter</td>
	  <td>Нормализует использование взаимозаменяемых символов в скандинавских языках.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_folding](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianFoldingNormalizationTokenFilter</td>
	  <td>Свертывает скандинавские символы: åÅäæÄÆ -> a, öÖøØ -> o. Также исключает использование двойных гласных aa, ae, ao, oe и oo, оставляя только первую гласную.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[shingle](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)</td>
      <td>#Microsoft.Azure.Search.ShingleTokenFilter</td>
	  <td>Создает комбинации токенов как один токен.</td>
	  <td>
		- maxShingleSize. Тип: целое число. Значение по умолчанию: 2. - minShingleSize. Тип: целое число. Значение по умолчанию: 2. - outputUnigrams. Тип: логическое значение. Если значение True, выходной поток будет содержать входные токены (униграммы), а также токены shingle. Значение по умолчанию: True. - outputUnigramsIfNoShingles. Тип: логическое значение. - Если значение True, поведение выходных униграмм переопределяется. Значение False используется тогда, когда токены shingle недоступны. Значение по умолчанию: False. - tokenSeparator. Тип: строка. Строка, которую нужно использовать, чтобы объединить смежные токены и создать один токен shingle. Значение по умолчанию: " ". - filterToken. Тип: строка. Строка, которую нужно вставлять в каждую позицию, где нет токена. Значение по умолчанию: "_".
	  </td>	  
    </tr>
    <tr>
      <td>[snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
      <td>#Microsoft.Azure.Search.SnowballTokenFilter</td>
	  <td>Фильтр токенов стемминга Snowball.</td>
	  <td>
		- language. Тип: строка. Допустимые значения: Armenian, Basque, Catalan, Danish, Dutch, English, Finnish, French, German, German2, Hungarian, Italian, Kp, Lovins, Norwegian, Porter, Portuguese, Romanian, Russian, Spanish, Swedish, Turkish.
	  </td>	  
    </tr>
	<tr>
      <td>[sorani_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.SoraniNormalizationTokenFilter</td>
	  <td>Нормализует представление текста на языке сорани в Юникоде.</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>stemmer</td>
      <td>#Microsoft.Azure.Search.StemmerTokenFilter</td>
	  <td>Фильтр стемминга для определенных языков.</td>
	  <td>
		- language. Тип: строка. Допустимые значения: - [arabic](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html) - [armenian](http://snowball.tartarus.org/algorithms/armenian/stemmer.html) - [basque](http://snowball.tartarus.org/algorithms/basque/stemmer.html) - [brazilian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html) - [bulgarian](http://members.unine.ch/jacques.savoy/Papers/BUIR.pdf) - [catalan](http://snowball.tartarus.org/algorithms/catalan/stemmer.html) - [czech](http://portal.acm.org/citation.cfm?id=1598600) - [danish](http://snowball.tartarus.org/algorithms/danish/stemmer.html) - [dutch](http://snowball.tartarus.org/algorithms/dutch/stemmer.html) - [dutch_kp](http://snowball.tartarus.org/algorithms/kraaij_pohlmann/stemmer.html) - [english](http://snowball.tartarus.org/algorithms/porter/stemmer.html) - [light_english](http://ciir.cs.umass.edu/pubfiles/ir-35.pdf) - [minimal_english](http://www.researchgate.net/publication/220433848_How_effective_is_suffixing) - [possessive_english](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html) - [porter2](http://snowball.tartarus.org/algorithms/english/stemmer.html) - [lovins](http://snowball.tartarus.org/algorithms/lovins/stemmer.html) - [finnish](http://snowball.tartarus.org/algorithms/finnish/stemmer.html) - [light_finnish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [french](http://snowball.tartarus.org/algorithms/french/stemmer.html) - [light_french](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_french](http://dl.acm.org/citation.cfm?id=318984) - [galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [minimal_galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [german](http://snowball.tartarus.org/algorithms/german/stemmer.html) - [german2](http://snowball.tartarus.org/algorithms/german2/stemmer.html) - [light_german](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_german](http://members.unine.ch/jacques.savoy/clef/morpho.pdf) - [greek](http://sais.se/mthprize/2007/ntais2007.pdf) - [hindi](http://computing.open.ac.uk/Sites/EACLSouthAsia/Papers/p6-Ramanathan.pdf) - [hungarian](http://snowball.tartarus.org/algorithms/hungarian/stemmer.html) - [light_hungarian](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [indonesian](http://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf) - [irish](http://snowball.tartarus.org/otherapps/oregan/intro.html) - [italian](http://snowball.tartarus.org/algorithms/italian/stemmer.html) - [light_italian](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [sorani](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html) - [latvian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html) - [norwegian](http://snowball.tartarus.org/algorithms/norwegian/stemmer.html) - [light_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [light_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [portuguese](http://snowball.tartarus.org/algorithms/portuguese/stemmer.html) - [light_portuguese](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [minimal_portuguese](http://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf) - [portuguese_rslp](http://www.inf.ufrgs.br//~viviane/rslp/index.htm) - [romanian](http://snowball.tartarus.org/algorithms/romanian/stemmer.html) - [russian](http://snowball.tartarus.org/algorithms/russian/stemmer.html) - [light_russian](http://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf) - [spanish](http://snowball.tartarus.org/algorithms/spanish/stemmer.html) - [light_spanish](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [swedish](http://snowball.tartarus.org/algorithms/swedish/stemmer.html) - [light_swedish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [turkish](http://snowball.tartarus.org/algorithms/turkish/stemmer.html)
	  </td>	  
    </tr>
	<tr>
      <td>[stemmer_override](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)</td>
      <td>#Microsoft.Azure.Search.StemmerOverrideTokenFilter</td>
	  <td>Все словарные термины помечаются как ключевые слова, чтобы парадигматические модули не разбивали их на более мелкие основы. Должен стоять перед каждым фильтром стемминга.</td>
	  <td>
		- rules. Тип: массив строк. Правила стемминга в формате "слово => основа", например "беговой => бег". Значение по умолчанию: пустой список.
	  </td>	  
    </tr>
	<tr>
      <td>[stop](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)</td>
      <td>#Microsoft.Azure.Search.StopTokenFilter</td>
	  <td>Удаляет стоп-слова из потока токенов.</td>
	  <td>
		- stopwords. Тип: массив строк. Список стоп-слов. Значение по умолчанию: пустой список. - stopwords_list. Тип: строка. Предварительно заданный список стоп-слов. Допустимые значения: _arabic_, _armenian_, _basque_, _brazilian_, _bulgarian_, _catalan_, _czech_, _danish_, _dutch_, _english_, _finnish_, _french_, _galician_, _german_, _greek_, _hindi_, _hungarian_, _indonesian_, _irish_, _italian_, _latvian_, _norwegian_, _persian_, _portuguese_, _romanian_, _russian_, _sorani_, _spanish_, _swedish_, _thai_, _turkish_. Значение по умолчанию: _english_. - ignoreCase. Тип: логическое значение. Если значение True, все слова сначала переводятся в нижний регистр. Значение по умолчанию: False. - removeTrailing. Тип: логическое значение. Если значение True, последнее условие поиска игнорируется, если является стоп-словом. Значение по умолчанию: True.
	  </td>	  
    </tr>
	<tr>
      <td>[synonym](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)</td>
      <td>#Microsoft.Azure.Search.SynonymTokenFilter</td>
	  <td>Сопоставляет один или несколько синонимом в потоке токенов.</td>
	  <td>
		- synonyms. Тип: массив строк. Список синонимов в одном из двух форматов: - невероятный, неимоверный, великолепный => изумительный. Все термины слева от символа => будут заменяться терминами справа. - невероятный, неимоверный, великолепный, изумительный. Список эквивалентных слов, разделенных запятыми. Укажите параметр expand, чтобы изменить способ обработки этого списка. - ignoreCase. Тип: логическое значение. Сопоставление входных данных без учета регистра. Значение по умолчанию: False. - expand. Тип: логическое значение. Если значение True, все слова в списке синонимов будут сопоставляться друг с другом (если не используется формат =>). Список "невероятный, неимоверный, великолепный, изумительный" является эквивалентом списка в формате "невероятный, неимоверный, великолепный, изумительный => невероятный, неимоверный, великолепный, изумительный". Если значение False, список "невероятный, неимоверный, великолепный, изумительный" будет эквивалентом списка в формате "невероятный, неимоверный, великолепный, изумительный => невероятный".   
	  </td>	  
    </tr>
	<tr>
      <td>[trim](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)</td>
      <td>#Microsoft.Azure.Search.TrimTokenFilter</td>
	  <td>Удаляет начальный и конечный пробелы из токенов.</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[truncate](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.TruncateTokenFilter</td>
	  <td>Усекает термины до определенной длины.</td>
	  <td>
		- length. Тип: целое число. Обязательный параметр.
	  </td>	  
    </tr>
	<tr>
      <td>[unique](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.UniqueTokenFilter</td>
	  <td>Отфильтровывает токены с таким же текстом, как и в предыдущем токене.</td>
	  <td>
		- onlyOnSamePosition. Тип: логическое значение. Если параметр задан, удаляются повторы только в такой же позиции. Значение по умолчанию: True.
	  </td>	  
    </tr>
    <tr>
      <td>[uppercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.UppercaseTokenFilter</td>
	  <td>Нормализует текст токена в верхний регистр.</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[word_delimiter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)</td>
      <td>#Microsoft.Azure.Search.WordDelimiterTokenFilter</td>
	  <td>Разделяет слова на подслова и выполняет дополнительные преобразования для групп подслов.</td>
	  <td>
		- generateWordParts. Тип: логическое значение. Разбивает слова на части, например "ПоискAzure" -> "Поиск" "Azure". Значение по умолчанию: True. - generateNumberParts. Тип: логическое значение. Разбивает числовые значения на подслова. Значение по умолчанию: True. - catenateWords. Тип: логическое значение. Соединяет несколько слов в одно, например "Поиск-Azure" -> "ПоискAzure". Значение по умолчанию: False. - catenateNumbers. Тип: логическое значение. Соединяет несколько числовых частей в одно число, например "1-2" -> "12". Значение по умолчанию: False. - catenateAll. Тип: логическое значение. Соединяет все подслова в одно слово, например "Поиск-Azure-1" -> "ПоискAzure1". Значение по умолчанию: False. - splitOnCaseChange. Тип: логическое значение. Если значение True, разбивает слова в месте изменения регистра, например "ПоискAzure" -> "Поиск" "Azure". Значение по умолчанию: True. - preserveOriginal. Сохраняет исходные слова и добавляет их в список подслов. Значение по умолчанию: False. - splitOnNumerics. Тип: логическое значение. Если значение True, разбивает слова в месте вставки числа, например "Поиск1Azure" -> "Поиск" "1" "Azure". Значение по умолчанию: True. - stemEnglishPossessive. Тип: логическое значение. Удаляет в каждом подслове конечные символы 's, обозначающие в английском языке притяжательный падеж. Значение по умолчанию: True. - protectedWords. Тип: массив строк. Токены, которые не нужно разделять. Значение по умолчанию: пустой список.
	  </td>	  
    </tr>
  </tbody>
</table>

**См. также: ** [Azure Search Service REST](http://msdn.microsoft.com/library/azure/dn798935.aspx) (Служба поиска Azure: REST) на веб-сайте MSDN.<br/> [Create Index (Azure Search Service REST API)](http://msdn.microsoft.com/library/azure/dn798941.aspx) (Создание индекса. REST API службы поиска Azure) на веб-сайте MSDN.<br/>

<!---HONumber=AcomDC_0224_2016-->