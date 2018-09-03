# Tutorial Processamento de documento XML usando Java

## Jargon

** Nó Filho **
Um nó filho ou nó descendente é um nó em uma estrutura de dados de árvore que está vinculada a um nó pai.

**Elemento**
Um bloco em um documento de marcação como uma tag em um documento HTML.

** Analisar **
A análise é o processo de dividir um fluxo contínuo de caracteres (lidos de um arquivo ou entrada de teclado, por exemplo) em tokens significativos e, em seguida, criar uma árvore de análise desses tokens.

** Serialize **
Codifique uma estrutura de dados como uma seqüência de bytes.

**Corrente**
Um fluxo contínuo de dados, normalmente digitalmente codificado, projetado para ser processado seqüencialmente. Também chamado de fluxo de bits.

**Corda**
Um grupo ou sequência de caracteres.

** Folha de estilo **
Um documento de texto ASCII anexado a um documento codificado em SGML ou XML e que contém instruções para especificar a formatação e a exibição do documento codificado ou para transformá-lo em outro formato.

**Modelo**
Um conjunto de formatos predefinidos para texto e gráficos nos quais novas páginas e webs podem ser baseadas.

**Texto**
Dados consistindo de uma sequência de caracteres, em oposição a números binários, imagens, comandos gráficos, programas executáveis ​​e semelhantes.

**Transformação**
Transformação é uma forma de conversão na qual um arquivo é convertido em um formato de arquivo com uma estrutura comparável (por exemplo, de XML para XML ou de SGML para HTML). Normalmente, esta forma de conversão pode ser realizada muito bem.

## Introdução

Fomos apresentados brevemente ao XML no capítulo 6. Neste capítulo, vamos analisar o trabalho com XML em nossos programas, de fato estaremos escrevendo XML (criando), lendo XML (parsing) e realizando transformações em XML (a partir de XML para HTML).

Neste capítulo, veremos duas ferramentas em vez de uma como nos capítulos anteriores. Vamos primeiro olhar para o analisador Xerces XML e, em seguida, olhar para Xalan, uma ferramenta que realiza transformações XML.

