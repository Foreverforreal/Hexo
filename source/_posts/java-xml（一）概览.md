title: 'java xml（一）概览 '
id: 1497407791507
author: 不识
date: 2017-06-14 10:36:33
tags:
---
Java API for XML Processing (JAXP) 是使用以Java编程语言编写的应用程序处理XML数据。JAXP使用解析器标准Simple API for XML Parsing（SAX）和文档对象模型（DOM），以便你可以选择以事件流的或者构建表示对象的方式来解析数据。JAXP还支持可扩展样式表语言转换（XSLT）标准，使您能够控制数据的呈现，并使您能够将数据转换为其他XML文档或其他格式，例如HTML。JAXP还提供了命名空间支持，允许您使用可能具有命名冲突的DTD。最后，从版本1.4开始，JAXP实现了Streaming API for XML（StAX）标准。

<!-- more -->
为了设计上的最大灵活性，JAXP允许您在应用程序中使用任何兼容XML的解析器。这是通过被称作一种可插拔层来实现的，可以让您插入SAX或DOM API的实现。可插拔层还允许您插入XSL处理器，让您控制XML数据的显示方式。

# 包概览
SAX和DOM API分别由XML-DEV组织和W3C定义。定义了这些API的库如下所示：

- **javax.xml.parsers**: JAXP API，为不同厂商的SAX和DOM解析器提供了一个通用接口。
- **org.w3c.dom**: 定义Document类（一个DOM）以及DOM的所有组件的类。
- **org.xml.sax**: 定义基本的SAX API。
- **javax.xml.transform**: 定义可将XML转换为其他表单的XSLT API。
- **javax.xml.stream**: 提供StAX指定转换API。


Simple API for XML(SAX)是一个事件驱动，序列访问的机制，它以一个元素接一个元素的方式进行处理。该级别的API将XML读取并写入数据存储库或Web中。对于服务器端和高性能应用程序，您需要充分了解此级别。但是对于大多数应用来说，简单的理解就足够了。 
　　DOM API是一个更容易使用的API。它提供了一个熟悉的对象树结构。您可以使用DOM API来操纵其封装的应用程序对象的层次结构。DOM API是交互式应用程序的理想选择，因为整个对象模型都存在于内存中，这样用户可以访问和操作DOM对象。   
　　另一方面，构建DOM需要读取整个XML结构并将对象树保存在内存中，因此它是CPU和内存密集型的。基于这个原因，SAX API倾向用于服务器端应用程序和数据过滤器，它不需要在内存中表示数据。     
　　在javax.xml.transform中定义的XSLT API可以将XML数据写入文件或将其转换为其他形式。如本教程的XSLT部分所示，您甚至可以将其与SAX API结合使用，将旧数据转换为XML。  
　　最后，javax.xml.stream中定义的StAX API提供了一个基于Java技术的流形式，事件驱动，pull式解析的API，用来读取和写入XML文档。StAX提供比SAX更简单的编程模型和比DOM更有效的内存管理。   

# Simple API for XML APIs

AX解析API的基本概要如图1-1所示。要想启动处理，需要一个SAXParserFactory类的实例用于生成解析器的实例。
![jaxpintro-saxApi](/images/java base/jaxpintro-saxApi.gif)

解析器包装一个SAXReader对象。当解析器的parse()方法被调用时，reader调用应用程序中实现的几个回调方法中的一个。这些回调方法由接口ContentHandler，ErrorHandler，DTDHandler和EntityResolver定义。

以下是SAX API的关键摘要：

- **SAXParserFactory**  
SAXParserFactory对象创建解析器的实例，它是由系统属性javax.xml.parsers.SAXParserFactory决定的。 

- **SAXParser**   
SAXParser接口定义了几种parse()方法。 通常，需要传递给解析器一个XML数据源和一个DefaultHandler对象，用来处理XML并在handler对象中调用适当的方法。  
- **SAXReader**   
SAXParser包装一个了SAXReader。通常情况下，您并不需要关注这一点，但是当你需要获取它的时候，使用SAXParser的getXMLReader()，这样您可以对它进行配置。实际上是SAXReader负责与你定义的SAX事件handler进行对话。

- **DefaultHandler**   
图中未显示，DefaultHandler实现了ContentHandler，ErrorHandler，DTDHandler和EntityResolver接口（实现都是空方法），这样你可以只需要重写你需要的方法。
- **ContentHandler**    
当识别XML标签时，会调用诸如startDocument，endDocument，startElement和endElement之类的方法。该接口还定义了character()和processingInstruction()方法，当解析遇到XML元素中的文本或内联处理指令时会调用这两个方法。

- **ErrorHandler**   
当解析遇到各种错误时，会调用error()，fatalError(),和warning()方法。默认的错误handler会为发生的致命错误抛出异常，但是忽略其他错误（包括验证错误）。这是您需要了解有关SAX解析器的一个原因，即使您正在使用DOM。有时，应用程序可能能够从验证错误中恢复。其他时候，它可能需要产生一个异常。为了确保正确的处理，您需要将自己实现的错误处理handler提供给解析器。
- **DTDHandler**   
你通常不会被要求使用它定义的方法。这时用于处理DTD以识别和对未解析实体的声明采取行动。

- **EntityResolver**  
当解析器必须辨别由URI标识的数据时，会调用resolveEntity方法。在大多数情况下，URI只是一个URL，用于指定文档的位置，但在某些情况下，文档可能由网络空间中唯一的URN（公共标识符或名称）标识。除了URL之外，还可以指定公共标识符。然后，EntityResolver可以使用公共标识符而不是URL来查找文档，例如，如果文档存在，则访问文档的本地副本。

