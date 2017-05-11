---
layout: post
title: "WPUB3"
date: 2017-05-11
tags:
- wpub
---
### Project:
The goal of the third project is to create XSL transformation files for generating XHTML+CSS and PDF presentations from XML formated files.
The requirements of the project:
  1. Presentation in well-formed and valid XML formated
  2. Validation and description of the XML using DTD, XML Schema or RELAX NG
  3. XSL transformation from XML to XHTML and CSS files
  4. XSL transformation from XML to a PDF file
  
---

### Documentation:
The html.bat and pdf.bat files were created for XSL transformation. The presSchema.xsd file validates the XML presentation, using XML Schema. All these files are included in the archive in AIS.  
For the transformations Saxon 9.7.0.18 was used, for generating the PDF the RenderX processor was used.   
Note: The transformations do not use any parameters, as I was able to do everything I wanted without using any parameters (such as using `<style>` insted of formating using a parameter).
##### 1. The presentation XML
The presentation is defined by the XML Schema file, described in the 2. section. While the requirements on the course page state, that the document has to be firstly descibed (DTD, XML Schema ...), I will demonstrate the description in XML form, since its easier to read.  
The structure of the presentation is given as follows:

```xml
<presentation>
	<data>
		<title></title> - must have, the title of the presentation
		<author></author> - optional, the name of the author
	</data>
	
	<slide> - any number of slides, at least 
		<title><title/> - optional, title of the slide
		...
		<presElements><presElements/> - any number of presentation elements in any order
		...
		<pagenumber/> - optional, display page number
		<style> - optional, local style
			...
			<styleElements><styleElements/> - any number of style elements in any order
			...
		</style>
	</slide>
	...
	<style> - global style
		...
		<styleElements><styleElements/> - any number of style elements in any order
		...
	</style>
</presentation>
```

The data element includes the metadata about the presentation, currently only the title and author. The presentation must have a title, since the HTML version is generated into a folder based on the title. This also means the title has to be a valid folder name.  
Any number of `<slide>` elements can be defined. Has attribute "layout".   
The layout attribut can have values "title", "raw" and "split". The title layout centers all elements on the page. The raw layout has two blocks, a top and a default(left) block, elements defined by the pos attribute and the title are included in the top block, other are simply put in the default block. The split layout splits the default block into 3 other block left, center and right, in addition to the top block.  
The `<title>` element has to be the first, and can only be used once in a slide. If it is left empty, it will display the presentation title. Has attribute "pos".  

The presentation elements used:
 * `<author>` - Has different style than the other text and title, if left empty it display the presentation author. Has attribute "pos".
 * `<text>` - Simple text element, new text elements are displayed on new line. Has attributes "pos" and "id".
 * `<toc>` - Generates Table of Content, by grabbing every non empty `<title>` element from the presentation. Has attribute "pos".
 * `<picture>` - Graphical element, containing the url of a file. Has attributes "pos" and "id".
 * `<space>` - Horizontal space(empty line). Has attribute "pos".
 * `<list>` - An ordered or unordered list, based on the marking attribute. Has attributes "pos", "id" and "marking".
 *	`<item>` - An item in the list. Holds only text content. Has attribute "id".
 * `<pagenumber/>` - If defined the page/slide number is created at the bottom of the page. Has to be the last element before the `<style>` element and has to be empty.  
 
The attributes used:  

"pos" - defines for the split and raw layouts, where the element is located. Can be "top", "left", "center" and "right".  
"id" - used for applying styles to specific elements, doesn't have to be unique, but has to be a valid XML name.  
"marking" - the marking of the list, has values "disc", "circle" and "square" for unordered list and values "1", "a", "A", "i" and "I" for ordered list.  

The `<style>` element contains the styling of the presentation. The `<style>` defined in the `<presentation>` element apllies to the whole presentation. The `<style>` defined in a `<slide>` only applies to that slide.  
The style elements give the style for a group of presentation elements, by using valid CSS definitions, and have the form:  

