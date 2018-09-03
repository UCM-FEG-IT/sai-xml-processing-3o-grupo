## Jargon

**Child Node**
A child node or descendant node is a node in a tree data structure that is linked to by a parent node.

**Element**
A block in a markup document like a tag in an HTML document.

**Parse**
Parsing is the process of splitting up a continuous stream of characters (read from a file or keyboard input, for example) into meaningful tokens, and then building a parse tree from those tokens.

**Serialize**
Encode a data structure as a sequence of bytes.

**Stream**
A continuous flow of data, usually digitally encoded, designed to be processed sequentially. Also called a bitstream.

**String**
A group or sequence of characters.

**Stylesheet**
An ASCII text document that is attached to an SGML or XML encoded document and that contains instructions to specify the formatting and display of the encoded document, or to transform it into another format.

**Template**
A set of pre-designed formats for text and graphics on which new pages and webs can be based.

**Text**
Data consisting of a sequence of characters, as opposed to binary numbers, images, graphics commands, executable programs, and the like.

**Transformation**
Transformation is a form of conversion in which a file is converted into a file format with a comparable structure (eg from XML to XML, or from SGML to HTML). Usually, this form of conversion can be carried out very well.

## Introduction

We were briefly introduced to XML in chapter 6. In this chapter we are going to look at working with XML in our programs, in effect we will be writing XML (creating), reading XML (parsing) and performing transformations on XML (from XML to HTML).

In this chapter we will be looking at two tools instead of one like previous chapters. We will first look at the Xerces XML parser and then look at Xalan, a tool that performs XML transformations.

