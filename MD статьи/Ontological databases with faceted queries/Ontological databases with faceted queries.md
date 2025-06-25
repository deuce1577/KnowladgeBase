# **REGULAR PAPER**

# **Ontological databases with faceted queries**

# **Tadeusz Pankowski[1](http://orcid.org/0000-0002-8168-4365)**

Received: 6 December 2020 / Revised: 25 November 2021 / Accepted: 10 February 2022 / Published online: 15 March 2022 © The Author(s) 2022

# **Abstract**

The success of the use of ontology-based systems depends on efficient and user-friendly methods of formulating queries against the ontology. We propose a method to query a class of ontologies, called *facet ontologies* (*fac-ontologies*), using a faceted human-oriented approach. A fac-ontology has two important features: (a) a hierarchical view of it can be defined as a nested facet over this ontology and the view can be used as a faceted interface to create queries and to explore the ontology; (b) the ontology can be converted into an *ontological database*, the ABox of which is stored in a database, and the faceted queries are evaluated against this database. We show that the proposed faceted interface makes it possible to formulate queries that are semantically equivalent to *SROIQFac*, a limited version of the *SROIQ* description logic. The TBox of a fac-ontology is divided into a set of rules defining intensional predicates and a set of constraint rules to be satisfied by the database. We identify a class of so-called *reflexive weak cycles* in a set of constraint rules and propose a method to deal with them in the chase procedure. The considerations are illustrated with solutions implemented in the DAFO system (*data access based on faceted queries over ontologies*).

**Keywords** Ontological database · Faceted queries · Query building · Ontology query languages · Ontology-based data access

# **1 Introduction**

In the last two decades, we have observed a steady increase in the number of applications based on ontology-oriented technologies. The reason is that ontologies provide a wellformulated and precise knowledge specification of the conceptualization of the application domain, and enrich query answering with intensional knowledge not explicitly captured by the extensional part of the ontology