```xml
<style>
	...
	<elementName>
		...
		<cssAttributeName>cssAttributeValue</cssAttributeName> - any number
		...
	</elementName>
	...
	Example:
	<text id="textId1" pos="center">
		<color>red</color>
		<font-size>200%</font-size>
	</text>
</style>
```

If the style element has no attributes it is applied to every element of that type in the slide/presentation. This can be further specified by using the "pos" and "id" attributes. The example given applies the style to every text element which are located in "center" or the ones which have the id "textId1".
Every presentation element can be used as style element, with their defined attributes, with the exception of `<pagenumber/>` and `<space>`, which have no style definition.
The `<toc>` element is a special case, since its style is only applied from the global `<style>` element.
In addition to these, two new style elements are defined. `<text-all>` applies the defined styles to every text element and `<page>` applies styles to the page, such as background.

---
##### 2. Document definition
The presentation was defined using XML Schema, contained in the presSchema.xsd file. 
An example of the definition of the slide:

```xml
<xs:complexType name="slideType">
	<xs:sequence>
		<xs:element name="title" type="titleType"  maxOccurs="1" minOccurs="0"/>
		<xs:choice maxOccurs="unbounded" minOccurs="0">
			<xs:element name="space" type="spaceType"/>
			<xs:element name="toc" type="titleType"/>
			<xs:element name="text" type="textType"/>
			<xs:element name="author" type="titleType"/>
			<xs:element name="picture" type="picType"/>
			<xs:element name="list" type="listType"/>
		</xs:choice>
		<xs:element name="pagenumber" type="emptyType" maxOccurs="1" minOccurs="0"/>
		<xs:element name="style" type="styleType" maxOccurs="1" minOccurs="0"/>
	</xs:sequence>	
	<xs:attribute name="layout" type="layAtt" use="optional"/>
</xs:complexType>
```

The schema currently validates any tags, and not only valid CSS tags for the styles:

```xml
<xs:complexType name="cssBothType">
	<xs:sequence maxOccurs="unbounded" minOccurs="0">
        <xs:any processContents="lax"/>
	</xs:sequence>
	<xs:attribute name="id" type="xs:Name" use="optional"/>
	<xs:attribute name="pos" type="posAtt" use="optional"/>
</xs:complexType>
```

---
##### 3. XSL transformation from XML to XHTML and CSS files
This transformation is done by the html.bat file, and is defined in the presXhtml.xsl file, with the presentation XML as the only argument. This process creates a new folder, based on the title element in the data element. The content of the presentation is saved in this folder.  
Each slide element is created into a new file, called slideX.html (the first slide is slide1.html etc.). These slides are valid XHTML files, without any stylization include. However each file contains links to CSS files: to the main.css fr every slide and to a styleX.css, if the style element was defined (or specific layouts were used).  
The CSS files are generated by the same transformation. The main.css contains the style for the whole presentation and formating and default definitions (such as the ToC format and layout elements), so it is important to have, even if no style is defined. The styleX.css files contain the style of the page and the layout of the page.  
In addition, navigation arrows are created on each page, linking to the next and previous HTML files.  
Note: I was unable to copy image files using XSL transformations, so pictures used in the presentation have to be manually copied to the created folder.  

---
##### 4. XSL transformation from XML to a PDF file
This transformation is done by the pdf.bat file, and is defined in the presPdf.xsl file, with the presentation XML as the only argument. This process creates a new file, called result.pdf, which contains the whole presentation.  
Unlike for the XHTML transformation, no style objects are generated, the style is directly applied to the given elements (the same way as for the XHTML), and is saved in the FO file. `<xsl:fo>` supports valid CSS definitions on each element, both for styling and positioning.  
For applying background, the `<fo:region-before>` region was used. Which means this region is set as `<fo:flow>`, meaning too much content with pos="top" can generate unwanted slides.  
This is a main difference from the XHTML+CSS version, since HTML allows the zones to stretch, `<xsl:fo>` has constant definitions. Another differnce is how the layouts work, since for the "raw" layout in HTML, the other 3 block are present, but their content can overleap, which is not possible using `<xsl:fo>`. There are further small differences, mostly the different application of CSS (such as different supported font-families etc).  





























