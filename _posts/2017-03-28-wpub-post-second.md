---
layout: post
title: "WPUB2"
date: 2017-03-28
tags:
- wpub
---
### Project:
The goal of the second project was to generate a document using DocBook, the generated document had to contain the following elements:
  1. Breakdown of text to sections, subsections and subsubsections, appendix and generated content
  2. Highlight of words, highlight using bullet or numbered list
  3. Links to other part of the document, links to external URL
  4. Footnotes
  5. List of used literature and references, their citations in the document
  6. Pictures and tables, references to them in the text, list of tables and pictures at the beginning or end of the document
  7. Register items, with at least level two hierarchy, for example "cycles,for" and "cycles,while" (at least 10-15 terms)
  
---
### Documentation:
Note: the generated document has 32 pages, without the initial pages and the appendix it has 18 pages. However the document contains 13 medium sized figures and many chapters have unfortunate length, creating almost empty pages.
Note2: the mathematical function, the tables and the bibliography was imported from LaTeX and transformed to xml using replacement of text with Regex, so these parts might by harder to read(especially complex mathematical functions). 
##### 1. Breakdown of text to sections, subsections and subsubsections, appendix and generated content
The document is broken down to the said sections:

![image of breakdown](https://github.com/gabelovsky/gabelovsky.github.io/tree/master/sourcefiles/breakdown.pgn)

The subsections were created using the hierarchy used in DocBook:

```xml
<chapter>
chapter content
...
	<section>
	subsection content
	...
		<section>
		subsubsection content
		...
		</section>
	</section>
</chapter>
```

The first appendix contains some prototype tests of the balchelor's thesis, the second appendix contains the work plan for both semesters, the third appendix contains the list of indexes.

For example, the index appendix:
```xml
<appendix>
	<title>Indexes</title>
	<index></index> 
</appendix>
```
Content was generated using DocBook, for example the table of content, list of tables and figures and the index mentioned above.
```xml
<xsl:param name="generate.toc">
book   figure,table,title,toc
</xsl:param>
```
---
##### 2. Highlight of words, highlight using bullet or numbered list
Links to the other part of the document were highlighted(since DocBook doesn't mark links like HTML):
```xml
<emphasis role="strong">
<xref linkend="descript" xrefstyle="template:first part (on page %p)"/>
</emphasis>
```

Bullet list was created using `itemizedlist`:
```xml
<itemizedlist>
	<listitem>
		<para>Transform y->f(y) </para>
	</listitem>
	<listitem>
		<para>Expand f(y)->D<subscript>f(y)</subscript></para>
	</listitem>
	<listitem>
		<para>Sample in D<subscript>f(y)</subscript> to get U<subscript>n+1</subscript> </para>
	</listitem>
</itemizedlist>
```
---
##### 3. Links to other part of the document, links to external URL
Links to the other part of the document were used mainly in the introduction chapter, using the syntax:
```xml 
<xref linkend="selfsim">Self similar models</xref>
```
Links to external URLs were used to source some of the figures:
```xml
<footnote>
	<para>
		From:
		<ulink url="http://www.ams.org/notices/199808/paxson.pdf">http://www.ams.org/notices/199808/paxson.pdf
		</ulink>
	</para>
</footnote>
```
---
#####  4. Footnotes
Footnotes were used for the external URLs mentioned above and for some mathematical corrections:
```xml
<footnote>
	<para>
		(<superscript>d</superscript><subscript>k</subscript>) represents binomial
	</para>
</footnote>
```
---
#####  5. List of used literature and references, their citations in the document
Bibliography was generated at the end of the document using the format:
```xml
<bibliography>
<title>References</title>
	
<bibliomixed id="packet">
	<author>Jain, Raj and Routhier, Shawn</author>
	<title>Packet trains--measurements and a new model for computer network traffic</title>
	<productname>IEEE journal on selected areas in Communications</productname>
	<date>1986</date>
	<publisher>IEEE</publisher>
	<bibliomisc>
		<type>article</type>	</bibliomisc>
</bibliomixed>
...
```
These were later referenced like: `<xref linkend="chen"/>`

---
##### 6. Pictures and tables, references to them in the text, list of tables and pictures at the beginning or end of the document
The document contains 13 figures using the syntax:
```xml
<figure>
	<title>The effect of heavy tails, Pareto random variables with &#x03b1;=1</title>
	<graphic width="80%" height="80%" fileref="fig0.png"></graphic>
</figure>
```
The document has only two tables, both for the work plan for the thesis:
```xml
<table frame='all'><title>Summer term</title>
	<tgroup cols='3' align='left' colsep='1' rowsep='1'>
		<thead>
			<row>
				<entry>Week</entry>
				<entry>Planned task</entry>
				<entry>Task done</entry>
			</row>
		</thead>
		<tbody>
			<row>
				<entry>1st</entry><entry>Confirmation of the Bachelor thesis </entry><entry> Organizational problems</entry>
			</row>
.....................
```
The list of tables and figures(pictures) was generated by DocBook.

---
##### 7. Register items, with at least level two hierarchy, for example "cycles,for" and "cycles,while" (at least 10-15 terms)
Register items were created for specific traffic models and for some more important traffic characteristics:
```xml
<indexterm>
	<primary>Model</primary>
	<secondary>Autoregressive</secondary>
	<tertiary>TES transform-expand-sample</tertiary>
</indexterm>
```
In appendix C the index was generated `<index></index>`.

---
##### X. XSLT templates
The template in thesis-tp-fo.xsl for the titlepage was modified to reflect the one in the BC thesis.
A template was created for the annotation:
```xml
<xsl:template match="*" mode="anno">
<xsl:apply-templates select="." mode="header"/>
<fo:block-container xmlns:fo="http://www.w3.org/1999/XSL/Format">
<fo:block font-size="20pt" space-before="10pt" text-transform="uppercase">
	<xsl:value-of select="title"/>
</fo:block>
<fo:block>
	<xsl:value-of select="subtitle"/>
</fo:block>
<fo:block-container absolute-position="absolute" top="15cm">
<fo:block>
	Course: <xsl:value-of select="/book/bookinfo//affiliation/orgdiv[@role='course']"/>
</fo:block>
<fo:block>
	Author: <xsl:value-of select="/book/bookinfo/author/firstname"/>
	<xsl:value-of select="/book/bookinfo/author/surname"/>
</fo:block>
<fo:block>
	Supervisor: <xsl:value-of select="/book/bookinfo/othername[@role='supervisor']"/>
</fo:block>
<fo:block>
	<xsl:value-of select="/book/bookinfo/pubdate"/>
</fo:block>
<fo:block space-before="10pt" ><xsl:value-of select="para"/></fo:block>
</fo:block-container>
</fo:block-container>
</xsl:template>
```
and one for the declaration:
```xml
<xsl:template match="*" mode="dec">
	<fo:block-container xmlns:fo="http://www.w3.org/1999/XSL/Format">
	<fo:block font-size="20pt" padding-top="150pt">
		<xsl:value-of select="title"/>
	</fo:block>
	<fo:block space-before="60pt">
		<xsl:value-of select="para"/>
	</fo:block>
	<fo:block space-before="20pt">
		<xsl:value-of select="sigdate"/>
		 <fo:leader padding-left="200pt" leader-pattern="rule" leader-length="30%" rule-style="solid" rule-thickness="2pt"/>
		 <fo:inline-container text-align="right">  
			<fo:block margin-right="45pt">
				<xsl:value-of select="sig"/>
			</fo:block>
		 </fo:inline-container>
		 
	</fo:block>
	</fo:block-container>
</xsl:template>
```
Additionally a short template was created for the header used in the annotation:
```xml
<xsl:template match="*" mode="header">
<fo:block xmlns:fo="http://www.w3.org/1999/XSL/Format" hyphenate="false" text-align="center" font-size="14pt"  text-transform="uppercase">
      <xsl:value-of select="/book/bookinfo//affiliation/orgname"/>
</fo:block>
<fo:block xmlns:fo="http://www.w3.org/1999/XSL/Format" space-before="3pt"  text-align="center" font-size="10pt" border-after-style="solid" border-after-width="2pt" space-after="10pt">
      <xsl:value-of select="/book/bookinfo//affiliation/orgdiv[@role='faculty']"/>
</fo:block>
</xsl:template>
```


































