## Basic Concepts

The purpose of a GEXF document is to define a graph representing a network. Let us start by considering the minimal graph shown in the figure below. It contains 2 nodes and 1 edge.
![Simple](../res/simple.png)

### A Simple Graph
````xml
<?xml version="1.1" encoding="UTF-8"?>
<gexf xmlns="http://www.gexf.net/1.3draft"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.gexf.net/1.3draft
                             http://www.gexf.net/1.3draft/gexf.xsd"
      version="1.3">
  <meta lastmodifieddate="2009-03-20">
    <creator>Gephi.org</creator>
    <description>A hello world! file</description>
  </meta>
  <graph defaultedgetype="directed">
    <nodes>
      <node id="0" label="Hello"/>
      <node id="1" label="Word"/>
    </nodes>
    <edges>
      <edge id="0" source="0" target="1"/>
    </edges>
  </graph>
</gexf>
````

The GEXF document consists of a gexf element and a variety of subelements: graph, node, edge. In the remainder of this section we will discuss these elements in detail and show how they define a graph.

### Header
n this section we discuss the parts of the document which are common to all GEXF documents, basically the gexf element and the meta declaration.

````xml
<?xml version="1.1" encoding="UTF-8"?>
<gexf xmlns="http://www.gexf.net/1.3draft"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.gexf.net/1.3draft
                             http://www.gexf.net/1.3draft/gexf.xsd"
      version="1.3">
  <meta lastmodifieddate="2009-03-20">
    <creator>Gephi.org</creator>
    <description>A hello world! file</description>
    <keywords>basic, web</keywords>
  </meta>
  ...
</gexf>
````

The first line of the document is an XML process instruction which defines that the document adheres to the XML 1.1 standard and that the encoding of the document is UTF-8, the standard encoding for XML documents. Of course other encodings can be chosen for GEXF documents.