一个典型的应用程序至少实现了大部分ContentHandler方法。因为这个接口的默认实现DefaultHandler会忽略除致命错误之外的所有输入，所以一个健壮的实现也可能需要实现ErrorHandler方法。

## SAX 包
SAX解析器在下表中列出的包中定义。

|Packages|Description|
|--------|-----------|
|org.xml.sax|	定义了SAX接口。org.xml是由定义了SAX API的组织确定的包前缀名称。
|org.xml.sax.ext|	定义用于执行更复杂的SAX处理的SAX扩展，例如处理文档类型定义（DTD）或查看文件的详细语法。
|org.xml.sax.helpers|	包含一些使其更容易使用SAX帮助类。比如，通过定义了一个实现所有接口并且都是空方法的默认handler，以便您只需要重写那些实际要实现的方法。
|javax.xml.parsers|	定义SAXParserFactory类，返回SAXParser。还定义了用于报告错误的异常类。

# Document Object Model APIs
下图显示了的DOM API的工作机制。
![jaxpintro-domApi](/images/java base/jaxpintro-domApi.gif)

您可以使用javax.xml.parsers.DocumentBuilderFactory类获取DocumentBuilder实例，并使用该实例来生成符合DOM规范的Document对象。事实上，您获得的builder由系统属性javax.xml.parsers.DocumentBuilderFactory决定，该属性选择用于生成builder的工厂类实现（java平台的默认值可以用命令行覆盖重写）。   
您还可以使用DocumentBuilder newDocument（）方法来创建一个实现org.w3c.dom.Document接口的空Document 对象。或者，您可以使用其中一个builder的解析方法从现有XML数据创建一个Document对象。方法会返回一个如上图所示的DOM树。

> 注意-虽然它们被称为对象，但DOM树中的实体实际上是相当低级的数据结构。例如，考虑这个结构：&lt;color> blue </ color>。这是一个color标签的元素节点，并且在它里面有一个包含数据blue的文本节点。这个问题将在本教程的DOM课程中详细探讨，但是开发人员期望返回的对象，会惊讶的发现当在元素节点上调用getNodeValue（）方法却不会返回任何内容。对于一个真正面向对象的树，请参阅http://www.jdom.org 上的JDOM API。

## DOM包

|Package|Description|
|-------|-----------|
|org.w3c.dom|	定义W3C指定的XML（和可选的HTML）文档的DOM编程接口。
|javax.xml.parsers|定义DocumentBuilderFactory类和DocumentBuilder类，它返回一个实现W3C Document接口的对象。用于创建builder的factroy由javax.xml.parsers系统属性决定，该属性可以在调用新的实例方法时从命令行设置或覆盖。此包还定义了用于报告错误的ParserConfigurationException类。

# Extensible Stylesheet Language Transformations APIs

下图显示了XSLT API的操作
![jaxpintro-xsltApi](/images/java base/jaxpintro-xsltApi.gif)

TransformerFactory对象被实例化并用于创建Transformer。Source对象是转换过程的输入。Source对象可以由SAX reader，DOM或从输入流创建。
类似地，Result对象是转换过程的结果。该对象可以是SAX事件处理程序，DOM或输出流。
当创建transformer时，可以从一组转换指令中来创建transformer，在这种情况下，执行指定的转换。如果不是由任何特定的指令来创建，那么transformer对象只是简单的把source复制到result中。

## XSLT包

|Package|	Description|
|-------|--------------|
|javax.xml.transform|定义TransformerFactory和Transformer类，您可以使用它来获取能够进行转换的对象。创建transformer 对象后，调用其transform（）方法，并且为其提供一个输入（源）和一个输出（结果）。
|javax.xml.transform.dom|从DOM创建输入（源）和输出（结果）对象的类。
|javax.xml.transform.sax|从SAX解析器创建输入（源）对象并从SAX事件handler创建输出（结果）对象的类。
|javax.xml.transform.stream|从I / O流创建输入（源）对象和输出（结果）对象的类。

# Streaming API for XML APIs

StAX是JAXP系列中最新的API，为希望能进行高性能流式过滤，处理和修改的开发人员提供一个SAX，DOM，TrAX和DOM的替代方案，特别是对于低内存和有限的可扩展性要求。
总之，StAX为流式XML处理提供了一个标准的双向pull式解析器接口，提供比SAX更简单的编程模型和比DOM更有效的内存管理。StAX使开发人员以事件形式解析和修改XML流，并扩展XML信息模型以允许特定于应用程序的添加。

## StAX 包

|Package|Description|
|-------|-----------|
|javax.xml.stream|定义XMLStreamReader接口，用于迭代XML文档的元素。 XMLStreamWriter接口指定如何写入XML。
|javax.xml.transform.stax|提供特定于StAX的转换API。

## API选择
针对以上各种解析模式，下面给出了一个选择何种解析方式的简单指导。
- 如果数据结构已经确定，并且正在编写需要快速处理的服务器应用程序或XML过滤器，使用Simple API for XML.
- 如果您需要从XML数据构建一个对象树，以便您可以在应用程序中进行操作，或将内存中的对象树转换为XML，使用Document Object Model.
- 如果您需要将XML标签转换为其他形式，如果要生成XML输出，或者（与SAX API结合使用）想要将旧数据结构转换为XML，使用Extensible Stylesheet Language Transformations.
- 如果您想要一种基于Java技术的流形式的，基于事件驱动的pull式解析API来读取和写入XML文档，或者想要创建快速，相对易于编程和轻量级内存占用的双向XML解析器，使用Streaming API for XML.