You can get Xerces and Xalan @  [xml.apache.org](http://xml.apache.org/). Note that we are using Xerces-J for Java.

## Writing XML

The first thing we may wish to do is to actually create an XML document. In this section we will look at some ways in which to accomplish this.

### Three Ways To Write XML

There are actually three ways in which to create an XML document. The first is the easiest, we simply type an XML document into our favourite text editor and then save the document with the  .xml  extension. The second way is to actually hardcode the XML into our program and the last method is to actually create a Document object and then serialize it.

Consider the example given in chapter 6 of a memo. It is printed here again:

<?xml version="1.0" ?>

<!DOCTYPE memo
    SYSTEM "memo.dtd">
    
<memo>
    <to>John</to>
    <subject>Re: Progress Meeting</subject>
    <message>Don't forget that we must book the boardroom
        well in advance or we'll have to have the meeting in
        one of our offices which will turn out to be
        <emphasis>very cramped</emphasis>.
        See you later!
    </message>
    <from>Trevor</from>
</memo>
                

You can quite easily copy the text, paste it into your text editor and save it. This is the easiest way in which to create an XML document. But what if we want to create a document from within our program?

### Programmatically

Programmatically creating an XML document is almost as easy as creating one in a text editor. For starters we can simply print out the document to the console:

System.out.println("<?xml version=\"1.0\" ?>");
System.out.println("");
System.out.println("<!DOCTYPE memo SYSTEM \"memo.dtd\">");
System.out.println("");
System.out.println("<memo>");
System.out.println("    <to>John</to>");
System.out.println("    <subject>Re: Progress Meeting</subject>");
System.out.println("    <message>Don't forget that we must book the boardroom");
System.out.println("        well in advance or we'll have to have the meeting in");
System.out.println("        one of our offices which will turn out to be");
System.out.println("        <emphasis>very cramped</emphasis>.");
System.out.println("        See you later!");
System.out.println("    </message>");
System.out.println("    <from>Trevor</from>");
System.out.println("</memo>");
                

This will print out the document to the console. What if we wish to send that to a file? This is done quite easily using Java's IO system.

try {
    PrintWriter out = new PrintWriter(new FileOutputStream("memo.xml"));
    out.println("<?xml version=\"1.0\" ?>");
    ...
    out.println("</memo>");
    out.close();
} catch (Exception e) {
    e.printStackTrace();
}
                

Many people are against hard coding the document, instead the correct way to do this is to create the document object.

### Creating The Document Object

#### The Xerces Parser

The Xerces parser is distributed in a compressed archive. Simply extract the archive to the location of your choice. Within the contents of the archive you will find two jar files that are required, namely:  xercesImpl.jar  and  xml-apis.jar. You need to add both to your classpath.

#### Creating The Document

The first thing we need to do is to actually create the document object through the use of a document builder. We first get a  javax.xml.parsers.DocumentBuilderFactoryinstance and use it to create a  javax.xml.parsers.DocumentBuilder  which in turn is used to create the  org.w3c.dom.Document  instance.

DocumentBuilderFactory builderFactory = 
    DocumentBuilderFactory.newInstance();
DocumentBuilder builder = builderFactory.newDocumentBuilder();
Document document = builder.newDocument();
                    

#### Creating The Root Element

Next we create the root element of the document. This is done through the  createElement  method of the  document  instance which returns a  org.w3c.dom.Element.

Element root = document.createElement("memo");
                    

#### Creating Additional Elements

We now create the  to  and  subject  elements. This is again done using the  createElement  method of the  document  instance. In each case we need to add a sub element which is a text node. Text elements contain the actual data in the tag.

Element item = document.createElement("to");
item.appendChild(document.createTextNode("John"));
root.appendChild(item);

item = document.createElement("subject");
item.appendChild(document.createTextNode("Re: Progress Meeting"));
root.appendChild(item);
                    

#### Creating The Message Element

The  message  element is interesting in that it contains both text and an  emphasis  subelement. In this instance we have three child elements, the first text element, the  emphasis  element and the second text element.

item = document.createElement("message");
item.appendChild(document.createTextNode("Don\'t forget that we "
    + "must book the boardroom well in advance or we'll have "
    + "to have the meeting in one of our offices which will "
    + "turn out to be ") );            
Element subitem = document.createElement("emphasis");
subitem.appendChild(document.createTextNode("very cramped"));
item.appendChild(subitem);
item.appendChild(document.createTextNode(". See you later!"));
root.appendChild(item);
                    

#### Adding The Root Element

We add the  from  element and then add the root element to the document.

item = document.createElement("from");
item.appendChild(document.createTextNode("Trevor"));
root.appendChild(item);

document.appendChild(root);
                    

#### Output The Document

In order to output the document we use an  org.apache.xml.serialize.XMLSerializer  instance which requires an  org.apache.xml.serialize.OutputFormatinstance and a writer object.

OutputFormat format = new OutputFormat(document);
PrintWriter  writer = 
    new PrintWriter(new FileOutputStream("memo.xml"));
XMLSerializer serializer = new XMLSerializer(writer, format);
serializer.asDOMSerializer();
serializer.serialize(document.getDocumentElement());
                    

You can get the complete source code for this chapter  [here](http://javaworkshop.sourceforge.net/chapter9.zip).

## Reading XML

In this section we will look at ways to read an xml document. This is typically known as parsing XML.

### Two Ways To Read XML

When one wishes to parse an XML document one will typically use an XML Parser for this purpose. There are two ways of parsing an XML document, one is through the use of Simple API for XML (SAX) and the other is using the Document Object Model (DOM).

SAX is easy to implement and easy to understand. Its is also very quick to parse an XML Document. The way sax works is that it parses the document in a serial fashion, so you get each element as it is parsed. This means that once an element is parsed you cannot go back to it and you cannot read elements that occur later in the document in random access fashion.

DOM allows you to access any element in the document in random access fashion. You first parse the entire document and keep it in memory then you can read elements from it. DOM is a bit more difficult to implement and does not work well with extremely large documents. It is better suited to small documents like our memo. Keep the differences between SAX and DOM in mind as we explore them.

### Simple API for XML (SAX)

We said that SAX parses the document in a serial fashion, one element at a time. How this works is as follows, as soon as an element is found an event occurs and any event handlers associated with that event are fired. Your program will typically inherit from one of the handler classes or implement the  Handler  interface which defines these event handlers.

We are going to use SAX to read in our memo document from a file named "memo.xml" and print out the memo. We first define our class SAXReader to implement ContentHandler and implement all the required methods. The method bodies are all empty.

public class SAXReader implements ContentHandler {
    public void setDocumentLocator(Locator arg0) {}
    public void startDocument() throws SAXException {}
    public void endDocument() throws SAXException {}
    public void startPrefixMapping(String arg0, 
        String arg1) throws SAXException {}
    public void endPrefixMapping(String arg0) 
        throws SAXException {}
    public void startElement(String uri, String local, 
        String raw, Attributes attrs) throws SAXException {}
    public void endElement(String arg0, String arg1, 
        String arg2) throws SAXException {}
    public void characters(char[] ch, int start, 
        int length) throws SAXException {}
    public void ignorableWhitespace(char[] arg0, 
        int arg1, int arg2) throws SAXException {}
    public void processingInstruction(String arg0, 
        String arg1) throws SAXException {}
    public void skippedEntity(String arg0) throws SAXException {}
}
                

The class should also include a main method since this is our program. In the main method we create an  org.xml.sax.XMLReader  instance using the  org.xml.sax.helpers.XMLReaderFactory. We set its content handler to our class and then parse the file.

public static void main(String[] args) {
    try {
        XMLReader parser = 
            XMLReaderFactory.createXMLReader(
				"org.apache.xerces.parsers.SAXParser");
        parser.setContentHandler(new SAXReader());
        parser.parse("memo.xml");
    } catch (Exception e) {
        e.printStackTrace();
    }
}
                

Finally we implement two of the methods in order to print out our memo. We print the name of each element in the  startElement  method and then we print out the character data for each text node.

public void startElement(String uri, String local, 
        String raw, Attributes attrs) throws SAXException {
    System.out.print(local + ": ");
}

public void characters(char[] ch, int start, 
        int length) throws SAXException {
    char[] array = new char[length];
    System.arraycopy(ch, start, array, 0, length);
    System.out.print(array);
}
                

When we run the program, we get the following output:

memo: 
	to: John
	subject: Re: Progress Meeting
	message: Don't forget that we must book the boardroom
		well in advance or we'll have to have the meeting in 
		one of our offices which will turn out to be 
		emphasis: very cramped. 
		See you later!
	from: Trevor
                

### Document Object Model (DOM)

Using DOM, we first parse the entire document into memory before we can use it. Using the document means actually using the set of interfaces and classes in the DOM API. We will use these to print out the memo we parse in from file.

We can write a printNode method that is responsible for printing out the Node and then recursively calls the method to print each child node. This is fairly straight forward.

public static void printNode(Node node) {
    if (node == null) return;

    int type = node.getNodeType();
    if (type == Node.TEXT_NODE) {
        Text text = (Text)node;
        System.out.println(text.getWholeText());
    } else if (type == Node.ELEMENT_NODE) {
        Element element = (Element) node;
        System.out.print(element.getTagName() +  ": ");
			
        NodeList children = node.getChildNodes();
        for (int i = 0; i < children.getLength(); i++)
            printNode(children.item(i));
    }
}
                

The last thing we need to do is to define our program's main method. This follows a similar pattern to writing out the XML using DOM. We use a DOM builder to actually parse the XML. After the document is parsed, we call the  printNode  method using the document's root element.

public static void main(String[] args) {
    try {
        DocumentBuilderFactory builderFactory = 
            DocumentBuilderFactory.newInstance();
        DocumentBuilder builder = builderFactory.newDocumentBuilder();
        Document document = builder.parse("memo.xml");
        printNode(document.getDocumentElement());
    } catch (Exception e) {
        e.printStackTrace();
    }
}
                

## Transforming XML

XML is designed to be a format for structuring data and not for presenting data. Countless other formats exist that focus on presenting data such as HTML. We may want to transform our XML into a format that is more capable of presenting the data.

In order to transform our XML we make use of another tool called eXtensible Stylesheet Laguage. Using XSL we are going to transform our XML memo into an HTML version. We will be using  **Xalan**  to do the transformation for us.

### eXtensible Stylesheet Language (XSL)

An XSL stylesheet is a document that is defined in XML and describes the process of transforming an XMLdocument into another format. It does this by using templates, typically a template would match a specific element in the XML document and describe how to convert that XML element into the desired format.

Lets get started by defining our stylesheet document.

<?xml version="1.0"?>
<xsl:stylesheet version="1.0" 
    xmlns:xsl="http://www.w3.org/1999/XSL/Transform">

</xsl:stylesheet>
                

As you can see we define an XML document using the XML declaration and then declare that this document is an xsl stylesheet using the  xsl:stylesheet  declaration. All XSL stylesheets are defined in this way.

The next thing we do is to define a template that matches the root element. The root element of the document is used to create the output document, in this case the HTML docuement, so in this template we define an HTML document. We also use the  xsl:apply-templates  instruction to continue processing.

<xsl:template match="/">
    <HTML>
        <HEAD>
            <TITLE>Memo</TITLE>
        </HEAD>
        <BODY>
            <H2>Memo</H2>
            <TABLE>
                <xsl:apply-templates/>
            </TABLE>
        </BODY>            
    </HTML>
</xsl:template>
                

A number of people code their HTML in lowercase, I prefer to use uppercase in order to distinguish between the XML for the stylesheet and the HTML output.

The templates for the  to,  from  and  subject  elements are given next.

<xsl:template match="to">
    <TR>
        <TD>To:</TD>
        <TD><xsl:value-of select="."/></TD>
    </TR>
</xsl:template>    
    
<xsl:template match="from">
    <TR>
        <TD>From:</TD>
        <TD><xsl:value-of select="."/></TD>
    </TR>
</xsl:template>    
    
<xsl:template match="subject">
    <TR>
        <TD>Subject:</TD>
        <TD><xsl:value-of select="."/></TD>
    </TR>
</xsl:template>
                

The  xsl:value-of select="."  instruction is used to print the actual text content of the XML element. Because the message element does not contain only text elements but other elements, namely the  emphasis  element, we cannot use this instruction. Instead we tell the transformer to continue processing which prints the text and processes other elements.

<xsl:template match="message">
    <TR>
        <TD>Message:</TD>
        <TD><xsl:apply-templates/></TD>
    </TR>
</xsl:template>
                

The last template is for the emphasis element:

<xsl:template match="emphasis">
    <EM><xsl:value-of select="."/></EM>
</xsl:template>
                

### Performing the Transformation

Xalan is distributed as a compressed archive, once you have uncompressed the archive to the location of your choice you will see that it contains a number of jar files, You will need all these jar files on your classpath.

We use a  javax.xml.transform.Transformer  to do the transformation for us. We need to create an instance using a  javax.xml.transform.TransformerFactory. We load the XML and XSL as  javax.xml.transform.stream.StreamSource  objects and write the output to a  javax.xml.transform.stream.StreamResult.

TransformerFactory factory = TransformerFactory.newInstance();
StreamSource stylesheet = new StreamSource("xml2html.xsl");
Transformer transformer = factory.newTransformer(stylesheet);
StreamSource source = new StreamSource("memo.xml");
StreamResult result = 
    new StreamResult(new FileOutputStream("memo.html"));
transformer.transform(source, result);
                

After running the program we end up with the file named "memo.html" which is an HTML version of the memo.

## Summary

XML is widely used for a number of purposes since it is easily read and understood by both humans and computers. XML can be written, read and transformed into different formats. DOM can be used to write and read XML while SAX provides a method for reading XML. XSL is used to transform XML.

## Exercise Set

### Multiple Choice

1.  XML stands for eXtensible Markup Language
    
    1.  True
    2.  False
2.  Possible ways to write XML are:
    
    1.  Manually with a text editor.
    2.  Programmtically: Hard-Coded
    3.  Programmtically: Using a Document Object
    4.  All the above
    5.  None of the above
3.  When writing to a file you will use
    
    1.  java.io.OutputStream
    2.  java.io.Writer
    3.  java.io.PrintWriter
    4.  java.io.FileOutputStream
    5.  1 and 2
    6.  3 and 4
4.  To get a document builder you need to
    
    1.  Create a builder factory and get a document builder instance.
    2.  Create a document builder instance.
    3.  Get a new instance of a builder factory and get a new document builder from it.
5.  Every document contains a single root element.
    
    1.  True
    2.  False
6.  To create a new element:
    
    1.  Create a new instance using a constructor.
    2.  Use the  createElement  method on a document object.
7.  To output a document you use an XMLSerializer.
    
    1.  True
    2.  False
8.  Possible ways to read XML are:
    
    1.  Programmatically
    2.  Using SAX
    3.  Using DOM
    4.  None of the above
    5.  2 and 3
9.  For a large document, you would parse it using
    
    1.  SAX
    2.  DOM
    3.  Neither
10.  What XSL instruction is used to retrieve the text of an xml element?
    
    1.  <xsl:template match="to">
    2.  <xsl:value-of select="."/>
    3.  <xsl:apply-templates/>

### Exercises

1.  Find out about the  DefaultHandler  and how it differs from  ContentHandler.

### Programming Exercises

1.  Complete the following:
    1.  Write a program to generate a memo in xml format and save it to a file.
    2.  Now write a program to display the contents of this xml memo.
    3.  Write a program to transform the memo into HTML and raw text.
2.  Implement a SAX parser using  DefaultHandler  instead of  ContentHandler.