The second line contains the root-element element of a GEXF document: the gexf element. The gexf element, like all other GEXF elements, belongs to the namespace [http://www.gexf.net/1.3draft]. For this reason we define this namespace as the default namespace in the document by adding the XML Attribute ````xmlns="http://www.gexf.net/1.3draft"```` to it. The two other XML Attributes are needed to specify the XML Schema for this document. In our example we use the standard schema for GEXF documents located on the gexf.net server. The first attribute, ````xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"````, defines xsi as the XML Schema namespace. The second attribute, ````xsi:schemaLocation ="http://www.gexf.net/1.3draft http://www.gexf.net/1.3draft/gexf.xsd"````, defines the XML Schema location for all elements in the GEXF namespace.

The XML Schema reference is not required but it provides means to validate the document and is therefore strongly recommended.

The ````<meta>```` element contains additional information about the network. Element leafs are assumed to be text, and ````lastmodifieddate```` is an international standard date (yyyy-mm-dd). The ````<graph>```` element must be declared after the ````<meta>```` element.

GEXF documents are specified in the [RelaxNG Compact](http://relaxng.org/compact-tutorial-20030326.html) file [gexf.rc](http://www.gexf.net/1.3draft/gexf.rnc). The Header is defined by the following declaration:
````rnc
default namespace = "http://www.gexf.net/1.3draft"
namespace     rng = "http://relaxng.org/ns/structure/1.0"
datatypes     xsd = "http://www.w3.org/2001/XMLSchema-datatypes"

# Grammar root
start = element gexf { gexf-content }

# Tree
gexf-content =
    attribute version { string "1.3" }
  & attribute variant { xsd:string }?
  & (element meta { meta-content }?
  ,  element graph { graph-content })

# Attributes & Leafs
meta-content =
    attribute lastmodifieddate { xsd:date }?
  & element creator { text }?
  & element keywords { text }?
  & element description { text }?
 ````
 
### Network Topology

The network topology structure containing nodes and edges is called the graph. A graph is, not surprisingly, denoted by a ````<graph>```` element. Nested inside a graph element are the declarations of nodes and edges. A node is declared with the ````<node>```` element inside a ````<nodes>```` element, and an egde with the ````<edge>```` element inside an ````<edges>```` element.  
````xml
<graph defaultedgetype="directed">
  <nodes>
    <node id="0" label="Hello" />
    <node id="1" label="Word" />
    ...
  </nodes>
  <edges>
    <edge id="0" source="0" target="1" weight="3.167" />
    ...
  </edges>
</graph>
````

#### Declaring a Graph
Graphs in GEXF are mixed, in other words, they can contain directed and undirected edges at the same time. If no direction is specified when an edge is declared, the default direction ````defaultedgetype```` is applied to the edge. If you know what kind of edges are stored, you may interpret the mixed graph as a directed or an undirected graph at your own risks.

The default direction is declared as the optional XML-attribute ````defaultedgetype```` of the ````<graph>```` element. The three possible values for this XML-attribute are ````directed````, ````undirected```` and ````mutual````. Note that the default direction is optional and would be assumed as ````undirected````.

The optional XML-attribute ````mode```` sets the kind of network: static, dynamic or slice. The last two provide time support (see  the section [dynamics]. Static mode is assumed by default.

The ````<edges>```` element must be declared *after* the ````<nodes>```` element. The order of ````<nodes>```` and ````<edges>```` doesn't matter.
````xml
<graph>
  <nodes>
  </nodes>
  <edges>
  </edges>
</graph>
````

````
graph-content =
    attribute defaultedgetype { defaultedgetype-type }?
  & attribute idtype { idtype-type }?
  & attribute mode { mode-type }?
  & (element nodes { nodes-content }
  ,  element edges { edges-content })


# Nodes

nodes-content =
    attribute count { xsd:nonNegativeInteger }?
  & element node { node-content }*

node-content =
    attribute id { id-type }
  & attribute label { xsd:token }?


# Edges

edges-content =
    attribute count { xsd:nonNegativeInteger }?
  & element edge { edge-content }*

edge-content =
    attribute id { id-type }
  & attribute type { edgetype-type }?
  & attribute label { xsd:token }?
  & attribute source { id-type }
  & attribute target { id-type }
  & attribute weight { weight-type }?


# Datatypes

defaultedgetype-type = [ a:defaultValue = "undirected" ]
    string "directed" |
    string "undirected" |
    string "mutual"

edgetype-type = [ a:defaultValue = "undirected" ]
    string "directed" |
    string "undirected" |
    string "mutual"

id-type =
    xsd:string | xsd:integer

idtype-type = [ a:defaultValue = "string" ]
    string "integer" |
    string "string"

mode-type = [ a:defaultValue = "static" ]
    string "static" |
    string "dynamic"

weight-type = [ a:defaultValue = "1.0" ]
    xsd:float
````

#### Declaring a Node
Nodes in the graph are declared by the ````<node>```` element. Each node has an identifier, which must be unique within the entire document, i.e., in a document there must be no two nodes with the same identifier. The identifier of a node is defined by the XML-attribute ````id````, which is a string. Addtionatly the type of id's used for all nodes can be declared with an optional ````idtype```` attribute on the outer````<nodes>```` element. Each node *must* have a XML-attribute ````<label>````, which is a string. 

#### Declaring an Edge
Edges in the graph are declared by the ````<edge>```` element. Each edge must define its two endpoints with the XML-Attributes ````source```` and ````target````. The value of the source, resp. target, must be the identifier of a node in the same document. The identifier of an edge is defined by the XML-Attribute ````id````. There is no order notion applied to edges.

Edges with only one endpoint, also called loops, selfloops, or reflexive edges, are defined by having the same value for source and target.

Each edge can have an optional XML-attribute ````label````, which is a string.

The optional XML-attribute ````type```` declares if the edge is ````directed````, ````undirected```` or ````mutual```` (directed *from source to target and from target to source*). If the direction is not explicitly defined, the default direction is applied to this edge as defined in the enclosing graph.

In addition the optional XML-attribute ````kind```` can be used to further differntiate edges in e.g. multi-graphs. 

The weight of the edge is set by the optional XML-attribute ````weight```` and is a float.

Assuming two nodes having respectively the ````id```` value set to ````0```` and `````1`````:

````xml
<edge id="0" source="0" target="1"/>
````

````xml
<edge id="0" source="0" target="1" type="directed" weight="2.4" />
````

````
edge-content =
    attribute id { id-type }
  & attribute type { edgetype-type }?
  & attribute label { xsd:token }?
  & attribute source { id-type }
  & attribute target { id-type }
  & attribute weight { weight-type }?

# Datatypes

id-type =
    xsd:string | xsd:integer

edgetype-type = [ a:defaultValue = "undirected" ]
    string "directed" |
    string "undirected" |
    string "mutual"

weight-type = [ a:defaultValue = "1.0" ]
    xsd:float
````

### Network Data
In the previous section we discussed how to describe the topology of a graph in GEXF. While pure topological information may be sufficient for some applications, these days focus is made on network analysis based on data attributes. Data are everywhere.

A bunch of data can be stored within attributes. The concept is the same as table data or SQL. An attribute has a title/name and a value. Attribute's name/title must be declared for the whole graph. It could be for instance "degree", "valid" or "url". Besides the name of the attribute another column  contains the type.

#### Data types
GEXF uses the XML Schema Data Types [XSD 1.1](http://www.w3.org/TR/xmlschema-2/) for the following primitives:
* [string](http://www.w3.org/TR/xmlschema-2/#string),
* [integer](http://www.w3.org/TR/xmlschema-2/#decimal),
* [float](http://www.w3.org/TR/xmlschema-2/#float),
* [double](http://www.w3.org/TR/xmlschema-2/#double),
* [boolean](http://www.w3.org/TR/xmlschema-2/#boolean),
* [date](http://www.w3.org/TR/xmlschema-2/#date), 
* [anyURI](http://www.w3.org/TR/xmlschema-2/#anyURI), and
* [hexBinary](http://www.w3.org/TR/xmlschema-2/#hexBinary).

#### Attributes Example
Each Node of this graph has three attributes: 
* an url, 
* an indegree value and 
* a boolean for french websites which is set to ````true```` by default.

````xml
<?xml version="1.1" encoding="UTF-8"?>
<gexf xmlns="http://www.gexf.net/1.3draft"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.gexf.net/1.3draft
                             http://www.gexf.net/1.3draft/gexf.xsd"
      version="1.3">
  <meta lastmodifieddate="2009-03-20">
    <creator>Gephi.org</creator>
    <description>A Web network</description>
  </meta>
  <graph defaultedgetype="directed">
    <attributes class="node">
      <attribute id="0" title="url" type="string"/>
      <attribute id="1" title="indegree" type="float"/>
      <attribute id="2" title="frog" type="boolean">
        <default>true</default>
      </attribute>
    </attributes>
    <nodes>
      <node id="0" label="Gephi">
        <attvalues>
          <attvalue for="0" value="http://gephi.org"/>
          <attvalue for="1" value="1"/>
        </attvalues>
      </node>
      <node id="1" label="Webatlas">
        <attvalues>
          <attvalue for="0" value="http://webatlas.fr"/>
          <attvalue for="1" value="2"/>
        </attvalues>
      </node>
      <node id="2" label="RTGI">
        <attvalues>
          <attvalue for="0" value="http://rtgi.fr"/>
          <attvalue for="1" value="1"/>
        </attvalues>
      </node>
      <node id="3" label="BarabasiLab">
        <attvalues>
          <attvalue for="0" value="http://barabasilab.com"/>
          <attvalue for="1" value="1"/>
          <attvalue for="2" value="false"/>
        </attvalues>
      </node>
    </nodes>
    <edges>
      <edge id="0" source="0" target="1"/>
      <edge id="1" source="0" target="2"/>
      <edge id="2" source="1" target="0"/>
      <edge id="3" source="2" target="1"/>
      <edge id="4" source="0" target="3"/>
    </edges>
  </graph>
</gexf>
````

#### Declaring Attributes
Attributes are declared inside an ````<attributes>```` element. The XML-attribute ````class```` applies to  child ````<attribute>```` elements on nodes `````node````` or edges ````edge````. You may declare the data-types: integer, double, float, boolean, string and liststring. In either case you can specify a default value using ````<default>````.
	
````xml
<graph mode="static">
  <attributes class="node">
    <attribute id="0" title="my-text-attribute" type="string"/>
    <attribute id="1" title="my-int-attribute" type="integer"/>
    <attribute id="2" title="my-bool-attribute" type="boolean"/>
  </attributes>
  <attributes class="edge">
    <attribute id="0" title="my-float-attribute" type="float">
      <default>2.0</default>
    </attribute>
  </attributes>
  ...
</graph>
````

````
attributes-content =
    attribute class { class-type }
  & attribute mode { mode-type }?
  & element attribute { attribute-content }*

attribute-content =
    attribute id { id-type }
  & attribute title { xsd:string }
  & attribute type { attrtype-type }
  & element default { text }?
  & element options { text }?


# Datatypes

class-type =
    string "node" |
    string "edge"

mode-type = [ a:defaultValue = "static" ]
    string "static" |
    string "dynamic"

attrtype-type =
    string "integer" |
    string "long" |
    string "double" |
    string "float" |
    string "boolean" |
    string "liststring" |
    string "string" |
    string "anyURI"
````
#### Defining Attribute Values
You may understand attributes while looking at this node definition. Besides native fields (id, label), node values are set for three attributes. Omitting an attribute will set the default value as its value. If no default value is set, this is an error.

````xml
<node id="0" label="Hello world">
  <attvalues>
    <attvalue for="0" value="samplevalue"/>
    <attvalue for="1" value="1831"/>
    <attvalue for="2" value="true"/>
  </attvalues>
</node>
````

````xml
<edge id="0" source="0" target="1">
  <attvalues>
    <attvalue for="0" value="1.5"/>
  </attvalues>
</edge>
````

````
attvalues-content =
    element attvalue { attvalue-content }*

attvalue-content =
    attribute for { id-type }
  & attribute value { xsd:string }
````

#####Note about the ````liststring```` type: 
A liststring replaces the usage of multiple boolean attributes. Instead of declaring the attributes ````foo````, ````bar```` and ````foobar````, you just only have to declare ````my-foobar````. ````my-foobar```` may takes the values ````foo````, ````bar````, ````foobar````, ````foo;bar````, ````foobar;foo```` etc. So the value ````foobar;foo```` is equivalent to an attribute ````foobar=true```` and ````foo=true````.

Liststring gives the element values separated by a pipe, a comma or a semicolon. This is an unsafe type! Liststring values are therefore parsed, and this parsing doesn't take any escape characters like quotes or double-quotes into account. You have to check your data before making a GEXF file.

````xml
<node id="0" label="Hello world">
  <attvalues>
    <attvalue for="0" value="foobar|bar"/>
  </attvalues>
</node>
````

A complete example:

````xml
<attributes>
 <attribute id="0" title="hobby" type="liststring">
   <options>ski|dance|photo</options>
  </attribute>
</attributes>
<nodes>
 <node id="42" label="a node">
       <attvalues>
           <attvalue for="0" value="dance|ski">
       </attvalues>
   </node>
</nodes>
````

````xml
<attributes>
 <attribute id="0" title="hobby-ski" type="boolean" />
 <attribute id="1" title="hobby-dance" type="boolean" />
 <attribute id="2" title="hobby-photo" type="boolean" />
</attributes>
<nodes>
 <node id="42" label="a node">
       <attvalues>
           <attvalue for="0" value="true">
           <attvalue for="1" value="true">
           <attvalue for="2" value="false">
       </attvalues>
   </node>
</nodes>
````

#####Note about the attribute ````options````:
it defines the available values, separated by a pipe. It is both used as a type constraint and for parser optimization. The combined default value must be an available option, like the following example.

````xml
<graph mode="static">
  <attributes class="node">
    <attribute id="0" title="my-string-attribute" type="string">
        <default>foo</default>
        <options>foo|bar|foobar</options>
    </attribute>
    <attribute id="1" title="my-integer-attribute" type="integer">
        <default>5</default>
        <options>1|2|5|6</options>
    </attribute>
  </attributes>
  ...
</graph>
````

When it is applied to a liststring attribute, it gives all possible elements of the list:

````xml
<attributes>
 <attribute id="0" title="foo-attr" type="liststring">
   <options>foo1|foo2|foo3</options>
 </attribute>
</attributes>
<nodes>
 <node id="42" label="node A">
       <attvalues>
           <attvalue for="0" value="foo3|foo2">
       </attvalues>
   </node>
 <node id="43" label="node B">
       <attvalues>
           <attvalue for="0" value="">
       </attvalues>
   </node>
 <node id="44" label="node C">
       <attvalues>
           <attvalue for="0" value="foo1|foo2|foo3">
       </attvalues>
   </node>
</nodes>
````

````xml
...
<node id="42" label="node A">
   <attvalues>
       <attvalue for="0" value="foo1|foo4">
   </attvalues>
</node>
````