Você pode obter Xerces e Xalan @ [xml.apache.org] (http://xml.apache.org/). Note que estamos usando o Xerces-J para Java.

## Writing XML

A primeira coisa que podemos desejar é criar um documento XML. Nesta seção, veremos algumas maneiras de realizar isso.

### Três maneiras de escrever XML

Na verdade, existem três maneiras de criar um documento XML. O primeiro é o mais fácil, simplesmente digitamos um documento XML em nosso editor de texto favorito e depois salvamos o documento com a extensão .xml. A segunda maneira é realmente codificar o XML em nosso programa e o último método é realmente criar um objeto Document e depois serializá-lo.

Considere o exemplo dado no capítulo 6 de um memorando. Está impresso aqui novamente:

<? xml version = "1.0">

<! DOCTYPE memo
    SISTEMA "memo.dtd">
    
<memo>
    <to> John </ a>
    <subject> Re: Reunião de progresso </ subject>
    <message> Não se esqueça de que precisamos reservar a sala de reuniões
        com bastante antecedência ou teremos que ter a reunião em
        um dos nossos escritórios, que acabará por ser
        <emphasis> muito apertado </ emphasis>.
        Até logo!
    </ message>
    <a partir de> Trevor </ from>
</ memo>
                

Você pode facilmente copiar o texto, colá-lo em seu editor de texto e salvá-lo. Essa é a maneira mais fácil de criar um documento XML. Mas e se quisermos criar um documento dentro do nosso programa?

### Programaticamente

Criar programaticamente um documento XML é quase tão fácil quanto criar um em um editor de texto. Para começar, podemos simplesmente imprimir o documento para o console:

System.out.println ("<? Xml version = \" 1.0 \ "?>");
System.out.println ("");
System.out.println ("<! DOCTYPE memo SYSTEM \" memo.dtd \ ">");
System.out.println ("");
System.out.println ("<memo>");
System.out.println ("<para> John </ to>");
System.out.println ("<assunto> Re: reunião de progresso </ subject>");
System.out.println ("<message> Não se esqueça que devemos reservar a sala de reuniões");
System.out.println ("com bastante antecedência ou teremos a reunião em");
System.out.println ("um dos nossos escritórios, que acabará por ser");
System.out.println ("<emphasis> muito apertado </ emphasis>.");
System.out.println ("Até logo!");
System.out.println ("</ message>");
System.out.println ("<from> Trevor </ from>");
System.out.println ("</ memo>");
                

Isso imprimirá o documento no console. E se quisermos enviar isso para um arquivo? Isso é feito facilmente usando o sistema IO do Java.

experimentar {
    PrintWriter out = new PrintWriter (novo FileOutputStream ("memo.xml"));
    out.println ("<? xml version = \" 1.0 \ "?>");
    ...
    out.println ("</ memo>");
    out.close ();
} catch (exceção e) {
    e.printStackTrace ();
}
                

Muitas pessoas são contra a codificação do documento, em vez disso, a maneira correta de fazer isso é criar o objeto do documento.

### Criando o objeto de documento

#### O analisador Xerces

O analisador Xerces é distribuído em um archive compactado. Basta extrair o arquivo para o local de sua escolha. Dentro do conteúdo do arquivo, você encontrará dois arquivos jar necessários, a saber: xercesImpl.jar e xml-apis.jar. Você precisa adicionar ambos ao seu classpath.

#### Criando o documento

A primeira coisa que precisamos fazer é realmente criar o objeto de documento através do uso de um construtor de documentos. Primeiramente, obtemos uma javax.xml.parsers.DocumentBuilderFactoryinstance e a usamos para criar um javax.xml.parsers.DocumentBuilder que, por sua vez, é usado para criar a instância org.w3c.dom.Document.

DocumentBuilderFactory builderFactory =
    DocumentBuilderFactory.newInstance ();
DocumentBuilder builder = builderFactory.newDocumentBuilder ();
Document document = builder.newDocument ();
                    

#### Criando o elemento raiz

Em seguida, criamos o elemento raiz do documento. Isso é feito através do método createElement da instância do documento que retorna um org.w3c.dom.Element.

Raiz do elemento = document.createElement ("memo");
                    

#### Criando elementos adicionais

Agora criamos os elementos to e subject. Isso é feito novamente usando o método createElement da instância do documento. Em cada caso, precisamos adicionar um sub-elemento que é um nó de texto. Elementos de texto contêm os dados reais na tag.

Elemento item = document.createElement ("to");
item.appendChild (document.createTextNode ("John"));
root.appendChild (item);

item = document.createElement ("subject");
item.appendChild (document.createTextNode ("Re: Progress Meeting"));
root.appendChild (item);
                    

#### Criando o elemento de mensagem

O elemento de mensagem é interessante na medida em que contém texto e um subelemento de ênfase. Neste exemplo, temos três elementos filho, o primeiro elemento de texto, o elemento de ênfase e o segundo elemento de texto.

item = document.createElement ("message");
item.appendChild (document.createTextNode ("Não se esqueça que nós"
    + "deve reservar a sala de reuniões com antecedência ou teremos"
    + "ter a reunião em um de nossos escritórios que"
    + "revelar-se"));
Elemento subitem = document.createElement ("emphasis");
subitem.appendChild (document.createTextNode ("very cramped"));
item.appendChild (subitem);
item.appendChild (document.createTextNode (". Até mais!"));
root.appendChild (item);
                    

#### Adicionando o elemento raiz

Adicionamos o elemento from e adicionamos o elemento raiz ao documento.

item = document.createElement ("from");
item.appendChild (document.createTextNode ("Trevor"));
root.appendChild (item);

document.appendChild (root);
                    

#### Saída do documento

Para produzir o documento, usamos uma instância org.apache.xml.serialize.XMLSerializer que requer um org.apache.xml.serialize.OutputFormatinstance e um objeto writer.

Formato FormatFormat = new OutputFormat (documento);
PrintWriter writer =
    novo PrintWriter (novo FileOutputStream ("memo.xml"));
Serializador XMLSerializer = new XMLSerializer (escritor, formato);
serializer.asDOMSerializer ();
serializer.serialize (document.getDocumentElement ());
                    

Você pode obter o código-fonte completo deste capítulo [aqui] (http://javaworkshop.sourceforge.net/chapter9.zip).

## Reading XML

Nesta seção, veremos maneiras de ler um documento xml. Isso é normalmente conhecido como análise XML.

### Duas maneiras de ler XML

Quando se deseja analisar um documento XML, normalmente é utilizado um analisador XML para esse fim. Há duas maneiras de analisar um documento XML, uma é através do uso de API simples para XML (SAX) e a outra é usando o Document Object Model (DOM).

O SAX é fácil de implementar e fácil de entender. É também muito rápido para analisar um documento XML. A forma como o sax funciona é que ele analisa o documento de maneira serial, então você obtém cada elemento conforme é analisado. Isso significa que, depois que um elemento é analisado, você não pode voltar a ele e não pode ler os elementos que ocorrem posteriormente no documento, de forma aleatória.

DOM permite que você acesse qualquer elemento no documento de forma aleatória. Você primeiro analisa o documento inteiro e o mantém na memória, então você pode ler elementos dele. O DOM é um pouco mais difícil de implementar e não funciona bem com documentos extremamente grandes. É mais adequado para pequenos documentos como o nosso memorando. Mantenha as diferenças entre o SAX e o DOM em mente enquanto as exploramos.

### API simples para XML (SAX)

Dissemos que o SAX analisa o documento de maneira serial, um elemento por vez. Como isso funciona é o seguinte, assim que um elemento é encontrado, um evento ocorre e todos os manipuladores de eventos associados a esse evento são disparados. Seu programa normalmente herdará de uma das classes manipuladoras ou implementará a interface do Manipulador que define esses manipuladores de eventos.

Vamos usar o SAX para ler em nosso documento de memorando a partir de um arquivo chamado "memo.xml" e imprimir o memorando. Primeiro definimos nosso SAXReader de classe para implementar o ContentHandler e implementar todos os métodos necessários. Os corpos do método estão todos vazios.

public class SAXReader implementa ContentHandler {
    public void setDocumentLocator (Locator arg0) {}
    public void startDocument () lança SAXException {}
    public void endDocument () lança SAXException {}
    public void startPrefixMapping (String arg0,
        String arg1) lança SAXException {}
    public void endPrefixMapping (String arg0)
        lança SAXException {}
    public void startElement (String uri, String local,
        Cadeia raw, Atributos attrs) lança SAXException {}
    public void endElement (String arg0, String arg1,
        String arg2) lança SAXException {}
    caracteres vazios públicos (char [] ch, int start,
        int length) lança SAXException {}
    public void ignorableWhitespace (char [] arg0,
        int arg1, int arg2) lança SAXException {}
    public void processingInstruction (String arg0,
        String arg1) lança SAXException {}
    public void skippedEntity (String arg0) lança SAXException {}
}
                

A turma também deve incluir um método principal, já que este é o nosso programa. No método principal, criamos uma instância org.xml.sax.XMLReader usando o org.xml.sax.helpers.XMLReaderFactory. Nós definimos seu manipulador de conteúdo para nossa classe e depois analisamos o arquivo.

public static void main (String [] args) {
    experimentar {
        Analisador XMLReader =
            XMLReaderFactory.createXMLReader (
"org.apache.xerces.parsers.SAXParser");
        parser.setContentHandler (new SAXReader ());
        parser.parse ("memo.xml");
    } catch (exceção e) {
        e.printStackTrace ();
    }
}
                

Por fim, implementamos dois dos métodos para imprimir nosso memorando. Imprimimos o nome de cada elemento no método startElement e depois imprimimos os dados de caractere para cada nó de texto.

public void startElement (String uri, String local,
        String raw, Atributos attrs) gera SAXException {
    System.out.print (local + ":");
}

caracteres vazios públicos (char [] ch, int start,
        int length) lança SAXException {
    char [] matriz = novo char [comprimento];
    System.arraycopy (ch, start, array, 0, comprimento);
    System.out.print (array);
}
                

Quando executamos o programa, obtemos a seguinte saída:

memorando:
para: John
assunto: Re: Reunião de progresso
mensagem: não se esqueça de que devemos reservar a sala de reuniões
com bastante antecedência ou teremos que ter a reunião em
um dos nossos escritórios, que acabará por ser
ênfase: muito apertado.
Até logo!
de: Trevor
                

### Document Object Model (DOM)

Usando o DOM, primeiro analisamos o documento inteiro na memória antes de usá-lo. Usar o documento significa realmente usar o conjunto de interfaces e classes na API do DOM. Vamos usá-los para imprimir o memorando que analisamos no arquivo.

Podemos escrever um método printNode que é responsável pela impressão do nó e, em seguida, chama recursivamente o método para imprimir cada nó filho. Isso é bastante direto.

public static void printNode (Node node) {
    if (node ​​== null) retornar;

    int type = node.getNodeType ();
    if (type == Node.TEXT_NODE) ​​{
        Texto text = nó (texto);
        System.out.println (text.getWholeText ());
    } else if (type == Node.ELEMENT_NODE) ​​{
        Elemento element = (Element) nó;
        System.out.print (element.getTagName () + ":");

        NodeList children = node.getChildNodes ();
        para (int i = 0; i <children.getLength (); i ++)
            printNode (children.item (i));
    }
}
                

A última coisa que precisamos fazer é definir o método principal do nosso programa. Isso segue um padrão semelhante ao escrever o XML usando o DOM. Usamos um construtor DOM para realmente analisar o XML. Depois que o documento é analisado, chamamos o método printNode usando o elemento raiz do documento.

public static void main (String [] args) {
    experimentar {
        DocumentBuilderFactory builderFactory =
            DocumentBuilderFactory.newInstance ();
        DocumentBuilder builder = builderFactory.newDocumentBuilder ();
        Document document = builder.parse ("memo.xml");
        printNode (document.getDocumentElement ());
    } catch (exceção e) {
        e.printStackTrace ();
    }
}
                

## Transformando XML

XML é projetado para ser um formato para estruturar dados e não para apresentar dados. Existem inúmeros outros formatos que se concentram em apresentar dados como HTML. Podemos querer transformar nosso XML em um formato mais capaz de apresentar os dados.

Para transformar nosso XML, usamos outra ferramenta chamada eXtensible Stylesheet Laguage. Usando o XSL, vamos transformar nosso memorando XML em uma versão HTML. Nós estaremos usando o ** Xalan ** para fazer a transformação para nós.

### eXtensible Stylesheet Language (XSL)

Uma folha de estilo XSL é um documento definido em XML e descreve o processo de transformação de um documento XML em outro formato. Ele faz isso usando modelos, geralmente um modelo corresponderia a um elemento específico no documento XML e descreveria como converter esse elemento XML no formato desejado.

Vamos começar definindo o nosso documento de estilo.

<? xml version = "1.0">
<xsl: versão da folha de estilos = "1.0"
    xmlns: xsl = "http://www.w3.org/1999/XSL/Transform">

</ xsl: stylesheet>
                

Como você pode ver, definimos um documento XML usando a declaração XML e declaramos que este documento é uma folha de estilo xsl usando a declaração xsl: stylesheet. Todas as folhas de estilo XSL são definidas dessa maneira.

A próxima coisa que fazemos é definir um modelo que corresponda ao elemento raiz. O elemento raiz do documento é usado para criar o documento de saída, neste caso a documentação HTML, portanto, neste modelo, definimos um documento HTML. Também usamos a instrução xsl: apply-templates para continuar o processamento.

<xsl: template match = "/">
    <HTML>
        <HEAD>
            <TITLE> Memorando </ TITLE>
        </ HEAD>
        <BODY>
            <H2> Memo </ H2>
            <TABLE>
                <xsl: apply-templates />
            </ TABLE>
        </ BODY>
    </ HTML>
</ xsl: template>
                

Um número de pessoas codifica seu HTML em minúsculas, eu prefiro usar letras maiúsculas para distinguir entre o XML para a folha de estilo e a saída HTML.

Os modelos para os elementos to, from e subject são dados a seguir.

<xsl: template match = "to">
    <TR>
        <TD> Para: </ TD>
        <TD> <xsl: value-of select = "." /> </ TD>
    </ TR>
</ xsl: template>
    
<xsl: template match = "from">
    <TR>
        <TD> De: </ TD>
        <TD> <xsl: value-of select = "." /> </ TD>
    </ TR>
</ xsl: template>
    
<xsl: template match = "subject">
    <TR>
        <TD> Assunto: </ TD>
        <TD> <xsl: value-of select = "." /> </ TD>
    </ TR>
</ xsl: template>
                

O xsl: value-of select = "." instrução é usada para imprimir o conteúdo de texto real do elemento XML. Como o elemento de mensagem não contém apenas elementos de texto, mas outros elementos, a saber, o elemento de ênfase, não podemos usar esta instrução. Em vez disso, dizemos ao transformador para continuar o processamento que imprime o texto e processa outros elementos.

<xsl: template match = "message">
    <TR>
        <TD> Mensagem: </ TD>
        <TD> <xsl: apply-templates /> </ TD>
    </ TR>
</ xsl: template>
                

O último modelo é para o elemento de ênfase:

<xsl: template match = "emphasis">
    <EM> <xsl: value-of select = "." /> </ EM>
</ xsl: template>
                

### Realizando a transformação

O Xalan é distribuído como um arquivo compactado, depois de descompactar o arquivo no local de sua escolha, você verá que ele contém vários arquivos jar. Você precisará de todos esses arquivos jar no seu caminho de classe.

Nós usamos um javax.xml.transform.Transformer para fazer a transformação para nós. Precisamos criar uma instância usando um javax.xml.transform.TransformerFactory. Carregamos o XML e o XSL como objetos javax.xml.transform.stream.StreamSource e gravamos a saída em um javax.xml.transform.stream.StreamResult.

TransformerFactory factory = TransformerFactory.newInstance ();
Folha de estilo StreamSource = new StreamSource ("xml2html.xsl");
Transformador transformador = factory.newTransformer (stylesheet);
StreamSource source = new StreamSource ("memo.xml");
Resultado do StreamResult =
    novo StreamResult (new FileOutputStream ("memo.html"));
transformador.transforma (fonte, resultado);
                

Depois de executar o programa, acabamos com o arquivo "memo.html", que é uma versão em HTML do memorando.

## Resumo

O XML é amplamente utilizado para vários propósitos, pois é facilmente lido e compreendido tanto por seres humanos quanto por computadores. XML pode ser escrito, lido e transformado em diferentes formatos. O DOM pode ser usado para escrever e ler XML, enquanto o SAX fornece um método para ler XML. O XSL é usado para transformar XML.

## Conjunto de Exercícios

### Múltipla escolha

1. XML significa eXtensible Markup Language
    
    1. Verdadeiro
    2. Falso
2. Possíveis maneiras de escrever XML são:
    
    1. Manualmente com um editor de texto.
    2. Programaticamente: Hard-Coded
    3. Programaticamente: Usando um Objeto de Documento
    4. Todos os itens acima
    5. Nenhuma das opções acima
3. Ao gravar em um arquivo, você usará
    
    1. java.io.OutputStream
    2. java.io.Writer
    3. java.io.PrintWriter
    4. java.io.FileOutputStream
    5. 1 e 2
    6. 3 e 4
4. Para obter um construtor de documentos, é necessário
    
    1. Crie uma fábrica de construtores e obtenha uma instância do construtor de documentos.
    2. Crie uma instância do construtor de documentos.
    3. Obtenha uma nova instância de uma fábrica de construtores e obtenha um novo construtor de documentos a partir dela.
5. Todo documento contém um único elemento raiz.
    
    1. Verdadeiro
    2. Falso
6. Para criar um novo elemento:
    
    1. Crie uma nova instância usando um construtor.
    2. Use o método createElement em um objeto de documento.
7. Para produzir um documento, use um XMLSerializer.
    
    1. Verdadeiro
    2. Falso
8. Possíveis maneiras de ler XML são:
    
    1. Programaticamente
    2. Usando o SAX
    3. Usando o DOM
    4. Nenhuma das opções acima
    5. 2 e 3
9. Para um documento grande, você poderia analisá-lo usando
    
    1. SAX
    2. DOM
    3. Nem
10. Que instrução XSL é usada para recuperar o texto de um elemento xml?
    
    1. <xsl: template match = "to">
    2. <xsl: value-of select = "." />
    3. <xsl: apply-templates />

### Exercícios

1. Descubra o DefaultHandler e como ele difere do ContentHandler.

### Exercícios de Programação

1. Complete o seguinte:
    1. Escreva um programa para gerar uma nota no formato xml e salve-a em um arquivo.
    2. Agora escreva um programa para exibir o conteúdo deste memorando xml.
    3. Escreva um programa para transformar o memorando em HTML e texto bruto.
2. Implemente um analisador SAX usando DefaultHandler em vez de ContentHandler.