It is known that classic reasoning problems in ontologies can be reduced to query answering problems [\[9\]](#page-17-0). Moreover, in many data-intensive applications, these inference capabilities are dominated by the need to respond to queries. To meet these needs, the extensional part of the ontology is often stored using robust and mature database technologies. Thus, we are witnessing the synergistic combination of ontologies and databases, which results in developing modern database solutions such as ontology-based data access (OBDA) [\[18](#page-17-1)[,59](#page-18-0)[,75](#page-18-1)], ontology-enhanced databases [\[7](#page-17-2)], ontological databases [\[37\]](#page-18-2), and virtual knowledge graphs (VKG) [\[76\]](#page-18-3).

B Tadeusz Pankowski tadeusz.pankowski@put.poznan.pl

Query and exploration tools for human-centered interaction remain an important issue in ontology-based access to data. To make a query interface easier to use, we propose a new concept of a *faceted view* of the ontology in a form of a hierarchical *nested facet* (as an extension of the "flat" facet investigated in [\[7\]](#page-17-2)). The faceted view of an ontology depends on the intended query, whose template is provided by the user. A relevant part of the ontology, in the form of a spanning tree covering the query template, is produced in the response as a faceted view. A *faceted interface* is a faceted view equipped with a set of operations allowing for creating queries, and for extending the faceted view through ontology exploration. We propose such a set of operations that allows to create queries with the expressive power equivalent to (a slightly limited) *SROIQ* [\[44](#page-18-4)[,48](#page-18-5)[,61\]](#page-18-6).

We assume that there are two sets of rules in a faceted ontology (*fac-ontology*: (1) *V* is a set of rules defining intensional predicates (views). Intensional predicates enrich the vocabulary and are used to facilitate query formulation. (2) *C* is a set of constraint rules which are expected to be satisfied by the ABox *A*, i.e., they are materialized in the ABox by means of the chase procedure. We investigate the termination of the chase for a new class of weak cycles in *C*, which we call *reflexive weak cycles*, and show that a chase terminates

<sup>1</sup> Institute of Computing Science, Pozna ´n University of Technology, Poznan, Poland

in the presence of reflexive weak cycles giving a solution (but not the universal solution). We do not consider rewriting rules because we assume that after the chase with respect to constraint rules, the ABox obeys all constraint rules. The ABox is stored in a relational database.

The presented approach has been implemented and verified in the DAFO system [\[24](#page-17-3)[,55](#page-18-7)[,57\]](#page-18-8).

# **1.1 Contribution**

The novelties in this paper are as follows:

- 1. Defining so-called *faceted ontologies* (fac-ontologies) and a concept of the *faceted view* over them. We use the faceted view to propose a *faceted interface* to create *faceted queries* over fac-ontologies. We show, that the faceted interface allows to formulate faceted queries equivalent (with some limitations) to queries (class expressions) in *SROIQ*. Then, the query answering is LogSpace in the size of the ABox.
- 2. Identifying a new class of weak cycles, called *reflexive weak cycles* in the dependency graph of a class *C* of constraint rules in a fac-ontology. The proposed modification of the chase algorithm produces a solution, although the universal solution does not exist.

# **1.2 Outline**

The paper is organized as follows. In Sect. [2,](#page-1-0) we define an ontology used as a running example, and formulate the motivations underlying the research. The notions of fac-ontologies and ontological databases are introduced in Sect. [3.](#page-3-0) In Sect. [4,](#page-5-0) we define the concept of a nested facet, which is next used to define faceted views, and faceted interfaces over ontological databases. Faceted queries are defined and investigated in Sect. [5.](#page-8-0) We show the relationship between faceted queries and queries (concept expressions) in the *SROIQ* description logic. Some subclasses of faceted queries and their graphical forms created by the faceted interface are presented in Sect. [6.](#page-9-0) In Sect [7,](#page-12-0) we discuss creation of an ontological database for a given fac-ontology, in particular we investigate the problem of the termination of the chase procedure in the presence of reflexive weak cycles. Related work and novelties of the paper are deeply analyzed in Sect [8.](#page-15-0) Section [9](#page-16-0) summarizes and concludes the paper.

# <span id="page-1-0"></span>**2 Running example and motivations**

# **2.1 A sample ontology BibOn**

As a running example, we consider an bibliographic ontology BibOn with the schema graph in Fig. [1.](#page-2-0) A schema graph is a directed graph with nodes labeled by classes, and edges labeled by properties. Unlabeled edges (with triangular arrows) denote subsumption relations between classes and between properties. Classes are *unary predicates*, and properties are *binary predicates*. Both classes and properties are divided into *extensional* and *intensional* ones. Extensional predicates are materialized in the ABox, while intensional are views defined by rules. There are two rationale behind using intensional predicates.

1. *Simplification of the syntax of rules.* In most implemented systems, classes and properties are restricted to being atomic names [\[9\]](#page-17-0). This can be achieved by using intensional predicates (views). For example, the intensional predicate (class) *ACMConf* can be defined by the following *definition rule*:

*Con f erence* - ∃*organi zer*.{ *ACM* } ≡ *ACMConf* .

Then *ACMConf* is an atomic class name and its definition consists of extensional predicates *Con f erence* and *organi zer* and a constant *ACM* . Similarly, the definition rule

*Paper* -∃*presented At*.*ACMConf* ≡ *ACMPaper*

defines a subclass *ACMPaper*. Then *ACMPaper* is an intensional predicate that can be unfolded to an extensional expression. Then, for example, the subsumption *ACMPaper Paper*, abbreviates the following subsumption between class expressions:

*Paper* - ∃*presented At*.(*Con f erence* - ∃*organi zer*.{ *ACM* }) *Paper*.

2. *Enriching the vocabulary of the ontology.* Intensional predicates enrich the vocabulary of the ontology, thus facilitating the formulation of queries. They are, in fact, views defined over extensional predicates. During query rewriting, they are unfolded using their definitions.

In Fig. [1,](#page-2-0) extensional classes and properties are drawn with solid lines, while intensional with dashed lines.

Below, we list some rules in the BibOn using the standard description logic notation.

1. There are four *inheritance hierarchies* with uniquely defined extensional *top classes*: *Person*, *Paper*, *Proceedings*, and *Conferences*, respectively. They are pairwise disjoint, e.g.,

– *Per son* ¬*Paper*.

The distinguished class *Data* is a class of standard data values (strings, numbers, etc.).

#### <span id="page-2-0"></span>**Fig. 1** A graph of the BibOn ontological schema

![](_page_2_Figure_3.jpeg)

- 2. *Subsumption and equivalences between extensional classes and properties*.
	- Class subsumption: *Author Per son*, *PUTAuthor Author*.
	- Class-driven specialization (definition of extensional predicate): *Per son* -∃*author O f* .*Paper* ≡ *Author*.
	- Value-driven specialization (definition of extensional predicate): *Author* - ∃*affiliation*.{ *PUT* } ≡ *PUTAuthor*.
	- Property subsumption: *corresp Author* w*rittenBy*.
- 3. *Definitions of intensional predicates*:
	- *Author* -∃*authorOf* .*ACMPaper* ≡ *ACMAuthor*.
	- *Con f erence* - ∃*countr y*.{ *USA* } ≡ *USAConf* .
	- *in Proceed*<sup>−</sup> ≡ *includes Paper*.
	- chains of properties: *inProceed ofConf* ≡ *presentedAt*, *author O f* ◦ *presentedAt* ≡ *authorConf* , *authorConf* ◦ *confPCMember* ≡ *authConfPCMember*.
- 4. *Domains and ranges of properties.*If *P* is a property, then ∃*P A* specifies that the domain of *P* is subsumed by *A*. We denote by *dom*(*P*) the *least upper bound* of the set of classes subsumming ∃*P*. Similarly, by *rng*(*P*) we denote the least upper bound of classes subsuming ∃*P*−, e.g.,
	- *dom*(*name*) = *Per son*, *rng*(*name*) = *Data*,
	- *dom*(*corresp Author*) = *Paper*,
	- *rng*(*corresp Author*) = *Author*.
- 5. *Mandatory membership of classes (or totality of properties). A* ∃*P* specifies that *A* has the *mandatory* membership in the domain of *P*, or that *P* is *total* on *A*. Similarly, *A* ∃*P*<sup>−</sup> says that *A* has the *mandatory* membership in the range of *P*, or that *P*− is *total* on *A*, e.g.,
	- *Per son* ∃*name*,
	- *Author* ∃w*rittenBy*−.
- 6. *Functionality of properties*:
	- *name* is a functional data property: (funct *name*),
	- *in Proceed* is a functional object property: (funct *in Proceed*).

# **2.2 Motivation of the paper**

In Fig. [2,](#page-3-1) we show a faceted query tree that formulates the request:

"Get persons who: (a) are authors of at least ten papers presented at ACM or IEEE conferences, and (b) are not affiliated at the 'PUT' ('Poznan University of Technology'), and (c) served PC members at conferences where they presented their papers."

In the faceted query tree in Fig. [2:](#page-3-1)

1. The root is labeled by the distinguished property *root*, we assume that *root* is reflexive universal property, i.e., ∀*x*, *y*(*root*(*x*, *y*) ↔ *x* = *y*).

![](_page_3_Figure_1.jpeg)

<span id="page-3-1"></span>**Fig. 2** A sample faceted query tree over the BibOn ontology

- 2. Every node (rectangle) is a class-node (labeled by a class), a property-node (labeled by a property) or a constant-node (labeled by a constant).
- 3. A node is labeled either by " or "-—the label is drawn below the node, and the set of children is then either disjunctive or conjunctive.
- 4. Nodes are labeled by: a number restriction (≥ 10), negation (¬), or local reflexivity (*Self*)—these labels are drawn above the node.
- 5. The semantics of the query is defined by its translation to a DL *SROIQ* expression: ∃*root*.(*Per son* - ((≥ 10)*authorConf* .(*ACMConf IEEEConf*) - ¬∃ *affiliation*.{ *PUT* }-∃*authConfPCMember*.*Self*)).Note that ∃*root*. *Q* ≡ *Q*, so ∃*root* can be omitted.

The tree-shaped structure of a faceted query also provides a faceted view of an ontology. In order to be viewed and queried in this way, an ontology must satisfy properties implied by the following assumptions:

- 1. All the nodes connected by or have a common parent node, and all of them are in the same inheritance hierarchy. The set of inheritance hierarchies is pairwise disjoint, and each hierarchy has a unique top class (the least upper bound). The top class is also used to compute the negation of a query (the complement with respect to the top class).
- 2. Every path of nodes in a faceted query tree is of the form:(*root*, *A*1,..., *Pn*, *An*),(*root*, *A*1,..., *Pn*), or (*root*, *A*1,..., *Pn*, *an*), *n* ≥ 1, where *A*, *P*, and *a* (with subscripts) are a class, a property, or a constant, respectively. This can be achieved if every class is defined as a specialization of its superclass.
- 3. The set of all predicates is divided into extensional (can occur in the ABox *A*) and intensional predicates (defined by rules). The extensional predicates are materialized by means of a chase procedure in the ABox and in a database.

The running example and the above comments serve as a motivation for the following research problems presented in this paper.

- 1. *Faceted ontologies* (fac-ontologies). We identify facontologies as a class for which the proposed faceted approach can be applied.
- 2. *Faceted-oriented query formulation*.We develop a method based on the faceted approach. We examine the expressive power of this approach and compare it with other faceted-oriented query systems.
- 3. *Creating a (faceted) ontological database for answering faceted queries*. We define and consider the so-called *reflexive weak cycles*, and propose a method of chasing the ABox in the presence of these cycles.

# <span id="page-3-0"></span>**3 Ontological database**

Ontologies are commonly considered as the best method to specify conceptualizations of application domains (conceptual models) [\[8\]](#page-17-4). In a database design, a conceptual model is presented as the entity–relationship (ER) diagram [\[22](#page-17-5)], expanded entity–relationship (EER) diagram [\[28](#page-17-6)] or an UML class diagram. A family of ontologies which can be used to a formal specification of such conceptual models is the *DL-Lite* family [\[8](#page-17-4)[,17](#page-17-7)]. The importance and usefulness of the *DL-Lite* family is testified by the fact that it is the basis of the W3C OWL 2 QL standard [\[54\]](#page-18-9). In this paper, we define a class of ontologies called *faceted ontologies*(*fac-ontologies*). In general, from a methodological point of view, each fac-ontology has the following properties:

- 1. The terminological part of the fac-ontology is a formal specification of the conceptual schema of an application domain created by means of the EER model. In particular, we follow the idea of subclasses specification by means of the *specialization* abstraction [\[28\]](#page-17-6).
- 2. Every finite part of the terminological part of the facontology must be representable through a faceted interface (Sect. [4.3\)](#page-6-0). In particular, in any class hierarchy, each subclass is a specialization of the top class of this hierarchy.

# **3.1 Faceted ontologies and ontological database**

In this section, we define an *ontological database*. We denote by UP <sup>=</sup> UP*<sup>E</sup>* <sup>∪</sup> UP*<sup>I</sup>* an infinite set of *unary predicates* (classes), where UP*<sup>E</sup>* is a set of extensional, and UP*<sup>I</sup>* a set of intensional classes. Analogously, we denote by BP <sup>=</sup> BP*<sup>E</sup>* <sup>∪</sup> BP*<sup>I</sup>* an infinite set of *binary predicates* (properties) consisting of a set of extensional (BP*<sup>E</sup>* ) and intensional (BP*<sup>I</sup>* ) properties. Const denotes an infinite set of *constants* (individual names). We also assume that a set of *labeled nulls*, LabNull <sup>⊆</sup> Const, is a subset of constants, for which the UNA (*Unique Name Assumption*) does not hold [\[1\]](#page-17-8). In particular, two different labeled nulls can denote the same individual, i.e., N*<sup>I</sup>* <sup>1</sup> <sup>=</sup> <sup>N</sup>*<sup>I</sup>* <sup>2</sup> for an interpretation *I*. Regular constants, i.e., constants from Const \ LabNull, obey UNA. It means that two different regular constants always denote different individuals.

*Data* is a distinguished class of *data values* (strings, numbers, etc.). The other classes are *object classes* and their instances are *objects*. Both data values and objects are represented by constants. Every property with the range in the class *Data* is a *data property*; otherwise, it is an *object property*.

<span id="page-4-0"></span>We define now the syntax for rules.

**Definition 1** Let *A*, *P*, and *a* be, respectively, a class, a property, and a constant (individual name). Let:

*C*::=*A* | ∃*R* | ∃*R*.{*a*}|∃*R*.*C* | *C* - *C*, *R*::=*P* | *P*−, *S*:: = *R* | *S* ◦ *R*.

Then

1. *Definition rules* are rules with the syntax:

$$\mathcal{L}\_V ::= C \equiv A \mid S \equiv P \mid \operatorname{dom}(P) = A \mid \operatorname{rng}(P) = A \dots$$

2. *Constraint rules* are rules built from extensional predicates and conforming to the syntax:

$$\mathcal{L}\_{\mathcal{C}} ::= \mathcal{C} \sqsubseteq \mathcal{C} \mid \mathcal{S} \sqsubseteq \mathcal{S} \mid A \sqsubseteq \neg A \mid \mathcal{R} \sqsubseteq \neg \mathcal{R} \mid (\mathtt{funct } \mathcal{S}) .$$

**Definition 2** A set Class of classes is an *inheritance hierarchy* if it is a *finite bounded complete partial order*,

$$\mathcal{H} = (\top^{\mathcal{H}}, \mathsf{Class}, \sqsubseteq),$$

where *<sup>H</sup>* <sup>∈</sup> Class is the *least upper bound* (the *top class*) in *H*.

We will also denote by *<sup>A</sup>* the top class *<sup>H</sup>* of an inheritance hierarchy to which belongs a class *A*.

**Definition 3** A class *A* is a *specialization* of a class *A*<sup>0</sup> in a set of rules if the rule *A*<sup>0</sup> - *C* ≡ *A* is derivable in this set, and the syntax of *C* is given in Definition [1.](#page-4-0)

**Definition 4** An *fac-ontology* with the signature *Sig*(*O*) ⊆ UP <sup>∪</sup> BP <sup>∪</sup> Const is a triple *<sup>O</sup>* <sup>=</sup> (*V*, *<sup>C</sup>*, *<sup>A</sup>*) such that:

1. *V*, and *C* are sets of rules with the syntax defined by *LV*, and *LC*, respectively, and *A* is a set of facts of the form *A*(*a*), and *P*(*a*1, *a*2), where *A* and *P* are extensional predicates, and *a*, *a*1, *a*<sup>2</sup> are constants.

- 2. The set UP of classes form a set of pairwise disjoint inheritance hierarchies with extensional top classes.
- 3. Every class *A* is either the top class in its inheritance hierarchy or is a specialization of *A*, denoted as *<sup>A</sup>* - *C* ≡ *A*.

**Definition 5** An *ontological database* is a fac-ontology *O* = (*V*, *C*, *A*) such that *A* satisfies all rules in *C*, i.e., *A* | *C*. We denote this as *Odb* = (*V*, *C*, *Adb*).

*Example 1* In the BibOn fac-ontology:

- 1. *V* can contain: *Paper* - ∃*presentedAt*.*AC MConf* ≡ *ACMPaper*, *Con f erence* - ∃*country*.{'*USA* } ≡ *USAConf* ,*in Proceed*<sup>−</sup> ≡ *includes Paper*,*inProceed*◦ *ofConf* ≡ *presentedAt*.
- 2. *C* can contain: *Per son*-∃*author O f* .*Paper* ≡ *Author*, *Author* - ∃*affiliation*.{ *PUT* } ≡ *PUTAuthor*, *Author* ∃*author O f* .*Paper*, *Paper* ∃w*rittenBy*.*Author*, *PUTAuthor*

 *Author*, *author O f* w*rittenBy*−, *Per son* <sup>¬</sup>*Paper*, (funct *of Conf* ).

# **3.2** *SROIQFac***—a subset of** *SROIQ*

We will consider *SROIQFac* as a query language, which is a subset of *SROIQ* [\[44](#page-18-4)[,46](#page-18-10)[,48](#page-18-5)[,61\]](#page-18-6). The syntax of *SROIQFac* is defined by the grammar

$$\begin{array}{rcl} q & ::= A \mid \{a\} \mid q \sqcap q \mid q \sqcup q \mid \neg q\\ & \mid \exists R \mid \exists R.q \mid (\geq k)R \mid (\geq k)R.q \mid \exists R.Sdf \\ R & ::= P \mid P^-, \end{array} \tag{1}$$

where *A* is a class, *P* is a property, *a* is a constant, and *k* is an integer, *k* ≥ 1.

We will use *SROIQFac* as a reference language for faceted queries and assume that it satisfies the following restrictions:

- 1. A nominal {*a*} can occur only in extensional restrictions, i.e., in ∃*R*.{*a*}.
- 2. Every query *<sup>q</sup>* in *SROIQFac* has a uniquely determined type, *type*(*q*), where:

*type*(*A*) = *A*, *type*({*a*}) <sup>=</sup> *Data*, *type*(*q*<sup>1</sup> *q*2) = *type*(*q*1) = *type*(*q*2), *type*(*q*<sup>1</sup> *q*2) = *type*(*q*1) = *type*(*q*2), *type*(¬*q*) = *type*(*q*), *type*(∃*R*) = *type*(∃*R*.*q*) = *type*((≥ *k*)*R*) = = *type*((≥ *k*)*R*.*q*) = *type*(∃*R*.*Self*) = *type*(*dom*(*R*)).

Formally, the semantics of*SROIQFac* is defined in terms of an interpretation *I* consisting of a non-empty set -*I* (the domain of the interpretation) and an interpretation function

<span id="page-5-1"></span>**Table 1** Syntax and semantics of *SROIQFac*

|                    | Syntax   | Semantics                 |
|--------------------|----------|---------------------------|
| Property inversion | P−       | {(x, y)   (y, x) ∈ PI }   |
| Nominal            | {a}      | {aI }                     |
| Conjunction        | q1<br>q2 | qI<br>1 ∩ qI<br>2         |
| Disjunction        | q1<br>q2 | qI<br>1 ∪ qI<br>2         |
| Existential        | ∃R       | {x ∈<br>I  ∃y(x, y) ∈ RI} |
| Restriction        | ∃R.q     | {x ∈<br>I   ∃y(y ∈ qI     |
|                    |          | ∧(x, y) ∈ RI)}            |
| (≥ k) restriction  | (≥ k)R   | {x ∈<br>I   #{y ∈<br>I    |
|                    |          | ∧(x, y) ∈ RI} ≥ k}        |
|                    | (≥ k)R.q | {x ∈<br>I   #{y ∈ qI      |
|                    |          | ∧(x, y) ∈ RI} ≥ k}        |
| Local reflexivity  | ∃R.Self  | {x   (x, x) ∈ RI}         |
| Negation           | ¬q       | (type(q))I \ qI           |

· *<sup>I</sup>*, which assigns: to every atomic class *A* a set *A<sup>I</sup>* ⊆ -*I*, to every atomic property *P* a binary relation *P<sup>I</sup>* ⊆ -*<sup>I</sup>* × -*<sup>I</sup>*, and to every data name *a* an element *a<sup>I</sup>* ∈ *DataI*. Table [1](#page-5-1) shows how to obtain the semantics of each compound expression from the semantics of its parts [\[9](#page-17-0)[,48\]](#page-18-5).

Note that the semantics of the negation of *q* is defined relatively to the *type*(*q*), i.e., to the top class (the "local universal class") of the inheritance hierarchy containing the class of answers to *q*.

An interpretation *I* satisfies the subsumptions *C*<sup>1</sup> *C*<sup>2</sup> and *S*<sup>1</sup> *S*<sup>2</sup> if *C<sup>I</sup>* <sup>1</sup> ⊆ *C<sup>I</sup>* <sup>2</sup> and *S<sup>I</sup>* <sup>1</sup> ⊆ *S<sup>I</sup>* <sup>2</sup> , respectively. *I* satisfies *A*<sup>1</sup> ¬*A*<sup>2</sup> and *R*<sup>1</sup> ¬*R*<sup>2</sup> if *A<sup>I</sup>* <sup>1</sup> ∩ *A<sup>I</sup>* <sup>2</sup> = ∅, and *RI* <sup>1</sup> ∩ *R<sup>I</sup>* <sup>2</sup> = ∅, respectively, and satisfies (funct *<sup>S</sup>*) if *<sup>S</sup><sup>I</sup>* is a partial function. We say that *I* is a *model* of a TBox or an ABox if it satisfies all subsumptions and facts in it. An ABox *A* is consistent with *C* if *A* and *C* have a common model. If *I* is a model of *O*, then we denote this as *I* | *O*, or *I* | *A* ∪ *V* ∪ *C*.

#### **3.3 Query answering in ontological databases**

Let *<sup>q</sup>* by a query in *SROIQFac*. We denote by *<sup>q</sup>*(*x*) <sup>a</sup> first-order form of the query *q*, which can be obtained using rules proposed in [\[61](#page-18-6)]. A *certain answer* to *q*(*x*) over a facontology *O* = (*V*, *C*, *A*) is a constant *a* for each *q*(*a*) is satisfied for all models of *O*, denoted *A* ∪ *V* ∪ *C* | *q*(*a*), or shortly *O* | *q*(*a*). The set of all certain answers to *q*(*x*) over *<sup>O</sup>* is denoted as *<sup>q</sup>*(*O*) , or *q*(*A*∪*V*∪*C*) .

If *Odb* = (*V*, *C*, *Adb*) is an ontological database, then *C* is satisfied in *Adb*, and is immaterial in query answering, i.e., the equality holds:

*<sup>q</sup>*(*Odb*) <sup>=</sup> *<sup>q</sup>*(*Adb*∪*V*) .

To eliminate intensional predicates in *q*, we unfold rules in *V*. Then every intensional predicate is replaced by its extensional definition. We obtain a query *q*[*V*], in which only extensional predicates appear, and the equality holds:

$$q^{(\mathcal{O}\_{db})} = q^{(\mathcal{A}\_{db})}\_{[\mathcal{V}]} \cdot$$

In DAFO, a mapping *M* is used to map *Odb* into a relational database *D* = (*Sch*, *D*), where the instance *D* is created from *Adb*, i.e., *M*(*Adb*) = *D*, and *M* is a set of dependencies of the form:

$$
\forall \mathbf{x} (\alpha(\mathbf{x}) \to \exists \mathbf{y} \mathbb{R}(\mathbf{x}, \mathbf{y})),
$$

where α ranges over unary and binary atoms, and R is an *n*-ary relation name in *Sch*, *n* ≥ 1. Additionally, we assume that *D* only has data "exported" from *Adb*, and each labeled null is mapped to NULL.

The mapping *M* is used in the translation of *q*[*V*] into a SQL query *q*[*V*,*M*] over the database *D*. As a result, the set of answers to *q* over *Odb* coincides to the set of answers to *q*[*V*,*M*] over the database, i.e.,

$$q^{(\mathcal{O}\_{db})} = q^{(D)}\_{[\mathcal{V},\mathcal{M}]}.$$

# <span id="page-5-0"></span>**4 Faceted views and faceted interfaces over ontological databases**

We now propose a method of query formulation against an ontological database *Odb* = (*V*, *C*, *Adb*). The method is based on the idea of *faceted search* [\[70](#page-18-11)] and *faceted queries* [\[7](#page-17-2)].

We start with a concept of the *nested facet* as a generalization of the ("flat") facet proposed in [\[7](#page-17-2)].

# **4.1 Nested facet**

In [\[7](#page-17-2)], a *facet* is defined as a pair (*X*,♦), with ♦ ∈ {-, ), a non-empty set, where either: (a) *<sup>X</sup>* <sup>=</sup> type and is a set of classes, or (b) *X* is a binary predicate (a property) and contains a distinguished symbol any and a set of individual names (constants) or a set of classes. A facet of the form (*X*, -) is conjunctive, and a facet of the form (*X*, ) is disjunctive.

We will extend the above notion of facets to *nested facets*, which have the form of a tree. In this tree-oriented notation, a facet (*X*,♦) will be written as a tree ♦*X*(), where ♦*X* is the labeled root, and is a set of its children, interpreted as a conjunctive or disjunctive set depending on ♦. Note that a facet defined in [\[7](#page-17-2)] forms always a two level tree.

In commercial applications, faceted queries are usually limited to flat facets where the flat facet corresponds to possible values of one data property. A query consists then of several facets, each of which relates to a different data property (aspect) of the searched object (e.g., price, manufacture, size, in the case of mobile phones). We extend this approach by introducing object properties and subsumptions between classes and properties. This requires nested faceted queries. The nested form of faceted queries is also due to the fact that a faceted query is a (complex) Description Logic query, and its syntax tree is nested.

<span id="page-6-3"></span>We define a nested facet as a finite multi-level tree with at least two levels.

**Definition 6** Let *A*, *P*, and *a*, possibly with subscripts, be a class, a property and a constant, respectively. Let *root* be a distinguished property not in BP, and ♦ ∈ {-, }. A *nested facet f* is an expression with the syntax defined by the grammar:

$$\begin{array}{l} f ::= \lozenge root \{ \mu\_{\lkern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5mu \skern 1.5$$

where *k*,*l*, *m*, *n* ≥ 1.

Every expression ♦*X*{*Y*1,..., *Yn*}, *n* ≥ 0, of the category *f* , *u*, and *t* is a tree with the root *X* and a set (possibly empty) of subtrees, which are children of *X*. The tree is labeled by ♦. By default, if *<sup>X</sup>* <sup>∈</sup> BP∪{*root*}, then♦ <sup>=</sup>, otherwise♦ = -, i.e., the set of property-node children is disjunctive, while the set of class-node children is conjunctive. *P*{*a*1,..., *an*} abbreviates the tree *P*{{*a*1},...,{*an*}}. The trees *A*{} and *P*{} are abbreviated by *A* and *P*, respectively.

<span id="page-6-2"></span>*Example 2* A nested facet corresponding to the sentence *"Persons who are authors of papers presented at ACM or IEEE conferences, and affiliated in PUT"*, is:

```

 root{
 -
  Per son{

   authorConf{ACMConf ,IEEEConf},
  affiliation.{
              PUT 
                    }
 }
},
```
Its graphical form is shown in Fig. [3.](#page-6-1) Note that in the graphical representation, the logical connective is drawn below the labeled node.

Its semantics is given by the following query in *SROIQFac* (a formal translation is given later on in Definition [10\)](#page-8-1):

*Per son* - (∃*authorConf* .(*ACMConf IEEEConf*) -∃*affiliation*.{ *PUT* }).

![](_page_6_Figure_13.jpeg)

<span id="page-6-1"></span>**Fig. 3** A graphical form of the nested facet in Example [2](#page-6-2)

# **4.2 Faceted view**

If a nested facet relates to a fac-ontology, then we call it a *faceted view* of this ontology. If *u* is a class-node of a nested facet (Definition [6\)](#page-6-3), then we denote by ρ(*u*) the root class of *u*, i.e., ρ(♦*A*{*t*1,..., *tn*}) = *A*, and similarly, by ρ(*t*) we denote the root property of *t*.

**Definition 7** A nested facet *f* is a *faceted view* over an facontology *O* = (*V*, *C*, *A*) if the signature of *f* is in the signature of *O*, and

- 1. If ♦*root*{*u*1,..., *un*} is in *f* and {ρ(*u*1), . . . , ρ(*un*)} = {*A*1,..., *An*}, then all classes in {*A*1,..., *An*} are in the same inheritance hierarchy, i.e., there is the unique least upper bound (top class) of them.
- 2. If ♦*A*{*t*1,..., *tn*} is in *f* and {ρ(*t*1), . . . , ρ(*tn*)} = {*P*1,..., *Pn*}, then the domain of every property *Pi* is subsumed by *A*, i.e., *dom*(*Pi*) *A*, for 1 ≤ *i* ≤ *n*.
- 3. If ♦*P*{*u*1,..., *un*} is in *f* and {ρ(*u*1), . . . , ρ(*un*)} = {*A*1,..., *An*}, then every class *Ai* is subsumed by the range of *P*, i.e., *Ai rng*(*P*), for 1 ≤ *i* ≤ *n*.
- 4. If *P*{*a*1,..., *an*} is in *f* , then ∃*x P*(*x*, *ai*) is satisfied in *O*, i.e., *O* | ∃*x P*(*x*, *ai*), for 1 ≤ *i* ≤ *n*.

In contrast to a structural graph (Fig. [1\)](#page-2-0) that provides a *graph-oriented view* of the entire ontology, the aim of a *faceted view* is to provide a *tree-oriented view* (a *hierarchical view*) of a part of this ontology relevant for the intended query.

A faceted view over the ontology is the basis of faceted search, where the formulation of queries proceeds over a hierarchical view of this ontology [\[25](#page-17-9)[,77\]](#page-18-12).

# <span id="page-6-0"></span>**4.3 Faceted interface**

We show now how a faceted view over a fac-ontology can be used to built a *faceted interface* supporting query formula<span id="page-7-1"></span>tion. Intuitively, a faceted interface is a faceted view equipped with some operational features allowing to label, extend and narrow the view.

**Definition 8** A *faceted interface* over a fac-ontology *O*, with a signature *Sig*(*O*) <sup>=</sup> UP <sup>∪</sup> BP <sup>∪</sup> Const, is a labeled tree *F I* = (*r*, *N*, *E*, λ, *State*) rooted in *r* ∈ *N*, such that:

- 1. *N* is a set on nodes, *E* ⊆ *N* × *N* is a set of edges defining a tree rooted in *r*, and λ is a node labeling function (*NL* ⊆ *N* is a set of leaves):
	- λ : *N* \ *NL* → {-, } × ({*root*} ∪ UP <sup>∪</sup> BP), – <sup>λ</sup> : *NL* <sup>→</sup> UP <sup>∪</sup> BP <sup>∪</sup> Const,

that assigns a pair (♦, *name*), ♦ ∈ {-, }, *name* ∈ {*root*} ∪ UP <sup>∪</sup> BP to every non-leaf node, and a *name* <sup>∈</sup> UP <sup>∪</sup> BP <sup>∪</sup> Const to any leaf node.

- 2. λ defines a faceted view over *O*, meaning that the result of the *depth-first-search* (DFS) serialization of *F I* is a faceted view over *O*.
- 3. A state of each node v ∈ *N* is defined by the quintuple function:

$$\begin{aligned} State(\upsilon) &= (Sel(\upsilon), AdOr(\upsilon), PosNeg(\upsilon), \\ NumRestr(\upsilon), Self(\upsilon)), \end{aligned}$$

where

- *Sel*(v) ∈ {ε, YES}—indicates whether v is selected (checked) or not.
- *AndOr*(v) ∈ {-, }—indicates that the set of children of a non-leaf node v is conjunctive (-) or disjunctive ( ).
- *PosNeg*(v) ∈ {ε, ¬}—indicates whether the subtree rooted in v is negated (*excluded*) or not.
- *NumRestr*(v) ∈ {ε, (≥, *k*)}—indicates if a number restriction is bound to a property node v.
- *Sel f* (v) ∈ {ε, *Self*}—indicates if a local reflexivity restriction is bound to an object property node v.

In Fig. [4,](#page-7-0) there is a faceted interface in the form implemented in the DAFO system. On the left-hand side, we see a sample initial faceted interface on which a user can operate using operations presented in the context menu shown on the right-hand side.

A faceted interface in DAFO is implemented as a labeled AND/OR tree such that:

- 1. Nodes are labeled by classes (*class nodes*), properties (*property nodes*), and constants (*constant nodes*). Constant nodes are visible only on demand.
- 2. There is a distinguished root, which is a property node labeled by the universal reflexive property *root*.

![](_page_7_Figure_21.jpeg)

<span id="page-7-0"></span>**Fig. 4** A sample faceted interface in the DAFO system and the context menu containing operations on it

3. At the beginning, any class node has the black color and the AND ("∧") label meaning that the set of its children is interpreted as a *conjunctive set*, while each property node has the red color and the OR ("∨") label, denoting that the set of its children is a *disjunctive set*.

A user operates interactively and iteratively on a faceted interface while building a faceted query. The user can refine the query by operating on this interface and by browsing and exploring the application domain modeled by the underlying fac-ontology. The operations on a faceted interface are listed in the context menu shown in Fig. [4.](#page-7-0)

Using the context menu, a user can set or change labels assigned to nodes, as well as to enlarge or reduce the interface. The set of labels defines the *state* of a node. A state is a quintuple determined by functions defined in Definition [8,](#page-7-1) i.e., *Sel*(), *AndOr*(), *PosNeg*(), *NumRestr*(), and *Sel f* ().

In addition, the following operations are used to modify the content of a faceted interface:

- 1. *AttributeV alues*(v)—is applicable to data property nodes to upload (from a database), insert, edit or remove values of this data property.
- 2. *Clone*(v)*(duplicate)*—allows to clone the subtree rooted in the indicated node. A new subtree is created and inserted as a child of the indicated node, and the inserted subtree is isomorphic, up to node identifiers, with the cloned subtree. Applicable to class- and property nodes except of the *root* node.
- 3. *E x plore*(v)—allows to display invisible parts of the facontology and include them into the faceted interface.

4. *RemoveALLUnchecked*(v)—removes from the faceted interface all unselected (unchecked) nodes.

A graphical form of a state of a sample faceted interface is shown in Fig. [2.](#page-3-1) It arises from Fig. [3](#page-6-1) by: adding one subtree as a result of using the *Explore(Person)* operation, adding a number restriction to *author O f* node, negating *affiliation* node, and labeling by *Self* the *authorConfPCMember* node.

<span id="page-8-2"></span>**Proposition 1** *A faceted interface F I determines an expression F with the syntax defined by the grammar:*

$$\begin{array}{lcl}F:: = \lozenge root\{U\_1, \ldots, U\_n\} \mid \neg F, \\ U: = A \mid \lozenge A(\{T\_1, \ldots, T\_n\}) \mid \neg U, \\ T: = P \mid Self P \mid \lozenge P\{U\_1, \ldots, U\_n\} \mid P\{a\_1, \ldots, a\_n\} \\ \mid (\succeq k) \lozenge P\{U\_1, \ldots, U\_n\} \mid (\succeq k) P\{a\_1, \ldots, a\_n\} \mid \neg T. \end{array}$$

*Proof* According to Definition [8](#page-7-1) p. (2), the DFS serialization of FI converts it to a faceted view, i.e., a nested facet conforming to the syntax given in Definition [6.](#page-6-3) Additionally, the subexpressions of this nested facet can be labeled by the negation symbol(¬), number restriction (≥ *k*) or by the local reflexivity symbol (*Self*). - 

# <span id="page-8-0"></span>**5 Faceted queries over ontological databases**

### **5.1 Syntax and semantics of faceted queries—faceted normal form (FacNF) of queries**

Any faceted interface in which all nodes are selected (i.e., all unselected are removed) defines a *faceted query*. A faceted interface imposes a tree-shaped structure on a faceted query and: (a) the negation symbol precedes a single node (not a set of nodes), (b) the set of all children of a node is: (i) either conjunctive or disjunctive and (ii) all children are of the same category, i.e., all are class nodes, property nodes or constant nodes.

**Definition 9** A *faceted query* is a faceted interface in which every node is selected.

<span id="page-8-1"></span>The semantics of a faceted query *F* is defined by translating it into a description logic expression. We assume that *root* is the *universal reflexive property* with the semantics: *root<sup>I</sup>* = {(*x*, *x*) | *x* ∈ -*I*}.

| τ (♦root{U1,, Un})  | = ∃root.♦{U1,, Un} =                               |
|---------------------|----------------------------------------------------|
|                     | ♦{U1,, Un},                                        |
| τ (A)               | = A,                                               |
| τ (♦A{T1,, Tn})     | = A<br>(♦{T1,, Tn}),                               |
| τ (P)               | = ∃P,                                              |
| τ (P{a1,, an})      | = ∃P.{a1,, an},                                    |
| τ (♦P{U1,, Un})     | = ∃P.♦{U1,, Un}.                                   |
| τ (Self P)          | = ∃P.Self ,                                        |
|                     | τ ((≥ k)♦P{U1,, Un}) = (≥ k)P.♦{τ (U1), , τ (Un)}, |
| τ ((≥ k)P{a1,, an}) | = (≥ k)P.{a1,, an},                                |
| τ (¬X)              | = ¬τ (X),                                          |

where *X* denotes any (not negated) expression of categories *F*, *U*, *T* (see Proposition [1\)](#page-8-2).

<sup>A</sup> *SROIQFac* query with the syntax conforming to the syntax of faceted queries will be called a query in a *faceted normal form* (FacNF). The following proposition follows from Definition [10.](#page-8-1)

<span id="page-8-3"></span>**Proposition 2** *A faceted query conforms to the syntax specified by the following grammar called a faceted normal form (FacNF):*

$$\begin{array}{lcl} q & ::= G \mid \neg G\\ G & ::= E\_1 \sqcup \dots \sqcup E\_n \mid E\_1 \sqcap \dots \sqcap E\_n\\ E & ::= A \mid A \sqcap (D\_1 \sqcup \dots \sqcup D\_n) \mid \\ & A \sqcap (D\_1 \sqcap \dots \sqcap D\_n) \mid \neg E\\ D & ::= \exists P \mid \exists P. \mathit{Self} \mid \exists P. G \mid (\succeq k) P. G \mid\\ & \exists P. \{a\_1, \dots, a\_n\} \mid (\succeq k) P. \{a\_1, \dots, a\_n\} \mid \neg D \end{array}$$

<span id="page-8-4"></span>*Example 3* The following *SROIQFac* queries are not in FacNF: *q*<sup>1</sup> = *A*<sup>1</sup> - (*A*<sup>2</sup> *A*3), *q*<sup>2</sup> = ∃*P*1.¬(*A*<sup>1</sup> - *A*2), *q*<sup>3</sup> = ∃*P*2.{*a*}-¬*A*1. In Example [4,](#page-9-1) they will be transformed into FacNFs.

# **5.2 Transformation of** *SROIQFac* **into FacNF**

We will show that the expressive power of the faceted query system is equal to that of *SROIQFac*. To this end, we will show that every *SROIQFac* query can be transformed into a query in FacNF. This means that for any query in *SROIQFac* there is a semantically equivalent query that can be formulated using the faceted interface.

**Theorem 1** *Every query in SROIQFac over a fac-ontology O* = (*V*, *C*, *A*) *can be transformed into an equivalent query in FacNF over O.*

*Proof* Let *<sup>q</sup>* be a query in *SROIQFac* over *<sup>O</sup>*. We transform *q* into FacNF as follows:

1. Every class *A* occurring in *q* is replaced by the left-hand side of its specialization (unfolding the specialization). If *<sup>O</sup>* | *<sup>A</sup>* -*C* ≡ *A*, then

*qspec* <sup>=</sup> *<sup>q</sup>*.*replace*(*A*, *<sup>A</sup>* -*C*),

and *qspec* arises from *<sup>q</sup>* by replacing *<sup>A</sup>* with *<sup>A</sup>* -*C*, and syntax of *C* is given in Definition [1.](#page-4-0)

2. *qspec* is converted into disjunctive normal form (DNF):

*qdnf* = *dnf*(*qspec*) = *q*<sup>1</sup> ··· *qm*,

where *qi* , 1 ≤ *i* ≤ *m* is a conjunction of queries in *SROIQFac*.

3. Every disjunct *qi* , 1 ≤ *i* ≤ *m*, containing negation of a top class is removed from *qdnf* , since it evaluates to the empty set. The reduced disjunction is:

*qred* = *reduce*(*qdnf*) = *q*<sup>1</sup> ··· *qn*.

4. Every disjunct *qi* , 1 ≤ *i* ≤ *n*, in *qred* can be rewritten into the form:

$$q\_l^{FacNF} = type(q\_{red}) \sqcap (D\_1^i \sqcap \dots \sqcap D\_{k\_l}^i),$$

where *D<sup>i</sup> <sup>j</sup>* , 1 ≤ *i* ≤ *ki* conforms to the syntax of *D* in Proposition [2.](#page-8-3) Then, *qFacNF <sup>i</sup>* is in FacNF, and *qred* ≡ *qFacNF red* , where *<sup>q</sup>FacNF red* is in FacNF, and

$$q\_{red}^{FacNF} = q\_1^{FacNF} \sqcup \cdots \sqcup q\_n^{FacNF} \dots$$

5. The procedure is applied recursively to every subquery *q* appearing in formulas of the form ∃*P*.*q*.

> -

<span id="page-9-1"></span>*Example 4* Let *q*1, *q*<sup>2</sup> and *q*<sup>3</sup> be queries specified in Example [3.](#page-8-4) Let classes *A*1, *A*2, and *A*<sup>3</sup> be defined by the following specializations:

*A*<sup>0</sup> - ∃*P* 1.*A* <sup>1</sup> ≡ *A*1, *A*<sup>0</sup> - ∃*P* 2.*A* <sup>2</sup> ≡ *A*2, *A*<sup>0</sup> - ∃*P* 3.*A* 3 ≡ *A*3, where

*A*<sup>0</sup> = *type*(*A*1) = *type*(*A*2) = *type*(*A*3) = *type*(∃*P*2), *A*<sup>∗</sup> = *type*(∃*P*1). Then:

1. Transforming *q*<sup>1</sup> = *A*<sup>1</sup> - (*A*<sup>2</sup> *A*3) into FacNF:

$$\begin{aligned}{}^c\_{\textit{fac}} \mathit{NF}(q\_{\textit{l}}) &= A\_0 \sqcap (\exists P'\_1. A'\_1 \sqcap \exists P'\_2. A'\_2) \\ {}^{\sqcup}\_{\sqcup A\_0} \sqcap (\exists P'\_1. A'\_1 \sqcap \exists P'\_3. A'\_3) .\end{aligned}$$

2. Transforming *q*<sup>2</sup> = ∃*P*1.¬(*A*<sup>1</sup> -*A*2) into FacNF:

$$\operatorname{FacNF}(q\_2) = A\_\* \sqcap \exists P\_1. (\neg A\_1 \sqcup \neg A\_2).$$

![](_page_9_Figure_23.jpeg)

<span id="page-9-2"></span>**Fig. 5** A sample of: **a** a query template and **b** a faceted interface generated for it

3. Transforming *q*<sup>3</sup> = ∃*P*2.{*a*}-¬*A*<sup>1</sup> into FacNF:

$$\begin{aligned} q\_{spec}^{\mathfrak{3}} &= \exists P\_2. \{a\} \sqcap \neg(A\_0 \sqcap \exists P'\_1. A'\_1), \\ q\_{dnf}^{\mathfrak{3}} &= \exists P\_2. \{a\} \sqcap \neg A\_0 \sqcup \exists P\_2. \{a\} \sqcap \neg\exists P'\_1. A'\_1, \\ q\_{red}^{\mathfrak{3}} &= \exists P\_2. \{a\} \sqcap \neg\exists P'\_1. A'\_1, \\ FcNF(q\_3) &= A\_0 \sqcap (\exists P\_2. \{a\} \sqcap \neg\exists P'\_1. A'\_1). \end{aligned}$$

# <span id="page-9-0"></span>**6 Faceted query formulation in DAFO**

# <span id="page-9-3"></span>**6.1 Examples of query formulation**

Now, we show how some representative queries can be formulated in the DAFO system [\[24](#page-17-3)].

Below, we consider queries, some of which concern the involvement of people in conferences—ACM conferences and/or conferences in the USA. Thus, a user can start with indicating the relevant classes to the intended query (as a query template, Fig. [5a](#page-9-2)): *Per son* as the type of the expected answers, as well as *ACMConf* and *USAConf*related somehow with the *Per son*. In response, a faceted interface is created, (Fig. [5b](#page-9-2)). Note that a person can be connected to conferences as a participant and/or as a PC member. Formulation of a query over a faceted interface requires a sequence of operations on this interface. Each state of a faceted interface represents a faceted query, so operations over the interface are transformations in a space of faceted queries.

![](_page_10_Figure_1.jpeg)

<span id="page-10-0"></span>**Fig. 6** Query *q*<sup>1</sup> (**a**) and its first-order syntax tree **b**

We will consider the following kinds of (faceted) queries:

- 1. Positive existential queries:
	- with default conjunctive and disjunctive facets query *q*1,
	- a disjunctive set is switched to a conjunctive one query *q*2,
	- a subtree is cloned (duplicated) and values of some data properties are added—query *q*3.
- 2. Queries with negation:
	- query with one negation (exclusion)—query *q*4,
	- query with double negation (equivalent to a universal quantification)—query *q*5.
- 3. Query with a number restriction—query *q*6,
- 4. Query with a local reflexivity (a cycle)—query *q*7.

For every query we provide:

- a natural language version,
- a DL version in *SROIQFac*,
- graphical forms of faceted queries (*q*1)–(*q*7), their firstorder forms, for (*q*1)–(*q*5), and a notation involving variables for(*q*6) and (*q*7)—all as screenshots on DAFO,
- operations on the faceted interface used to create the final faceted query.

# *1. Positive existential queries*

*q*1: *"Authors of papers presented at an ACM conference or at a conference in the USA"*

*q*<sup>1</sup> = *Per son* - ∃*authorConf* .(*ACMConf USAConf*).

In Fig. [6a](#page-10-0), the query is expressed as a faceted query, and in Fig. [6b](#page-10-0) there is a first-order form of *q*1. The query is created by performing the following sequence of operations on the faceted interface in Fig. [5b](#page-9-2):

pcMemberOf.Uncheck(); RemoveAllUnchecked().

*q*2: *"Authors of papers presented at an ACM conference in the USA"*

*q*<sup>2</sup> = *Per son* - ∃*authorConf* .(*ACMConf* -*USAConf*).

![](_page_10_Figure_25.jpeg)

<span id="page-10-1"></span>**Fig. 7** Query *q*<sup>2</sup> (**a**) and its first-order syntax tree (**b**)

![](_page_10_Figure_27.jpeg)

<span id="page-10-2"></span>**Fig. 8** Query *q*<sup>3</sup> (**a**) and its first-order syntax tree (**b**)

In Fig. [7,](#page-10-1) the query is depicted as a faceted query and its first-order form as a result of operating on the query/faceted interface in Fig. [6a](#page-10-0):

authorConf.SetToAND().

*q*3: *"Authors who presented her/his papers at ACM conferences in years 2014 and 2015"*

$$\begin{array}{l} q\_3 = \mathit{Author} \sqcap\\ \exists \mathit{a} 
boldsymbol{\mathit{Conf}}.(\mathit{ACM} \mathit{Conf} \sqcap \exists \mathit{conf} \mathit{Year}.\{\!\!\! 2014\!\!\!\/\!\/\!\/\!\/\!\/\!\/\!\/\!\/\/\!\/] \mathtt{F} \\ \exists \mathit{a} 
boldsymbol{\mathit{Conf}}.(\mathit{ACM} \mathit{Conf} \sqcap \exists \mathit{conf} \mathit{Year}.\{\!\!\!\/2015\!\!\/\!\/\/\/\/) \end{array}$$

Query *q*<sup>3</sup> is formulated taking Fig. [6a](#page-10-0) as a current form of a faceted interface. The following operations are performed on it:

```
USAConf.Uncheck(); ACMConf.Expand();
confYear.Check();
confYear.AttributeValue.AddValue('2014');
RemoveALLUnchecked();
AuthorConf.ClonSubtree();
confYear[2].AttributeValue.EditValue
('2014'/'2015');
```
A result of these operations is shown in Fig. [8.](#page-10-2)

![](_page_11_Figure_1.jpeg)

<span id="page-11-0"></span>**Fig. 9** Queries *q*<sup>4</sup> and *q*<sup>5</sup> and their first-order syntax trees

#### *2. Queries with negations*

*q*4: *"Authors of papers presented at ACM conferences but NOT in the USA"*

*q*<sup>4</sup> = *Per son* - ∃*authorConf* .(*ACMConf* -¬*USAConf*).

*q*<sup>4</sup> arises from *q*<sup>2</sup> by excluding the *USAConf*:

USAConf.Exclude();

The result is in Fig. [9–](#page-11-0)q4(a) and q4(b). In *q*5, negation is used to express a universal quantification. *q*5: *"Papers written only by PUTAuthors"*

*q*<sup>5</sup> = *Paper* - ¬∃*writtenBy*.¬*PUTAuthor*) = *Paper* -∀*writtenBy*.*PUTAuthor*.

Formulation of *q*<sup>5</sup> requires double exclusion (double negation) (Fig. [9\)](#page-11-0)—q5(a) and q5(b).

*3. Queries with number restrictions*

*q*6: *"Authors participating in over ten ACM conferences in the USA"*

*q*<sup>6</sup> = *Per son* - (> 10)*authorCon f* .(*AC MConf* -*USAConf*).

The query is formulated in DAFO as shown in Fig. [10.](#page-11-1) The argument of *count*(*x*1) indicates that *x*<sup>1</sup> is tested for the required number of distinct values. If *x* is connected to more than 10 different valuations of *x*1, then this valuation of *x* is returned as an answer. To formulate the query, we use the query in Fig. [7a](#page-10-1) as a faceted interface and perform the following operation on it:

authorConf.SetNumRestr.count()>10;

*4. Queries with local reflexivity (looking for cycles).*

*q*7: *"Authors of papers presented at conferences where the author was a PC member"*

*q*<sup>7</sup> = *Per son* -∃*authConfPCMember*.*Self* ,

![](_page_11_Figure_21.jpeg)

<span id="page-11-1"></span>**Fig. 10** Query *q*<sup>6</sup> (**a**), and its syntax tree (**b**)

![](_page_11_Figure_23.jpeg)

<span id="page-11-2"></span>**Fig. 11** Query *q*<sup>7</sup> (**a**) and its syntax tree (**b**)

<span id="page-11-3"></span>**Table 2** Expressiveness of faceted interfaces in four systems

| Quer y    | DAFO | BrowseRDF | Sewelis | SemFacet |
|-----------|------|-----------|---------|----------|
| A         | +    | +         | +       | +        |
| q1<br>q2  | +    | −         | +       | +        |
| q1<br>q2  | +    | +         | +       | +        |
| q1<br>¬q2 | +    | +         | +       | −        |
| ¬q        | +    | +         | +       | −        |
| ∃R.q,     | +    | +         | +       | +        |
| ∃R        | +    | +         | +       | +        |
| ∃R.{a}    | +    | +         | +       | +        |
| (≥ k)∃R.q | +    | −         | −       | +        |
| (≥ k)∃R   | +    | −         | −       | +        |
| ∃R.Self   | +    | +         | +       | −        |

where:

*authorConf* ◦ *confPCMember* ≡ *authConfPCMember*.

The query is formulated in DAFO as shown in Fig. [11a](#page-11-2). A syntax tree in Fig. [11b](#page-11-2) is an intermediate form with variables and a global variable @*x* that indicates objects which are connected with themselves by the property *authorConf* ◦ *confPCMember*. The occurrences of @*x* determine the equality [*x* = *x*3] saying that we are looking for objects assigned to *x* and *x*<sup>3</sup> that are equal.

### **6.2 Expressiveness of DAFO compared to other systems**

In Table [2,](#page-11-3) we compare the expressive power of four faceted query systems: DAFO, BrowseRDF [\[53\]](#page-18-13), Sewelis [\[34](#page-17-10)] and SemFacet [\[7](#page-17-2)[,64\]](#page-18-14).

The first column in the table contains queries in *SROIQFac*, which are used as reference points to interpret the semantics of operations in the compared systems. Since there are differences in the interpretations of some concepts and notions (especially, negation, aggregation, and recursion), we describe these differences in comments.

- 1. *Disjunction and conjunction.* In DAFO, each disjunction *q*<sup>1</sup> *q*2, and conjunction *q*<sup>1</sup> *q*2, requires that *q*<sup>1</sup> and *q*<sup>2</sup> are of the same type.
- 2. *Negation* A negation is computed as the complement with respect to a *guard*.
	- In a negation *q*<sup>1</sup> - ¬*q*2, the guard is *q*1, and *q*<sup>1</sup> and *q*<sup>2</sup> are of the same type.
	- In DAFO, a negation ¬*q* is *guarded* by the *type*(*q*).
	- In BrowseRDF, the negation can only concern the existence of an property. Negation is true for objects that do not have the negated property.
	- In Sewelis, the complement is computed with respect to the set of all objects (the universal class).
- 3. *Navigation, recursion, reachability*. In all analyzed systems, existential restrictions: ∃*R*.*q*, ∃*R*, ∃*R*.{*a*}, are fundamental for navigation through the underlying ontology, where: (a) ∃*R*.*q* denotes a set of objects connected via an object property *R* with objects in *q*, (b) ∃*R* denotes a set of objects having a property *R* (irrespective of its value), ∃*R*.{*a*} is a set of objects connected via a data property *R* with a constant *a*. *R* can denote a property *P* or its inversion *P*− (only for object properties). Properties can be recursively composed, expressing in this way a (restricted) form of a recursion. In DAFO, composed properties can be defined as *chains* of other properties. In SemFacet, so called *reachability atoms*, Next(*x*, *y*) and Next+(*x*, *y*) are introduced. They denote (dynamically) a property, or a sequence of properties, leading from *x* to *y*.
- 4. *Aggregation.* In *SROIQ*, an aggregation is limited to a *number restriction*. We also do this in DAFO restricting ourselves to the *count*() function.
- 5. *Cycles.* A reflexivity restriction ∃*R*.*Self* , denotes objects which are connected via *R* with themselves. In this way, cycles can be found. In DAFO and in Sewelis, this is achieved by means of special variables (in DAFO prefixed by @). Two different occurrences in a query of the same variable @*x* indicate that a value of @*x* is connected with itself by a property (possibly composed) specified in the query.

# <span id="page-12-0"></span>**7 Materialization of constraint rules**

Given a fac-ontology *O* = (*V*, *C*, *A*), our goal is to convert *O* into such an ontological database *Odb* = (*V*, *C*, *Adb*) that *Adb* is a minimal model of *A* ∪ *C*, i.e., *Adb* | *A* ∪ *C*. Such the *Adb* can be obtained as the *chase* of *A* with respect to *C*, *Adb* = *chaseC*(*A*). In other words, *A* is included in *Adb* and *C* is *materialized* in *Adb*.

In general, the chase procedure can: (a) be infinite, (b) terminate with the fail, (c) produce a finite set of facts containing *A* and all consequences of *C*. Some rules in *C*, namely disjointness and functionality, are used to verify consistency of the ontological database. The functionality rules can also be used to discover some missing values, represented by labeled nulls.

We divide *C* into two subsets: *C*<sup>1</sup> and *C*2:

1. *C*<sup>1</sup> contains rules of the form: *C*<sup>1</sup> *C*<sup>2</sup> and *R*<sup>1</sup> *R*2. Their first-order forms are *tuple-generating dependencies*:

$$-\,\forall \mathbf{x}, \mathbf{y} (\boldsymbol{\varphi}(\mathbf{x}, \mathbf{y}) \to \exists \mathbf{z} \boldsymbol{\psi}(\mathbf{x}, \mathbf{z})),$$

where **x**, **y**, **z** are tuples of variables and ϕ(**x**, **y**), ψ(**x**, **z**) are conjunctions of atoms over all the given variables and constants. These rules are used to chase new facts.

2. *C*<sup>2</sup> contains disjointness rules, *A*<sup>1</sup> ¬*A*2, *R*<sup>1</sup> ¬*R*2, and functionality rules (funct *S*), with first-order forms, respectively:

$$-\,\forall \mathbf{x} (\varphi(\mathbf{x}) \to \neg \varphi(\mathbf{x})),$$

– ∀**x**(ϕ(**x**) → *x*<sup>1</sup> = *x*2), where *x*1, *x*<sup>2</sup> ∈ **x**.

These rules are mainly used to check if the chased set of facts is consistent.

*Chasing with respect to*: σ = ∀**x**, **y**(ϕ(**x**, **y**) → ∃**z**ψ(**x**, **z**)) Let <sup>ω</sup> be a valuation, <sup>ω</sup> : **<sup>x</sup>** <sup>∪</sup> **<sup>y</sup>** → Const, such that *A* | ϕ(ω(**x**), ω(**y**)). Then:

- 1. If *A* | ∃**z**ψ(ω(**x**), **z**), then as a result of *applying* σ *to A with valuation* ω we obtain the set *A* that extends *A* with facts *A*(ω (v1)) and *R*(ω (v2), ω (v3)), where *A*(v1) and *R*(v2, v3) occur in ψ(**x**, **z**), v*<sup>i</sup>* ∈ **x** ∪ **z**, for *i* = 1, 2, 3, and: (a) if v*<sup>i</sup>* ∈ **x**, then ω (v*i*) = ω(v*i*); (b) if v*<sup>i</sup>* ∈ **z**, then ω (v*i*) is a fresh labeled null N. We denote this as *<sup>A</sup>* σ,ω −−→ *A* .
- 2. If *A* | ∃**z**ψ(ω(**x**), **z**), then σ is not applicable to *A* with the valuation ω.

*Chasing with respect to*: σ = ∀**x**(ϕ(**x**) → ¬ψ(**x**)). Let <sup>ω</sup> : **<sup>x</sup>** → Const be a valuation such that *A* | ϕ(ω(**x**)). Then:

- 1. If *A* | ψ(ω(**x**)), then the *result of applying* σ *to A with* <sup>ω</sup> is "failure," which is denoted by *<sup>A</sup>* σ,ω −−→ FAIL.
- 2. If *A* | ψ(ω(**x**)), then σ is not applicable to *A* with the valuation ω.

*Chasing with respect to*: σ = ∀**x**(ϕ(**x**) → *x*<sup>1</sup> = *x*2).

Let <sup>ω</sup> : **<sup>x</sup>** → Const be a valuation such that *A* | ϕ(ω(**x**)). Then,

- 1. if ω(*x*1) = ω(*x*2) and neither ω(*x*1) nor ω(*x*2) is a labeled null, then *<sup>A</sup>* σ,ω −−→ FAIL.
- 2. if ω(*x*1)is a labeled null <sup>N</sup>, then *<sup>A</sup>* is obtained from *<sup>A</sup>* by replacing every occurrence of <sup>N</sup> in *<sup>A</sup>* by ω(*x*2), denoted by *<sup>A</sup>* σ,ω −−→ *A* .

# **7.1 Termination of chase**

The chase can be defined as the *data exchange* problem [\[21,](#page-17-11) [33\]](#page-17-12), and can be understood as a pair (**T**, *C*), where **T** = *Sig*(*C*) <sup>∪</sup> Const is a target schema, and *<sup>C</sup>* is a set of target-totarget dependencies. A *solution* of an instance *A* of **T** with respect to *C* is an instance *A* of **T** such that *A* | *A* ∪ *C*. In general, an instance *A* can have infinitely many solutions. In particular, if *A* is a solution for *A* with respect to *C*, then every *A* containing *A* is also a solution for *A*. A set *A* of facts is the *universal solution* of *A* with respect to *C* if (a) *A* is a solution of *A*, and (b) for each solution *A* of *A* there is a homomorphism *h* from *A* to *A* (*h* is the identity on constants). Intuitively, a universal solution contains no more and no less information than that specified by the given data exchange problem.

The chase procedure *chaseC*(*A*) is guaranteed to terminate in polynomial time producing the universal solution for *A* with respect to *C* if the *dependency graph* of *C* is *weakly acyclic* [\[5](#page-17-13)[,33](#page-17-12)].

**Definition 11** The *dependency graph*, *DG*(*C*), over a set *C* of constraint rules is constructed as follows: (a) for every class *A* occurring in *C* there is a node (*A*, 1) in *DG*(*C*); (b) for every property *R* occurring in *C* there are two node (*R*, 1) and (*R*, 2) in *DG*(*C*); (c) for every rule of the form *A*<sup>1</sup> ∃*R*.*A*<sup>2</sup> in *C* there are three edges in *DG*(*C*):

– (*A*1, 1) → (*R*, 1), (*R*, 2) → (*A*2, 1) – *regular* edges, – (*A*1, 1) <sup>∗</sup> −→ (*R*, 2) – a *special* edge (labeled by ∗).

*C* is *weakly acyclic* if its dependency graph *DG*(*C*) does not have any *weak cycle*, i.e., a cycle going through a special edge.

Sets of constraint rules in fac-ontologies usually have a lot of weak cycles because properties and their inverses are usually taken into account.

<span id="page-13-0"></span>*Example 5* Let *<sup>C</sup><sup>a</sup>* = {σ1, σ2}, where

σ<sup>1</sup> = *Author* ∃*author O f* .*Paper*, σ<sup>2</sup> = *Paper* ∃w*rittenBy*.*Author*.

![](_page_13_Figure_15.jpeg)

<span id="page-13-1"></span>**Fig. 12** The dependency graphs for *C<sup>a</sup>* in Example [5](#page-13-0) has a weak cycle: (*Author*, 1) <sup>∗</sup> −→ (*author O f* , <sup>2</sup>) −→ (*Paper*, <sup>1</sup>) <sup>∗</sup> −→ (w*rittenBy*, 2) −→ (*Author*, 1)

The dependency graph of *C<sup>a</sup>* is depicted in Fig. [12.](#page-13-1) The graph has a weak cycle, and this leads to an infinite chase. On the other hand, a finite solution exists, but this solution is not the universal solution. We see that

*<sup>A</sup>* = {*Author*(*a*), *author O f* (*a*, <sup>N</sup>1), *Paper*(N1), <sup>w</sup>*rittenBy*(N1, *<sup>a</sup>*)},

is a solution for *A*. However, a solution is also

*<sup>A</sup>* = {*Author*(*a*), *author O f* (*a*, <sup>N</sup>1), *Paper*(N1), w*rittenBy*(N1, N2), *Author*(N2), *author O f* (N2, N3), *Paper*(N3), w*rittenBy*(N3, *<sup>a</sup>*)}.

However, there is no any homomorphism *h* : *A* → *A* since *<sup>h</sup>* is not identity on *<sup>a</sup>*, because *<sup>h</sup>*(*a*) <sup>=</sup> *<sup>a</sup>* and *<sup>h</sup>*(*a*) <sup>=</sup> <sup>N</sup>2. In this case, the universal solution for *A* with respect to *C*<sup>1</sup> does not exist.

# **7.2 Reflexive weak cycles**

Now, we identify a class of weak cycles in dependency graphs and call them*reflexive weak cycles*(RWC). For RWC, we will modify the chase problem. The modified chase will terminate with a universal solution. This universal solution is also a solution (but not a universal solution) for the chase before the modification.

**Definition 12** Let *C* be a set of constraint rules and *DG*(*C*) a dependency graph over *C*. A *reflexive weak cycle* (RWC) in *DG*(*C*) is a cycle of the form

$$(A\_1, 1) \xrightarrow{\ast} (R\_1, \mathcal{Z}) \xrightarrow{\ast} (A\_2, 1) \xrightarrow{\ast} \cdots \xrightarrow{\ast} (R\_n, \mathcal{Z}) \xrightarrow{} (A\_1, 1),$$

such that:

1. Every subpath (*Ai*, 1) <sup>∗</sup> −→ (*Ri*, 2) −→ (*Ai*+1, 1) is implied by the rule *Ai* ∃*Ri* .*Ai*+<sup>1</sup> ∈ *C*, 1 ≤ *i* ≤ *n*, *n* ≥ 2, *An*+<sup>1</sup> = *A*1, 2. *C* | *R*<sup>1</sup> ◦···◦ *Rn*−<sup>1</sup> *R*<sup>−</sup> *n* .

<span id="page-14-0"></span>A RWC is abbreviated as (*A*1, *R*1, *A*2,..., *An*, *Rn*, *A*1).

**Proposition 3** *Let dom*(*R*1) = *A and R*<sup>1</sup> ◦···◦ *Rn*−<sup>1</sup> *R*<sup>−</sup> *n . Then, A* ≡ ∃(*R*<sup>1</sup> ◦···◦ *Rn*).*Self .*

*Proof* We prove the proposition for *<sup>n</sup>* <sup>=</sup> 2. Let us assume that *R*<sup>1</sup> *R*<sup>−</sup> <sup>2</sup> and *dom*(*R*1) = *A*.

⇒If *A*(*a*)then for some *b*, *R*1(*a*, *b*) and *R*2(*b*, *a*). Therefore, (∃(*R*<sup>1</sup> ◦ *R*2).*Self*)(*a*).

⇐ Now, let (∃(*R*<sup>1</sup> ◦ *R*2)*Self*)(*c*). Then, for some *d*, *R*1(*c*, *d*) and *R*2(*d*, *c*). Thus, *A*(*c*).

This reasoning can easily be extended to any *n* > 2. - 

Proposition [3](#page-14-0) shows that the chain *R*<sup>1</sup> ◦···◦ *Rn* of properties belonging to RWC is "local reflexive," i.e., any object in the domain of *R*<sup>1</sup> is connected with itself via this chain. This property justifies the term *"reflexive"* for the class of weak cycles under consideration.

*Example 6* Let σ<sup>1</sup> and σ<sup>2</sup> be defined in Example [5,](#page-13-0) and σ 2 = *Paper* ∃*re*v*ie*w*ed By*.*Author*. Let *C* = {σ1, σ2}, and *C* = {σ1, σ <sup>2</sup>}. Then, both:

*<sup>p</sup>*<sup>1</sup> <sup>=</sup> (*Author*, <sup>1</sup>) <sup>∗</sup> −→ (*author O f* , <sup>2</sup>) −→ (*Paper*, <sup>1</sup>) <sup>∗</sup> −→ (w*rittenBy*, 2) −→ (*Author*, 1), and

*<sup>p</sup>*<sup>2</sup> <sup>=</sup> (*Author*, <sup>1</sup>) <sup>∗</sup> −→ (*author O f* , <sup>2</sup>) −→ (*Paper*, <sup>1</sup>) <sup>∗</sup> −→ (*re*v*ie*w*ed By*, 2) −→ (*Author*, 1) are weak cycles in, respectively, *DG*(*C* ) and *DG*(*C*). But only *p*<sup>1</sup> is RWC since *author O f* w*rittenBy*−.

We define a *p-aware chase* as a chase in the presence of a RWC *p*. The aim is that the *p-aware* chase terminates with a solution, although not a universal solution.

**Definition 13** Let *C* be a set of constraint rules,*A*a set of facts over *Sig*(*C*)∪Const, and *<sup>p</sup>* <sup>=</sup> (*A*1, *<sup>R</sup>*1, *<sup>A</sup>*2,..., *An*, *Rn*, *<sup>A</sup>*1) a RWC over *C*. A *p-aware chase* of *A* with respect to *C*, *chase*{*p*} *<sup>C</sup>* (*A*), is a chase *chaseC*(*A*), such that *<sup>C</sup>* <sup>=</sup> *<sup>C</sup>*\{*An* ∃*Rn*.*A*1}.

<span id="page-14-1"></span>Intuitively, our goal is to prevent firing of the rule *An* ∃*Rn*.*A*1. Instead, the rule *R*<sup>1</sup> ◦···◦ *Rn*−<sup>1</sup> *R*<sup>−</sup> *<sup>n</sup>* is applied.

*Example 7* Let *<sup>C</sup><sup>b</sup>* = {σ1, σ2, σ3}, <sup>σ</sup>1, σ<sup>2</sup> be defined in Example [5,](#page-13-0) and σ<sup>3</sup> = *author O f* w*rittenBy*−. Then,

*p* = (*Author*, *author O f* , *Paper*, w*rittenBy*, *Author*)

is the weak cycle presented in Fig. [12.](#page-13-1) Because of σ3, *p* is also a RWC. Let

*A* = {*Author*(*a*)}.

Then a *p-aware* chase, *chase*{*p*} {σ1,σ2,σ3}(*A*), is the chase

*chase*{σ1,σ3}(*A*).

First-ordered forms of the rules involved in the chase are:

$$\begin{array}{l} \sigma\_{\text{l}} = \forall \mathbf{x} \, (Author(\mathbf{x}) \to \exists \mathbf{y} \, \mathbf{z} \, (auth \, Of(\mathbf{x}, \mathbf{y}) \land Paper(\mathbf{y})),\\ \sigma\_{\text{3}} = \forall \mathbf{x} \, \mathbf{y} (auth \, Of(\mathbf{x}, \mathbf{y}) \to written \, Bry(\mathbf{y}, \mathbf{x})). \end{array}$$

The *p-*aware chase consists of the following steps:

$$\begin{array}{l} \{Author(a)\} \xrightarrow{\sigma\_{1}, [\mathbb{x} \mapsto a]} \{Author(a), author\,Of(a, \mathbb{N}\_{1}),\\ \operatorname{Papor}(\mathbb{N}\_{1})\} \xrightarrow{\sigma\_{3}, [\mathbb{x} \mapsto a, \mathbb{y} \mapsto \mathbb{N}\_{1}]} \{Author(a),\\ auhtor\,Of(a, \mathbb{N}\_{1}), \operatorname{Papor}(\mathbb{N}\_{1}), writtenBy(\mathbb{N}\_{1}, a)\} = \mathcal{A}'. \end{array}$$

*A* is a *universal solution* for *A* with respect to {σ1, σ3}, and *A* is also a *solution* for *A* with respect to {σ1, σ2, σ3}, but not a universal solution. To show that *A* is not a universal solution for *A* with respect to {σ1, σ2, σ3}, let us note that, for example,

*<sup>A</sup>* = {*Author*(*a*), *author O f* (*a*, <sup>N</sup>1), *Paper*(N1), w*rittenBy*(N1, N2), *Author*(N2), *author O f* (N2, N3), *Paper*(N3), w*rittenBy*(N3, <sup>N</sup>2), w*rittenBy*(N1, *<sup>a</sup>*)}.

is also a solution for *A* with respect to {σ1, σ2, σ3}, but there is not a homomorphism *h* : *A* → *A* preserving constants (we have *<sup>h</sup>*(*a*) <sup>=</sup> *<sup>a</sup>* and *<sup>h</sup>*(*a*) <sup>=</sup> <sup>N</sup>2).

**Theorem 2** *Let p* = (*A*1, *R*1, *A*2,..., *An*, *Rn*, *A*1) *be a RWC over a set <sup>C</sup> of constraint rules. Let chase*{*p*} *<sup>C</sup>* (*A*) <sup>=</sup> *chaseC*(*A*) = *A , C* = *C* \ {*An* ∃*Rn*.*A*1}*, be a p-aware chase. Then A is a universal solution for A with respect to C and a solution for A with respect to C.*

*Proof* The set *<sup>C</sup>* is weakly acyclic, so *chaseC*(*A*) produces a universal solution *A* for *A* with respect to *C* . We have to show that *A* is also a solution for *A* with respect to *C*, i.e., that also the removed rule, σ = *An* ∃*Rn*.*A*1, holds in *A* :

$$\mathcal{A}' \models A\_n \sqsubseteq \exists \mathcal{R}\_n. A\_1. \tag{2}$$

Let *A*<sup>1</sup> be a result of chasing just before applying the rule σ = ∀*x*, *y*((*R*<sup>1</sup> ◦···◦ *Rn*−1)(*x*, *y*) → *Rn*(*y*, *x*)). Then

$$\mathcal{A}\_{\mathbb{I}} \vdash \{ A\_{\mathbb{I}}(a\_{\mathbb{I}}), (R\_{\mathbb{I}} \diamond \cdots \diamond R\_{n-1})(a\_{\mathbb{I}}, a\_n), A\_n(a\_n) \},$$

for some constants *a*1, *an*. Now, the rule σ is applicable. After the application:

$$\mathcal{A}\_1 \xrightarrow{\sigma', [\mathfrak{x} \mapsto a\_1, \mathfrak{y} \mapsto a\_n]} \mathcal{A}\_1 \cup \{ R\_n(a\_n, a\_1) \} = \mathcal{A}\_2,$$

and *A*<sup>2</sup> also satisfies σ, i.e., *A*<sup>2</sup> | *An* ∃*Rn*.*A*1.

The above reasoning shows that every result *A* = *chaseC*(*A*) of chasing that satisfies σ also satisfies σ. This proves that *A* is a solution for *A* with respect to *C*. However, *A* is not a universal solution for *A* with respect to *C*, that was shown in Example [7.](#page-14-1) - 

The *p*-aware chase can be generalized to an arbitrary set of RWCs. For *p* = (*A*1, *R*1, *A*2,..., *An*, *Rn*, *A*1), we denote σ (*p*) = *An* ∃*Rn*.*A*1.

**Definition 14** Let *C* be a set of constraint rules, *A* a set of facts over *Sig*(*C*) <sup>∪</sup> Const, and {*p*1,..., *pk* }, *<sup>n</sup>* <sup>≥</sup> 1, a set of RWCs. A {*p*1,..., *pk* }*-aware chase* of *A* with respect to *<sup>C</sup>*, *chase*{*p*1,...,*pn* } *<sup>C</sup>* (*A*), is *chaseC*(*A*), such that *<sup>C</sup>* <sup>=</sup> *<sup>C</sup>* \ {σ (*p*1), . . . , σ (*pk* )}.

# <span id="page-15-0"></span>**8 Related work**

#### **8.1 Ontologies and databases**

There is a long research tradition in investigating similarities and differences between ontologies and databases [\[1](#page-17-8)[,13](#page-17-14)[,14](#page-17-15)[,49](#page-18-15)[,50](#page-18-16)]. The investigations concern both the underlying theories and behavior in practice. Ontologies offer richer semantics due to the ability to represent both extensional (by means of a set of facts) and intensional (by means of a set of rules) knowledge. Thus, ontologies are commonly considered as the best method to specify conceptualizations of application domains (conceptual models) [\[11](#page-17-16)[,20](#page-17-17)[,40\]](#page-18-17). However, the efficiency of query answering on ontologies is unsatisfactory [\[17](#page-17-7)[,37](#page-18-2)]. Databases, on the contrary, are characterized by less expressive semantics but higher efficiency. Therefore, databases based on relational or graph models can be used to store ontological instances. Other differences relate to the interpretation of the rules [\[1](#page-17-8)[,50\]](#page-18-16). In particular, all rules are interpreted as integrity constraints in databases and as deductive rules in ontologies. To overcome the differences in treating rules in ontologies and in databases, in [\[50\]](#page-18-16) a concept of the extended knowledge base is proposed, where the set of rules is divided into a set of integrity constraint rules (satisfied in the knowledge base) and a set of deductive rules (representing intensional knowledge). Then, integrity constraints can be disregarded while answering positive queries [\[50](#page-18-16)[,51\]](#page-18-18). The combination of ontologies and databases has found a satisfactory solution in ontology-based data access (OBDA) [\[16](#page-17-18)[,19](#page-17-19)[,63](#page-18-19)[,65](#page-18-20)[,75\]](#page-18-1), where the TBox of an ontology is used as a global schema over a set of integrated databases.

An OBDA specification is a triple *P* = (*O* ,*M* , *Sch*), where *O* = (*T* , *A*) is an ontology, *Sch* is a data source schema, and *M* is a mapping from *Sch* to the signature of *O* [\[75](#page-18-1)]. For an instance, *D* of *Sch*, *A* = *M* (*D*), and *M* is a set of source-to-target tuple-generating dependencies of the form: ∀**x**, **y**(
(**x**, **y**) → α(**x**)), where is a conjunction of *n*-ary atoms, and α is an unary or a binary atom. A query *q* over *O* is rewritten with respect to *T* and *M* into a firstorder query over the data source. Notice that then: (a) the rules in *T* are not satisfied in the data source; instead, they are used in the first-order query rewriting and are not very expressive, usually in *DL-Lite* [\[17](#page-17-7)] or sticky [\[37](#page-18-2)], (b) integrity constraints are defined and managed in the data source management system.

In this paper, we present a DAFO approach to ontological databases that differs from the OBDA as follows. Let *P* = (*Odb*,*M*, *Sch*) be a DAFO specification, and *P* = (*O* ,*M* , *Sch*), *O* = (*T* , *A*), an OBDA specification. Then: (a) *Odb* = (*V*, *C*, *Adb*), where *T* is a subset of *C*, *T* ⊆ *C*, and is satisfied in *Adb*, as a result of the chase procedure; (b) *M* is a mapping from the signature of *Adb* into *Sch* consisting of dependencies of the form: ∀**x**(α(**x**) → ∃**y**R(**x**, **y**)), where α ranges over unary and binary atoms, and R is an *n*-ary relation name, *n* ≥ 1. The inversion of *M*, in the sense of Fagin [\[5](#page-17-13)[,32\]](#page-17-20), is an OBDA mapping, (c) *V* is a set of rules defining intensional predicates, i.e., predicates not occurring in *Adb*; these predicates are used in query formulation and "compensate" the poor expressive power of mapping dependencies in DAFO; (d) the query rewriting in DAFO is reduced to unfolding intensional predicates with their extensional definitions, and to apply mappings in translating first-order queries into SQL queries.

#### **8.2 Faceted queries over ontologies**

In the traditional setting, the reasoning tasks in ontologies include satisfiability and subsumption of concept expressions (with respect to a TBox), and instance checking (with respect to an ABox) [\[9](#page-17-0)[,61\]](#page-18-6). New applications of ontology-based systems require not only reasoning capabilities, but also query answering mechanisms [\[7](#page-17-2)[,16](#page-17-18)[,37](#page-18-2)[,50](#page-18-16)[,69](#page-18-21)]. Moreover, in [\[9](#page-17-0)] it was shown that reasoning tasks over an ontology can be realized by means of queries. Queries over ontologies can be expressed using different query mechanisms [\[2](#page-17-21)[,7](#page-17-2)], from first-order logic formulas to graph query languages, such as SPARQL [\[67\]](#page-18-22), Cypher [\[36](#page-18-23)[,39](#page-18-24)[,68](#page-18-25)], or Gremlin [\[4](#page-17-22)]. However, such expressive languages as SPARQL are not well-suited for end-users. Thus, we observe attempts to develop interactive graphic-oriented ontology query languages such as, for example, ViziQuer [\[78](#page-18-26)], SPARQLGraph [\[62\]](#page-18-27), OptiqueVQS [\[66](#page-18-28)[,73\]](#page-18-29), SEWASIE [\[10](#page-17-23)], OntoVQL [\[31\]](#page-17-24), NL-Graphs [\[27](#page-17-25)], K-search [\[12](#page-17-26)], which present ontology views combined with form-based query entry interfaces. A promising alternative to the aforementioned languages is approaches based on faceted search resulting in faceted queries [\[7](#page-17-2)[,70\]](#page-18-11).

Faceted search has emerged as a foundation for interactive information browsing and retrieval and has become increasingly prevalent in online information access systems, particularly for e-commerce and site search [\[7](#page-17-2)[,70](#page-18-11)[–72](#page-18-30)[,74](#page-18-31)]. Especially significant is combining browsing and searching in more flexible ways to support non-professional end-users in finding information. The implementation of the browsing paradigm allows for exploring and expressing information needs in interactive and iterative ways [\[42](#page-18-32)[,72](#page-18-30)[,74](#page-18-31)]. Most importantly, browsing and exploring concerns both data and metadata. Faceted queries are created interactively and iteratively during the faceted search.

The first systems of this kind are /facet [\[43\]](#page-18-33) and gFacet [\[42](#page-18-32)], which identify and implement the basic features of the semantic faceted search paradigm. These two systems operate over RDF data. The expressive power of /facet and gFacet is low. Multiple selections are connected by a logical AND and thus restrict the result set to only objects that satisfy all selections [\[42\]](#page-18-32). Exploration is restricted to a navigation during which a conjunction of constraints is added to or removed from a dynamically created faceted query. In gFacet [\[42\]](#page-18-32), facets and result sets are represented as nodes connected by directed edges labeled by semantic relations between nodes in a graph visualization. More expressive faceted navigation for RDF data was proposed in BrowseRDF [\[53](#page-18-13)]. The proposed set of operators describes faceted browsing in terms of a set of manipulations and is defined on an RDF graph. The operators are: basic selection, existential selection, not-existential selection, join and their inversions, and intersection. Further enrichment of the expressive power of exploratory search was proposed in such systems as: Sewelis [\[35](#page-17-27)] (allows to search for a limited form of cycles), VisiNav [\[41](#page-18-34)] and OpenLink Virtuose [\[30](#page-17-28)]. Faceted search solutions are offered as commercial products by some leading software vendors (e.g., ORACLE [\[52\]](#page-18-35), Microsoft [\[45](#page-18-36)], IBM [\[23\]](#page-17-29) and Apache [\[3\]](#page-17-30)) There are a large number of implemented systems, which are mostly based on RDF/S. About 30 faceted search systems based on RDF/S datasets are surveyed recently in [\[72\]](#page-18-30).

Results in [\[7\]](#page-17-2) can be considered a milestone in the development of the theory of faceted search systems. The authors propose a rigorous theoretical underpinning for faceted search in the context of RDF and OWL 2 ontology profiles. The expressive power of the faceted search language considered in [\[7](#page-17-2)] is limited to the description logic positive existential queries (PEQ). The theory is used in the implementation of SemFacet [\[6](#page-17-31)[,38](#page-18-37)] and Ontop [\[15](#page-17-32)]. Next, the query language in SemFacet has been extended with a restricted form of aggregation and recursion [\[47](#page-18-38)[,64](#page-18-14)]. A first approach to view an ontology as a nested facet system for human data integration was proposed in [\[77\]](#page-18-12).

In this paper, we extend the concept of "flat" facets proposed in [\[7](#page-17-2)] to *nested facets*, which are used to propose a *faceted view* of fac-ontologies. A faceted view equipped with a set of operations is defined as a *faceted interface* allowing to explore the ontology and creating queries. The queries are formulated using this graphical tree-shaped faceted interface. This way of querying determines so-called *faceted normal form* (FacNF) of queries. We prove that every expression in *SROIQ* (with some limitations) can be converted into FacNF, thus can be created using a faceted interface. This way of formulating queries requires that the ontology meets certain conditions. These conditions are met by fac-ontologies, which are proposed in this paper.

# <span id="page-16-0"></span>**9 Conclusions and future work**

In this paper, we have proposed a formal approach and a methodology to create ontological databases with a faceted interface treated as a builder for faceted queries. We identified a class of ontologies, called fac-ontologies, over which a faceted human-oriented interface can be created. We have specified conditions for the class of fac-ontologies and defined the concept of a nested facet, which provides a hierarchical faceted view over fac-ontologies. A hierarchical view with a set of operations constitutes a faceted interface used to formulate queries on the fac-ontology. The set of rules in the TBox consists of two sets: (a) a set *V* of rules defining intensional predicates, which extend the vocabulary and enrich the expressive power of the fac-ontology, and (b) a set *C* of constraint rules, which are used in the chase procedure to transform a fac-ontology into an ontological database. We show that any query in the description logic *SROIQ* (with some restrictions) can be formulated using the proposed faceted interface.

We see many directions for future work. (1) *Transforming heterogeneous data sources into a relational database*. In the DAFO approach, an ontology is mapped to a relational database. In this way, an ontological specification of data sources based on, e.g., XML [\[29](#page-17-33)], JSON [\[26](#page-17-34)] or RDF [\[60](#page-18-39)], can be mapped and materialized in a relational database. Then, the integrated repository can be effectively queried using a faceted interface. (2) *Exploratory search*. Our solution has a rather limited capability to exploratory search of ontologies, which is a characteristic of faceted search systems. Therefore, it would be interesting to enrich the faceted interface with an extended ability to navigate through ontologies with the structure unknown to the user.

The considerations in the paper are based on the DAFO (*Data Access based on Faceted queries over Ontologies*), that was implemented on the top of a commercial relational database engine and ensures high efficiency of query answering. Some details of the implementation as well as the high efficiency of query answering are reported in our previous work [\[55](#page-18-7)[,56](#page-18-40)[,58](#page-18-41)].

The performance of DAFO was evaluated on the basis of bibliographic datasets containing data on authors, papers, proceedings, and conferences [\[56](#page-18-40)]. The basic dataset was prepared by extracting data from DBLP [1](#page-16-1) resources (from XML, HTML, and BibTex files), and enriched with data extracted from personal and conference home pages. This basic dataset includes data on 1907 conferences, 1853 proceedings, 3818

<span id="page-16-1"></span><sup>1</sup> DBLP Computer Science Bibliography, [http://dblp.org.](http://dblp.org)

papers, 65 affiliations, and 61 authors. The dataset is organized in the form of an ontological database. The DAFO server is written in C# with NET Core 2.2, DAFO client is written in JavaScript, and the extensional part of the ontological database is stored in SQL Server (under the license Microsoft Imagine Premium). The total execution time consists of the time of: (1) transforming the faceted query into an extensional first-order form, (2) translating the extensional form into an SQL query, (3) executing the SQL query. It turns out that step (1) is the most time-consuming. The experiments showed that the total response time is very promising and for queries similar to the examples in Sect. [6.1](#page-9-3) is less than 50 ms. The system is available on GitHub [\[24](#page-17-3)].

**Acknowledgements** This work was supported by the Polish Ministry of Education and Science, grant 0311/SBAD/0710.

**Open Access** This article is licensed under a Creative Commons Attribution 4.0 International License, which permits use, sharing, adaptation, distribution and reproduction in any medium or format, as long as you give appropriate credit to the original author(s) and the source, provide a link to the Creative Commons licence, and indicate if changes were made. The images or other third party material in this article are included in the article's Creative Commons licence, unless indicated otherwise in a credit line to the material. If material is not included in the article's Creative Commons licence and your intended use is not permitted by statutory regulation or exceeds the permitted use, you will need to obtain permission directly from the copyright holder. To view a copy of this licence, visit [http://creativecomm](http://creativecommons.org/licenses/by/4.0/) [ons.org/licenses/by/4.0/.](http://creativecommons.org/licenses/by/4.0/)

# **References**

- <span id="page-17-8"></span>1. Abiteboul, S., Hull, R., Vianu, V.: Foundations of Databases. Addison-Wesley, Reading, MA (1995)
- <span id="page-17-21"></span>2. Angles, R., Arenas, M., Barceló, P., Hogan, A., Reutter, J.L., Vrgoc, D.: Foundations of modern query languages for graph databases. ACM Comput. Surv. **50**(5), 68:1-68:40 (2017)
- <span id="page-17-30"></span>3. Apache Solr: <https://solr.apache.org/> (2021). Accessed 24 November 2021
- <span id="page-17-22"></span>4. Apache TinkerPop: [http://tinkerpop.apache.org/docs/current/](http://tinkerpop.apache.org/docs/current/reference/) [reference/](http://tinkerpop.apache.org/docs/current/reference/) (2021), Access 24 November 2021
- <span id="page-17-13"></span>5. Arenas, M., Barceló, P., Libkin, L., Murlak, F.: Relational and XML Data Exchange. Morgan & Claypool Publishers, Synthesis Lectures on Data Management (2010)
- <span id="page-17-31"></span>6. Arenas, M., Grau, B.C., Kharlamov, E., Marciuska, S., Zheleznyakov, D.: Enabling Faceted Search over OWL 2 with Sem-Facet. In: OWLED 2014. CEUR, vol. 1265, pp. 121–132 (2014)
- <span id="page-17-2"></span>7. Arenas, M., Grau, B.C., Kharlamov, E., Marciuska, S., Zheleznyakov, D.: Faceted search over RDF-based knowledge graphs. J. Web Sem. **37–38**, 55–74 (2016)
- <span id="page-17-4"></span>8. Artale, A., Calvanese, D., Kontchakov, R., Zakharyaschev, M.: The dl-lite family and relations. J. Artif. Intell. Res. **36**, 1–69 (2009)
- <span id="page-17-0"></span>9. Baader, F., Calvanese, D., McGuinness, D., Nardi, D., Petel-Schneider, P. (eds.): The Description Logic Handbook: Theory, Implementation and Applications. Cambridge University Press, Cambridge (2003)
- <span id="page-17-23"></span>10. Beneventano, D., Bergamaschi, S., Guerra, F., Vincini, M.: The SEWASIE network of mediator agents for semantic search. J. UCS **13**(12), 1936–1969 (2007)
- <span id="page-17-16"></span>11. Berardi, D., Calvanese, D., De Giacomo, G.: Reasoning on UML class diagrams. Artif. Intell. **168**, 70–118 (2005)
- <span id="page-17-26"></span>12. Bhagdev, R., Chapman, S., Ciravegna, F., Lanfranchi, V., Petrelli, D.: Hybrid search: Effectively combining keywords and semantic searches. In: ESWC. pp. 554–568 (2008)
- <span id="page-17-14"></span>13. Calì, A., Gottlob, G., Lukasiewicz, T., Pieris, A.: Datalog+/-: A family of languages for ontology querying. In: Datalog. LNCS, vol. 6702, pp. 351–368. Springer (2011)
- <span id="page-17-15"></span>14. Calì, A., Gottlob, G., Pieris, A.: Advanced processing for ontological queries. PVLDB **3**(1), 554–565 (2010)
- <span id="page-17-32"></span>15. Calvanese, D., Cogrel, B., Komla-Ebri, S., Kontchakov, R., Lanti, D., Rezk, M., Rodriguez-Muro, M., Xiao, G.: Ontop: Answering SPARQL queries over relational databases. Semantic Web **8**(3), 471–487 (2017)
- <span id="page-17-18"></span>16. Calvanese, D., De Giacomo, G., Lembo, D., Lenzerini, M., Poggi, A., Rosati, R.: Ontology-based database access. In: SEBD 2007. pp. 324–331 (2007)
- <span id="page-17-7"></span>17. Calvanese, D., Giacomo, G., Lembo, D., Lenzerini, M., Rosati, R.: Tractable reasoning and efficient query answering in description logics: the dl-lite family. J. Autom. Reason. **39**(3), 385–429 (2007)
- <span id="page-17-1"></span>18. Calvanese, D., Giacomo, G.D., Lembo, D., Lenzerini, M., Rosati, R.: EQL-Lite: Effective first-order query processing in description logics. In: IJCAI. pp. 274–279 (2007)
- <span id="page-17-19"></span>19. Calvanese, D., Horrocks, I., Jiménez-Ruiz, E., Kharlamov, E., Meier, M., Rodriguez-Muro, M., Zheleznyakov, D.: On rewriting, answering queries in OBDA systems for big data. In: OWLED. CEUR, vol. 1080 (2013)
- <span id="page-17-17"></span>20. Calvanese, D., Lenzerini, M., Nardi, D.: Unifying class-based representation formalisms. J. Artif. Intell. Res. **11**, 199–240 (1999)
- <span id="page-17-11"></span>21. ten Cate, B., Kolaitis, P.G.: Structural characterizations of schemamapping languages. Commun. ACM **53**(1), 101–110 (2010)
- <span id="page-17-5"></span>22. Chen, P.P.: The entity-relationship model - toward a unified view of data. ACM Trans. Database Syst. **1**(1), 9–36 (1976)
- <span id="page-17-29"></span>23. Creating a faceted enterprise search application: [https://www.](https://www.ibm.com/docs/en/search/faceted?scope=SS5RWK_3.0.0) [ibm.com/docs/en/search/faceted?scope=SS5RWK\\_3.0.0](https://www.ibm.com/docs/en/search/faceted?scope=SS5RWK_3.0.0) (2021). Access 24 November 2021
- <span id="page-17-3"></span>24. DAFO: Data Access based on Faceted queries over Ontology: <https://github.com/tpankowski/dafo> (2019). Access 24 November 2021
- <span id="page-17-9"></span>25. Dumais, S.T.: Faceted search. In: Encyclopedia of Database Systems, pp. 1103–1109. Springer (2009)
- <span id="page-17-34"></span>26. ECMA-404: The JSON data interchange syntax: [https://www.](https://www.ecma-international.org) [ecma-international.org](https://www.ecma-international.org) (2017). Access 24 November 2021
- <span id="page-17-25"></span>27. Elbedweihy, K., Mazumdar, S., Wrigley, S.N., Ciravegna, F.: Nlgraphs: A hybrid approach toward interactively querying semantic data. In: The Semantic Web: Trends and Challenges - ESWC 2014. pp. 565–579 (2014)
- <span id="page-17-6"></span>28. Elmasri, R., Navathe, S.B.: Fundamentals of Database Systems, 6th edn. Addison-Wesley, Boston (2011)
- <span id="page-17-33"></span>29. Extensible Markup Language (XML) 1.0 (Fifth Edition): [http://](http://www.w3.org/TR/xml/) [www.w3.org/TR/xml/](http://www.w3.org/TR/xml/) (2008). Access 24 November 2021
- <span id="page-17-28"></span>30. Faceted Browsing Tutorial, using LOD Cloud Cache data space: <http://vos.openlinksw.com/owiki/wiki/VOS/> (2019). Access 24 November 2021
- <span id="page-17-24"></span>31. Fadhil, A., Haarslev, V.: OntoVQL: A graphical query language for OWL ontologies. In: DL. CEUR, vol. 250 (2007)
- <span id="page-17-20"></span>32. Fagin, R.: Inverting schema mappings. ACM Trans. Database Syst. **32**(4), 25:1-25:53 (2007)
- <span id="page-17-12"></span>33. Fagin, R., Kolaitis, P.G., Miller, R.J., Popa, L.: Data exchange: semantics and query answering. Theor. Comput. Sci. **336**(1), 89– 124 (2005)
- <span id="page-17-10"></span>34. Ferré, S.: Expressive and scalable query-based faceted search over SPARQL endpoints. In: ISWC. LNCS, vol. 8797, pp. 438–453. Springer (2014)
- <span id="page-17-27"></span>35. Ferré, S., Hermann, A.: Semantic search: Reconciling expressive querying and exploratory search. In: ISWC. LNCS, vol. 7031, pp. 177–192. Springer (2011)
- <span id="page-18-23"></span>36. Francis, N., Green, A., Guagliardo, P., Libkin, L., Lindaaker, T., Marsault, V., Plantikow, S., Rydberg, M., Selmer, P., Taylor, A.: Cypher: An evolving query language for property graphs. In: SIG-MOD. pp. 1433–1445. ACM (2018)
- <span id="page-18-2"></span>37. Gottlob, G., Orsi, G., Pieris, A.: Query rewriting and optimization for ontological databases. ACM Trans. Database Syst. 39(3), 25:1– 25:46 (2014)
- <span id="page-18-37"></span>38. Grau, B.C., Kharlamov, E., Zheleznyakov, D., Arenas, M., Marciuska, S.: On faceted search over knowledge bases. In: DL. CEUR, vol. 1193, pp. 153–156 (2014)
- <span id="page-18-24"></span>39. Green, A., Guagliardo, P., Libkin, L., Lindaaker, T., Marsault, V., Plantikow, S., Schuster, M., Selmer, P., Voigt, H.: Updating graph databases with cypher. VLDB **12**(12), 2242–2253 (2019)
- <span id="page-18-17"></span>40. Gruber, T.R.: Toward principles for the design of ontologies used for knowledge sharing? Int. J. Hum.-Comput. Stud. **43**(5–6), 907– 928 (1995)
- <span id="page-18-34"></span>41. Harth, A.: VisiNav: A system for visual search and navigation on web data. J. Web Sem. **8**(4), 348–354 (2010)
- <span id="page-18-32"></span>42. Heim, P., Ertl, T., Ziegler, J.: Facet Graphs: Complex Semantic Querying Made Easy. In: ESWC. LNCS, vol. 6088, pp. 288–302. Springer (2010)
- <span id="page-18-33"></span>43. Hildebrand, M., van Ossenbruggen, J., Hardman, L.: /facet: A browser for heterogeneous semantic web repositories. In: ISWC. LNCS, vol. 4273, pp. 272–285. Springer (2006)
- <span id="page-18-4"></span>44. Horrocks, I., Kutz, O., Sattler, U.: The even more irresistible SROIQ. In: Principles of Knowledge Representation and Reasoning. pp. 57–67. AAAI Press (2006)
- <span id="page-18-36"></span>45. How to build a facet filter in Azure Cognitive Search: [https://docs.](https://docs.microsoft.com/en-us/azure/search/search-filters-facets) [microsoft.com/en-us/azure/search/search-filters-facets](https://docs.microsoft.com/en-us/azure/search/search-filters-facets) (2020). Access 24 November 2021
- <span id="page-18-10"></span>46. Kazakov, Y.: An extension of complex role inclusion axioms in the description logic SROIQ. In: IJCAR. LNCS, vol. 6173, pp. 472–486. Springer (2010)
- <span id="page-18-38"></span>47. Kharlamov, E., Giacomelli, L., Sherkhonov, E., Grau, B.C., Kostylev, E.V., Horrocks, I.: SemFacet: Making hard faceted search easier. In: CIKM. pp. 2475–2478. ACM (2017)
- <span id="page-18-5"></span>48. Krötzsch, M., Simancik, F., Horrocks, I.: A description logic primer. CoRR abs/1201.4089 (2012), [http://arxiv.org/abs/1201.](http://arxiv.org/abs/1201.4089) [4089,](http://arxiv.org/abs/1201.4089) Access 24 November 2021
- <span id="page-18-15"></span>49. Libkin, L., Sirangelo, C.: Data exchange and schema mappings in open and closed worlds. J. Comput. Syst. Sci. **77**(3), 542–571 (2011)
- <span id="page-18-16"></span>50. Motik, B., Horrocks, I., Sattler, U.: Bridging the gap between OWL and relational databases. J. Web Semant. **7**(2), 74–89 (2009)
- <span id="page-18-18"></span>51. Nikolaou, C., Grau, B.C., Kostylev, E.V., Kaminski, M., Horrocks, I.: Satisfaction and implication of integrity constraints in ontologybased data access. In: IJCAI. pp. 1829–1835 (2019)
- <span id="page-18-35"></span>52. Oracle Commerce Guided Search: [https://docs.oracle.com/cd/](https://docs.oracle.com/cd/E67226_02/Common.112/pdf/GettingStarted.pdf) [E67226\\_02/Common.112/pdf/GettingStarted.pdf](https://docs.oracle.com/cd/E67226_02/Common.112/pdf/GettingStarted.pdf) (2015), Access 24 November 2021
- <span id="page-18-13"></span>53. Oren, E., Delbru, R., Decker, S.: Extending faceted navigation for RDF data. In: ISWC. LNCS, vol. 4273, pp. 559–572. Springer (2006)
- <span id="page-18-9"></span>54. OWL 2Web Ontology Language Profiles (Second Edition): (2012), www.w3.org/TR/owl2-profiles, Access 24 November 2021
- <span id="page-18-7"></span>55. Pankowski, T.: Exploring ontology-enhanced bibliography databases using faceted search. In: TPDL. LNCS, vol. 10450, pp. 27–39. Springer (2017)
- <span id="page-18-40"></span>56. Pankowski, T.: Rewriting and Executing Faceted Queries over Ontology-Enhanced Databases. In: KES. pp. 137–146. Procedia Computer Science, Elsevier (2017)
- <span id="page-18-8"></span>57. Pankowski, T., Bak, J.: DAFO: an ontological database system with faceted queries. In: ESWC Satellite Events. LNCS, vol. 11762, pp. 152–155. Springer (2019)
- <span id="page-18-41"></span>58. Pankowski, T., Brzykcy, G.: Data access based on faceted queries over ontologies. In: DEXA. LNCS, vol. 9828, pp. 275–286. Springer (2016)
- <span id="page-18-0"></span>59. Poggi, A., Lembo, D., Calvanese, D., De Giacomo, G., Lenzerini, M., Rosati, R.: Linking data to ontologies. In: Journal on Data Semantics X, pp. 133–173. Springer-Verlag (2008)
- <span id="page-18-39"></span>60. Resource Description Framework (RDF) Model and Syntax Specification: (1999), [www.w3.org/TR/PR-rdf-syntax/,](www.w3.org/TR/PR-rdf-syntax/) Access 24 November 2021
- <span id="page-18-6"></span>61. Rudolph, S.: Foundations of description logics. In: Reasoning Web. Semantic Technologies for the Web of Data. LNCS, vol. 6848, pp. 76–136. Springer (2011)
- <span id="page-18-27"></span>62. Schweiger, D., Trajanoski, Z., Pabinger, S.: SPARQLGraph: a webbased platform for graphically querying biological Semantic Web databases. BMC Bioinformat. **15**(279), 1–55 (2014)
- <span id="page-18-19"></span>63. Sequeda, J.F., Arenas, M., Miranker, D.P.: OBDA: query rewriting or materialization? In practice, both! In: ISWC. LNCS, vol. 8796, pp. 535–551. Springer (2014)
- <span id="page-18-14"></span>64. Sherkhonov, E., Grau, B.C., Kharlamov, E., Kostylev, E.V.: Semantic faceted search with aggregation and recursion. In: ISWC. LNCS, vol. 10587, pp. 594–610. Springer (2017)
- <span id="page-18-20"></span>65. Skjæveland, M.G., Giese, M., Hovland, D., Lian, E.H., Waaler, A.: Engineering ontology-based access to real-world data sources. J. Web Sem. **33**, 112–140 (2015)
- <span id="page-18-28"></span>66. Soylu, A., Giese, M., Jiménez-Ruiz, E., Kharlamov, E., Zheleznyakov, D., Horrocks, I.: Ontology-based end-user visual query formulation: Why, what, who, how, and which? Univ. Access Inf. Soc. **16**(2), 435–467 (2017)
- <span id="page-18-22"></span>67. SPARQL Query Language for RDF: [http://www.w3.org/TR/rdf](http://www.w3.org/TR/rdf-sparql-query/)[sparql-query/](http://www.w3.org/TR/rdf-sparql-query/) (2008). Access 24 November 2021
- <span id="page-18-25"></span>68. The Neo4j Cypher Manual v4.1: [https://neo4j.com/docs/pdf/](https://neo4j.com/docs/pdf/neo4j-cypher-manual-4.1.pdf) [neo4j-cypher-manual-4.1.pdf](https://neo4j.com/docs/pdf/neo4j-cypher-manual-4.1.pdf) (2020). Access 24 November 2021
- <span id="page-18-21"></span>69. Thorne, C., Calvanese, D.: Controlled aggregate tree shaped questions over ontologies. In: FQAS. LNCS, vol. 5822, pp. 394–405. Springer (2009)
- <span id="page-18-11"></span>70. Tunkelang, D.: Faceted Search. Morgan & Claypool Publishers (2009)
- 71. Tzitzikas, Y., Analyti, A.: Mining the meaningful term conjunctions from materialised faceted taxonomies: algorithms and complexity. Knowl. Inf. Syst. **9**(4), 430–467 (2006)
- <span id="page-18-30"></span>72. Tzitzikas, Y., Manolis, N., Papadakos, P.: Faceted exploration of RDF/S datasets: a survey. J. Intell. Inf. Syst. **48**(2), 329–364 (2017)
- <span id="page-18-29"></span>73. Vega-Gorgojo, G., Slaughter, L., Giese, M., Heggestøyl, S., Soylu, A., Waaler, A.: Visual query interfaces for semantic datasets: An evaluation study. J. Web Sem. **39**, 81–96 (2016)
- <span id="page-18-31"></span>74. Wagner, A., Ladwig, G., Tran, T.: Browsing-oriented semantic faceted search. In: DEXA. LNCS, vol. 6860, pp. 303–319. Springer (2011)
- <span id="page-18-1"></span>75. Xiao, G., Calvanese, D., Kontchakov, R., Lembo, D., Poggi, A., Rosati, R., Zakharyaschev, M.: Ontology-based data access: A survey. In: IJCAI. pp. 5511–5519 (2018)
- <span id="page-18-3"></span>76. Xiao, G., Lanti, D., Kontchakov, R., Komla-Ebri, S., Kalayci, E.G., Ding, L., Corman, J., Cogrel, B., Calvanese, D., Botoeva, E.: The Virtual Knowledge Graph System Ontop. In: ISWC. LNCS, vol. 12507, pp. 259–277. Springer (2020)
- <span id="page-18-12"></span>77. Zhang, G., Tao, S., Zeng, N., Cui, L.: Ontologies as nested facet systems for human-data interaction. Semantic Web **11**(1), 79–86 (2020)
- <span id="page-18-26"></span>78. Zviedris, M., Barzdins, G.: ViziQuer: A Tool to Explore and Query SPARQL Endpoints. In: ESWC. LNCS, vol. 6644, pp. 441–445. Springer (2011)

**Publisher's Note** Springer Nature remains neutral with regard to jurisdictional claims in published maps and institutional affiliations.