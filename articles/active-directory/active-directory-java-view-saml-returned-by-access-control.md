.<properties
    pageTitle="Просмотр кода SAML, возвращаемого службой контроля доступа (Java)"
    description="Узнайте, как просматривать SAML, возвращенный службой контроля доступа в приложениях Java, размещенных в Azure."
	services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# Как просматривать SAML, возвращенный службой Azure Access Control

В этом руководстве показано, как просмотреть базовый код SAML, который возвращается в ваше приложение службой контроля доступа Azure (ACS). В этом руководстве используются материалы статьи [Проверка подлинности веб-пользователей с помощью службы контроля доступа Azure и Eclipse][], в частности, код, позволяющий отображать данные SAML. Ниже приводится пример завершенного приложения.

![Пример выходных данных SAML][saml_output]

Дополнительные сведения об ACS см. в разделе [Дальнейшие действия](#next_steps).

> [AZURE.NOTE]
Фильтр служб управления доступом Azure является CTP-версией. Эта предварительная версия программного обеспечения не обеспечивается официальной поддержкой корпорации Майкрософт.

## Предварительные требования

Прежде чем выполнять задачи данного руководства, выполните образец, представленный в разделе [Проверка подлинности веб-пользователей с помощью службы контроля доступа Azure и Eclipse][], и используйте его в качестве отправной точки для дальнейшей работы.

## Добавление библиотеки JspWriter к пути построения и сборке развертывания

Добавьте библиотеку, содержащую класс **javax.servlet.jsp.JspWriter**, к пути построения и сборке развертывания. Библиотека **jsp-api.jar** для сервера Tomcat размещается в папке Apache **lib**.

1. В обозревателе проектов Eclipse щелкните правой кнопкой мыши по проекту **MyACSHelloWorld**, выберите **Путь построения**, затем щелкните **Настроить путь построения**, откройте вкладку **Библиотеки** и выберите **Добавить внешние JAR-файлы**.
2. В диалоговом окне **Выбор JAR-файла** выберите нужный JAR-файл и нажмите кнопку **Открыть**.
3. Не закрывайте диалоговое окно **Свойства MyACSHelloWorld** и щелкните **Сборка развертывания**.
4. В диалоговом окне **Сборка веб-развертывания** нажмите кнопку **Добавить**.
5. В диалоговом окне **Новая директива сборки** щелкните **Записи пути построения Java** и нажмите кнопку **Далее**.
6. Выберите соответствующую библиотеку и нажмите кнопку **Готово**.
7. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Свойства MyACSHelloWorld**.

## Изменение JSP-файла для отображения кода SAML

Измените файл **index.jsp**, используя следующий код.

	<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8"%>
	    <%@ page import="javax.xml.parsers.*"
	             import="javax.xml.transform.*"
	             import="org.w3c.dom.*"
	             import="java.io.*"
	             import="javax.xml.transform.stream.*"
	             import="javax.xml.transform.dom.*"
	             import="javax.xml.xpath.*"
	             import="javax.servlet.jsp.JspWriter" %>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
	<title>Sample ACS Filter</title>
	</head>
	<body>
		<h3>SAML information from sample ACS program</h3>
		<%!
	    void displaySAMLInfo(Node node, String parent, JspWriter out)
	    {
	    
		    try
		    {
				String nodeName;
			    int nChild, i;
			    
			    nodeName = node.getNodeName();
			    out.println("<br>");
			    out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
			       
			       // Attributes.
			       NamedNodeMap attribsMap = node.getAttributes();
			       if (null != attribsMap)
			       {
	                     for (i=0; i < attribsMap.getLength(); i++)
	                     {
	                            Node attrib = attribsMap.item(i);
	                            out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
	                     }
			       }
			       
			       // Child nodes.
			       NodeList list = node.getChildNodes();
			       if (null != list)
	 		       {
			              nChild = list.getLength();
			              if (nChild > 0)
			              {                    
	
				                 // If it is a text node, just print the text.
				                 if (list.item(0).getNodeName() == "#text")
				                 {
	                                 out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
				                 }
				                 else
				                 {
				                	 // Print out the child node names.
				                	 out.print("Contains " + nChild + " child node(s): ");   
		   		                     for (i=0; i < nChild; i++)
				                     {
					                    Node temp = list.item(i);
					                    
					                    out.print("<b>" + temp.getNodeName() + "</b>");
					                    if (i < nChild - 1)
					                    {
					                    	// Separate the names.
					                    	out.print(", ");
					                    }
					                    else
					                    {
					                    	// Finish the sentence.
					                    	out.print(".");
					                    }
					                    	
				                     }
					                 out.println("<br>");
					                 
					                 // Process the child nodes.
					                 for (i=0; i < nChild; i++)
				                     {
					                    Node temp = list.item(i);
					                    displaySAMLInfo(temp, parent + nodeName + "\", out);
				                     }
				               }
			              }
			          }
			      }
			    catch (Exception e)
			    {
			    	System.out.println("Exception encountered.");
			    	e.printStackTrace();	    	
			    }
		    }
	    %>
	
	    <%
	    try 
	    {
		    String data  = (String) request.getAttribute("ACSSAML");
		    
		    DocumentBuilder docBuilder;
			Document doc = null;
			DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
			docBuilderFactory.setIgnoringElementContentWhitespace(true);
			docBuilder = docBuilderFactory.newDocumentBuilder();
			byte[] xmlDATA = data.getBytes();
			
			ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
			doc = docBuilder.parse(in);
			doc.getDocumentElement().normalize();
			
			// Iterate the child nodes of the doc.
	        NodeList list = doc.getChildNodes();
	
	        for (int i=0; i < list.getLength(); i++)
	        {
	        	displaySAMLInfo(list.item(i), "", out);
	        }
		}
	    catch (Exception e) 
	    {
	    	out.println("Exception encountered.");
	    	e.printStackTrace();
		}
	    
	    %>
	</body>
	</html>

## Выполнение приложения

1. Запустите свое приложение в эмуляторе среды выполнения приложений или разверните его в среде Azure, следуя инструкциям раздела [Проверка подлинности веб-пользователей с помощью службы контроля доступа Azure и Eclipse][].
2. Запустите браузер и откройте в нем свое веб-приложение. После того, как вы войдете в приложение, будут показаны данные SAML, включая утверждения безопасности, предоставляемые поставщиком удостоверений.

## Дальнейшие действия

Чтобы продолжить изучение функций ACS и поэкспериментировать с более сложными сценариями, см. раздел [Служба Access Control 2.0][].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Служба Access Control 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Проверка подлинности веб-пользователей с помощью службы контроля доступа Azure и Eclipse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
 

<!---HONumber=AcomDC_0817_2016-->