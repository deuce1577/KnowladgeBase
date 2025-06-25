# **Schema Transformations and Query Rewriting in Ontological Databases with a Faceted Interface**

Tadeusz Pankowski(B)

Institute of Control, Robotics and Information Engineering, Pozna´n University of Technology, Pozna´n, Poland tadeusz.pankowski@put.poznan.pl

**Abstract.** In this paper, we discuss some problems identified in designing and implementing a class of ontological database systems. The goal of these systems is to provide an extended knowledge system that combines flexibility of ontologies with efficiency of relational databases. The terminological part of the ontology forms the ontological (conceptual) schema of the database, and the extensional part is managed by a relational database server. Queries are formulated in an interactive way using a faceted search over the ontological schema. In such scenario, a number of transformations must be performed: (a) a mapping from an ontological schema into relational scheme that concerns both the structure and rules constituting the ontology; (b) transformation of faceted queries, defined in a graphical form, into first-order queries and to SQL queries. The considerations are based on verified solutions implemented in DAFO (*Data Access based in Faceted queries over Ontologies*) system.

## **1 Introduction**

In recent two decades, we witness the increasing importance of research on combining ontologies and databases. Ontology-based data access (OBDA) systems have been proposed as a way of integrating and querying information from heterogeneous sources [\[6,](#page-14-0)[21](#page-15-0)], in ontology-enhanced databases an ontology is added to enrich databases with intensional knowledge encoded by ontology rules [\[2](#page-14-1),[13,](#page-14-2)[19\]](#page-15-1), ontological databases are designed for ontologies, where query answering problems are more important than classical reasoning tasks [\[10\]](#page-14-3). In all these cases, the goal is to achieve a synergy in the result of combining flexibility of ontologies with efficiency of relational databases. The challenging issue is providing the system with a querying mechanism over ontologies, since the schema of the database has then a form of an ontology. A standard query language for ontologies is SPARQL [\[22\]](#page-15-2) but it is not well-suited for end users. Thus, we observe development of graphic-oriented query languages [\[26](#page-15-3)]. Among them, a significant place takes the faceted search [\[2](#page-14-1)[,24](#page-15-4)[,25](#page-15-5)], and in this paper we will exploit this paradigm as a query formulation mechanism in ontological database systems.

c Springer International Publishing AG, part of Springer Nature 2018

A. Rensink and J. S´anchez Cuadrado (Eds.): ICMT 2018, LNCS 10888, pp. 76–91, 2018.

*Contribution.* In this paper, we discuss and propose solutions to schema transformations and query rewriting problems identified in designing and implementing in ontological database systems with faceted search. Main novelties discussed in this paper, are:

- 1. Defining a mapping between a terminological part of an ontology (treated as a conceptual schema) and a schema of relational database. This mapping, called meta-schema mapping, since is defined on the meta-schema level and concerns schemas, specifies a mapping for both structural elements of the ontology as well as ontology rules. This is crucial in ontological databases since the mapping is used in the process of translating ontology-oriented queries into SQL queries executed by relational database engine.
- 2. Designing a transformation and rewriting procedures from ontology-oriented faceted queries into first-order queries and finally to SQL queries. A faceted query is created using a hierarchical graphical interface and is visualized as a tree. We propose a method for transforming such a tree in a first-order query, which is a tree-shaped monadic positive existential query [\[1,](#page-14-4)[2\]](#page-14-1), that is next translated into a SQL query.

*Related work.* Similarities and differences between ontologies and relational databases were investigated in both research papers [\[13](#page-14-2)[,18](#page-15-6)] and text books, notably [\[1](#page-14-4)]. A synergic combination of databases and ontologies can be observed in data integration systems [\[6,](#page-14-0)[21](#page-15-0)], ontology-enhanced databases [\[2,](#page-14-1)[13](#page-14-2)[,19](#page-15-1)] and ontological databases [\[4,](#page-14-5)[5](#page-14-6)[,10\]](#page-14-3). Essential and inspiring for this paper was the concept of the extended knowledge bases proposed in [\[13](#page-14-2)]. The theory of schema mapping was initiated and developed in [\[8,](#page-14-7)[9\]](#page-14-8). Faceted search is a prominent search and data exploration paradigm in ontology-based and e-commerce applications and a number of RDF-based faceted search systems have been developed in recent years [\[2](#page-14-1),[12,](#page-14-9)[20](#page-15-7)[,25](#page-15-5),[27\]](#page-15-8). The theoretical foundations of faceted search were studied [\[2](#page-14-1)]. In our previous papers [\[15](#page-14-10)[,16](#page-14-11)] we presented the preliminary results of our investigations and implementation of the *Data Access based on Faceted queries over Ontologies* (DAFO) system and reported experimental results, which prove high efficiency of proposed solutions.

### **2 Preliminaries: Ontological Databases**

An application domain can be specified as an ontology, since an ontology is *"an explicit specification of a conceptualization of an application domain"* [\[11](#page-14-12)]. On the other hand, from a more technical point of view, an application domain is represented using the relational database technology, where the conceptual model of the application domain is informally presented in a form of ER or UML diagram (see our running example in Fig. [1\)](#page-2-0). The idea of *ontological databases* is to combine these two approaches. Ontologies provide a rich mechanism for intensional knowledge specification (the conceptual model), and relational database technology provides methods for efficient implementation of the extensional knowledge (a relational database) and a query answering environment.

![](_page_2_Figure_1.jpeg)

<span id="page-2-0"></span>**Fig. 1.** ER diagram as a conceptual model of a bibliographic application domain.

#### **2.1 Ontologies and Ontological Schemas**

An ontology is defined as a triple O = (Σ, R, A), where: (a) Σ is a *signature* consisting of a set of unary predicates (UP) (classes) and binary predicates (BP) (properties), (b) R is a set of *rules* (intensional knowledge), and (c) A is a set of *facts* or assertions. All expressions in R and in A are built from symbols occurring in Σ and constants in a Const set. We assume that there is a subset LabNull of *labeled nulls* in Const. For labeled nulls, the *unique name assumption* (UNA) is not made, i.e., different symbols can denote the same individual. For "regular" constants UNA is satisfied [\[1,](#page-14-4)[8](#page-14-7)[,13](#page-14-2)]. A pair T = (Σ, R) is referred to as a *terminological part* of the ontology, and A as an extensional part of it [\[3\]](#page-14-13).

In ontological databases, the intensional part plays a role of an *ontological (conceptual) schema* presented to users, while the extensional part is represented in a relational database R = (*Sch*, I), where *Sch* is a relational schema and I is an instance of the schema [\[10](#page-14-3)]. In this paper, we impose some restrictions on the terminological part to achieve ability to transform ontological schema into the relational one.

<span id="page-2-1"></span>**Definition 1 (Ontological schema).** *A pair* T = (Σ, R) *is an ontological schema (a schema of an ontological database), if:*

- *1.* Σ *is partitioned into two disjoint sets of intensional and extensional predicates, i.e.,* Σ = UP ∪ BP*, where:* UP = UP*<sup>I</sup>* ∪ UP*E, and* BP = BP*<sup>I</sup>* ∪ BP*E, and the distinguished extensional unary predicate* String *is in* UP*E.*
- *2. The set of rules is partitioned into two disjoint sets,* R = R*<sup>c</sup>* ∪ R*w, Table [1,](#page-3-0) where:*
	- *–* R*<sup>c</sup> a set of integrity constraint rules of the form* (IC-1) (IC-8)*.*
	- *–* R*<sup>w</sup> a set of rewriting rules of the form* (RW-1) (RW-5)*. All predicates on the right-hand sides of rewriting rules are intensional.*

Definition [1](#page-2-1) determines a class of ontologies which can be transformed into relational databases without losing knowledge, and which can be queried using a faceted search paradigm.

<span id="page-2-2"></span>*Example 1.* The conceptual model represented by an ER diagram in Fig. [1,](#page-2-0) can be specified by an ontological schema BibOn = (Σ, R*<sup>c</sup>* ∪ R*w*), where:

| Id   | General form of a rule                  | Name                                                                     |
|------|-----------------------------------------|--------------------------------------------------------------------------|
| IC-1 | P(x, y) → C(x)                          | Domain rule                                                              |
| IC-2 | P(x, y) → C(y)                          | Range rule                                                               |
| IC-3 | C(x) → C1(x)                            | Subtype rule (follows from RW-3 or RW-4)                                 |
| IC-4 | P(x, y) → P1(x, y)                      | Subproperty rule (follows from RW-5)                                     |
| IC-5 | C(x) → ∃y P(x, y)                       | Totality on domain                                                       |
| IC-6 | C(y) → ∃x P(x, y)                       | Totality on range                                                        |
| IC-7 | P(x, y1) ∧ P(x, y2) → y1<br>= y2        | Functionality rule (a function on domain)                                |
| IC-8 | P(x1, y) ∧ P(x2, y) → x1<br>= x2        | Key rule (a function on range)                                           |
| RW-1 | ∃z P1(x, z) ∧ C(z) ∧ P2(z, y) → P(x, y) | Chain (composition)                                                      |
| RW-2 | P1(y, x) → P(x, y)                      | Inversion                                                                |
| RW-3 | C1(x) ∧ ∃yP(x, y) ∧ y = a → C(x)        | Type specialization determined by a value                                |
| RW-4 | C1(x) ∧ ∃yP(x, y) ∧ C2(y) → C(x)        | Type specialization determined by a<br>subtype                           |
| RW-5 | C1(x) ∧ P1(x, y) ∧ C2(y) → P(x, y)      | Property specialization determined by a<br>domain and/or a range subtype |

<span id="page-3-0"></span>**Table 1.** Categories of rules in DAFO ontologies.

1. Extensional predicates:

UP*<sup>E</sup>* = {String, P erson, P aper, P roceedings, Conference} BP*<sup>E</sup>* = {name, *affiliation*, authorOf, inP roc, ofConf, country, acronym . . . }.

- 2. Intensional predicates (not all are depicted in Fig. [1\)](#page-2-0): UP*<sup>I</sup>* = {Author, ACMConf, ACMP aper, . . . }, BP*<sup>I</sup>* = {writtenBy, presentedAt, authorConf, . . . }.
- 3. Integrity constraint rules (only concerning name):

$$\begin{array}{l} \mathcal{R}\_c = \{ \textit{name}(x, y) \rightarrow \textit{Person}(x), \textit{Person}(x) \rightarrow \exists y \ \textit{name}(x, y),\\ \textit{name}(x, y\_1) \land \textit{name}(x, y\_2) \rightarrow y\_1 = y\_2,\\ \textit{name}(x\_1, y) \land \textit{name}(x\_2, y) \rightarrow x\_1 = x\_2, \dots \} \end{array}$$

4. Rewriting rules specifying intensional predicates:

$$\begin{array}{l} \mathcal{R}\_{w} = \{Papor(x) \land \exists y \; authorOf(x,y) \land Papor(y) \to Autor(x),\\ \quad Confidence(x) \land \exists y \; arrowsm(x,y) \land y = 'ACM \to ACMConf(x),\\ \quad Papor(x) \land \exists y \; presentedAt(x,y) \land ACMConf(y) \to ACMPager(x)\\ \quad authorOf(y,x) \to writtenBy(x,y),\\ \quad \exists z \; in Proc(x,z) \land Procedings(z) \land of Conf(z,y) \to presentedAt(x,y),\\ \quad \exists z \; authorOf(x,z) \land Papor(z) \land presentedAt(z,y) \to authorConf(x,y)\} \end{array}$$

#### **2.2 Relational Schema**

A *relational schema* specifies relation names, their types (set of attributes), primary keys for some relations, and such integrity constraints as: primary key constraints, unique constraints, not-null constraints, referential constraints (foreign keys), and inclusion constraints.

**Definition 2 (Relational schema).** *A relational schema is a tuple Sch* = (**R**,Att, att, pkey, Constr), *where:*

- *–* **R** = {R1,...,R*n*} *is a finite set of relation (or table) names;*
- *–* Att *is a finite set of attributes;*
- *–* att *assigns a finite set* att(R) ⊆ Att *of attributes to each* R ∈ **R***;*
- *–* pkey *is a function assigning primary keys to some relation names,* pkey(R) = Id ∈ att(R) *(i.e., we assume that all primary keys have the same name,* Id*);*
- *–* Constr *is a set of constraints of the form:* R[Id] PRIMARY KEY *(primary key constraint);* R[A] UNIQUE *(unique constraint);* R[A] NOT NULL *(not-null constraint);* R[A] → R [Id] *(referential constraint);* R [Id] ⊆ R[A] *(inclusion constraint), where* A ∈ att(R)*,* Id = pkey(R )*,* R, R ∈ **R***.*

<span id="page-4-1"></span>*Example 2.* A sample relational schema of a bibliographic database is depicted in Fig. [2.](#page-4-0) There are six relation names, and, for example:

*att*(*P erson*) = {*Id, N ame*}*, att*(*Aff iliation*) = {*P ersonId, Aff iliation*}*, pkey*(*P erson*) = *Id,* Constr <sup>=</sup> {*P erson*[*Id*] PRIMARY KEY*, P erson*[*N ame*] UNIQUE*, P erson*[*N ame*] NOT NULL*, AuthorP aper*[*AuthorId*] NOT NULL*, AuthorP aper*[*AuthorId*] → *P erson*[*Id*]*, P aper*[*Id*] ⊆ *AuthorP aper*[*P aperId*]*,...* }*.*

![](_page_4_Figure_9.jpeg)

<span id="page-4-0"></span>**Fig. 2.** Diagram of a bibliographic relational schema BibSch. Arrows denote referential constraints, and ⊆ – inclusion constraints.

## **3 Transforming of an Ontology to Ontological Database**

Given an ontology O = (Σ, R*<sup>c</sup>* ∪ R*w*, A), we have to transform it to a relational database *RDB* = (*Sch*, I). So, two mappings must be defined:

- on a meta-schema level: a mapping of ontological to relational schema,
- on a schema level: a mapping of a set of facts, extended with materialized extensional rules, to relational database instance.

In result, and ontological database ODB = (Σ, R*<sup>c</sup>* ∪ R*w*, *RDB*) is obtained.

#### <span id="page-5-1"></span>**3.1 Mapping of Ontological Schema to Relational Schema**

Now, we show how elements of an ontological schema are mapped to elements of a relational schema. Let T = (Σ, R*<sup>c</sup>* ∪ R*w*) be an ontological schema. The set BP*<sup>E</sup>* ⊆ Σ of extensional binary predicates is divided into four pairwise disjoint sets:

FDP − a set of functional data properties, MDP − a set of multivalued data properties, FOP − a set of functional object properties, MOP − a set of multivalued object properties.

*Data properties* are binary predicates which have String as their range, otherwise they are *object properties*. A *functional property* is a binary predicate for which the functionality rule is defined, otherwise it is a *multivalued* property. Further on, by dom(P) and rng(P) we denote, respectively, the domain and the range of P, by domT ot(P) and rngT ot(P) we denote that P is total on its domain and range, respectively, and by key(P), that the key rule is defined for P.

**Definition 3.** *Given an ontological schema* T = (Σ, R*<sup>c</sup>* ∪ R*w*) *and a relational schema Sch* = (**R**,Att, att, pkey, Constr), *a (meta-schema) mapping from* T *to Sch is defined by means of the following meta-schema mapping function*

<span id="page-5-0"></span>
$$M = (rel, domAtt, rngAtt, constr),$$

*where*

$$\begin{array}{l}rel \\ lomAtt: \mathsf{BP}\_{E} \to \mathsf{Att} \\ rngAtt: \mathsf{BP}\_{E} \to \mathsf{Att} \\ const \quad \mathsf{Top}\_{E} \to \mathsf{Att} \\ const \quad \mathsf{U}\mathsf{P}\_{E} \cup \mathsf{BP}\_{E} \to 2^{\mathsf{Constr}}.\end{array}$$

We assume the following denotations:

- rel(C), and rel(P) will be denoted by R*<sup>C</sup>* and R*<sup>P</sup>* , respectively;
- *domAtt*(P) will be denoted by A*<sup>d</sup> P* ;
- *rngAtt*(P) will be denoted by A*<sup>r</sup> P* .

The above meta-schema mapping function M is defined as follows:

1. For every C, C ∈ UP*E*, we assume R*<sup>C</sup>* = R*<sup>C</sup>* for C = C , and

R*<sup>C</sup>* [Id] PRIMARY KEY ∈ constr(C).

To any extensional unary predicate C, a relation name R*<sup>C</sup>* of type att(R*<sup>C</sup>* ) is assigned. There is an attribute Id ∈ att(R*<sup>C</sup>* ), and Id is the primary key of R*<sup>C</sup>* . Different relation names are assigned to different unary predicates. For example: R*P erson* = P erson, Id ∈ att(P erson), pkey(P erson) = Id, P erson[Id] PRIMARY KEY ∈ Constr.

2. For every P ∈ FDP, if dom(P) = C, then

$$R\_P = R\_C, \ A\_P^d = Id, \ A\_P^r \in att(R\_C), \ A\_P^r \neq Id.$$

If P is a functional data property with domain C, then: an attribute of R*<sup>C</sup>* is assigned to P as its range attribute A*<sup>r</sup> <sup>P</sup>* , and the primary key of R*<sup>C</sup>* is assigned to P as its domain attribute A*<sup>d</sup> <sup>P</sup>* . The mapping does not effect the set of constraints in the relational schema. For example, if name ∈ FDP, dom(name) = P erson, then R*name* = P erson, A*<sup>d</sup> name* = Id, A*<sup>r</sup> name* = N ame ∈ att(P erson).

3. For every P ∈ MDP, if dom(P) = C, then:

$$R\_P \neq R\_C, \; att(R\_P) = \{A\_P^d, A\_P^r\}, \; R\_P[A\_P^d] \to R\_C[Id] \in constr(P).$$

If P is a multivalued data property with domain C, then: a relation name R*<sup>P</sup>* assigned to P is different from R*<sup>C</sup>* ; relation R*<sup>P</sup>* has two attributes, att(R*<sup>P</sup>* ) = {A*<sup>d</sup> <sup>P</sup>* , A*<sup>r</sup> <sup>P</sup>* }, assigned as the domain and the range attribute of P. The domain attribute is the foreign key in R*<sup>P</sup>* referring to the primary key of R*<sup>C</sup>* , i.e., the referential constraint R*<sup>P</sup>* [A*<sup>d</sup> <sup>P</sup>* ] → R*<sup>C</sup>* [Id] is assigned to P. For example, if *affiliation* is a multivalued data property with domain P erson, then Raffiliation = *Affiliation*, att(*Affiliation*) = {P ersonId, *Affiliation*}, A*<sup>d</sup>* affiliation = P ersonId, A*<sup>r</sup>* affiliation = *Affiliation*, and *Affiliation*[P ersonId] → P erson[Id] ∈ Constr.

4. For every P ∈ FOP, if dom(P) = C, and rng(P) = D, then

R*<sup>P</sup>* = R*<sup>C</sup>* , A*<sup>d</sup> <sup>P</sup>* = Id, A*<sup>r</sup> <sup>P</sup>* <sup>∈</sup> att(R*<sup>C</sup>* ), R*<sup>C</sup>* [A*<sup>r</sup> <sup>P</sup>* ] → R*D*[Id] ∈ constr(P).

If P is a functional object property with domain C and range D, then the relation assigned to P is that assigned to C. The domain attribute of P is the primary key of R*<sup>C</sup>* , and the range attribute of P is an attribute A*<sup>r</sup> <sup>P</sup>* ∈ att(R*<sup>C</sup>* ). A*<sup>r</sup> <sup>P</sup>* is also a foreign key referring to the primary key of R*D*. For example, if ofConf is a functional object property with domain P roceedings and range Conference, then: R*ofConf* = P roceedings, A*<sup>d</sup> ofConf* = Id = pkey(P roceedings), A*<sup>r</sup> ofConf* = ConferenceId ∈ att(P roceedings), and the referential constraint P roceedings[ConferenceId] → Conference[Id] is in Constr.

5. For every P ∈ MOP, if dom(P) = C, and rng(P) = D, then:

$$\begin{aligned} R\_P \neq R\_C, \ R\_P \neq R\_D, \ att(R\_P) = \{A\_P^d, A\_P^r\},\\ \{R\_P[A\_P^d] \to R\_C[Id], \ R\_P[A\_P^r] \to R\_D[Id]\} \subseteq constr(P). \end{aligned}$$

If P is a multivalued object property with domain C and range D, then the relation assigned to P is a binary relation R*<sup>P</sup>* , different from R*<sup>C</sup>* and R*D*. att(R*<sup>P</sup>* ) = {A*<sup>d</sup> <sup>P</sup>* , A*<sup>r</sup> <sup>P</sup>* }, where <sup>A</sup>*<sup>d</sup> <sup>P</sup>* is a foreigns key referring to the primary key of R*<sup>C</sup>* , and A*<sup>r</sup> <sup>P</sup>* is a foreign key referring to the primary key of R*D*. For example, if authorOf is a multivalued object property with domain P erson and range P aper, then: R*authorOf* = *AuthorPaper* , att(*AuthorPaper* ) = {AuthorId, P aperId}, <sup>A</sup>*<sup>d</sup> authorOf* = AuthorId, A*<sup>r</sup> authorOf* = P aperId and the referential constraints *AuthorPaper* [AuthorId] → P erson[Id] and *AuthorPaper* [P aperId] → P aper[Id] are in Constr.

- 6. *Mapping of another integrity constraint rules.* Note that the following integrity constraints rules: domain, range, and functionality, were considered while defining the above mappings. The subtype and subproperty rules are not mapped explicitly, since they are in fact consequences of some rewriting rules. Now, we define mappings for totality and key rules.
	- (a) If dom(P) = C, and domT ot(P) is a totality rule on domain of P, then

$$\{R\_P[A\_P^r] \text{ MOT NUL, } \ R\_C[Id] \subseteq R\_P[A\_P^d] \} \subseteq constr(P).$$

In this case, the rule is mapped into two constraints in relational schema: (1) the not-null constraint on the range attribute of P, and (2) the inclusion dependency defining inclusion of the primary key of R*<sup>C</sup>* in the domain attribute of P (trivially satisfied for functional properties).

- P erson(x) → ∃y name(x, y) saying that name is total on P erson is mapped to P erson[N ame] NOT NULL, and to P erson[Id] ⊆ P erson[Id] (that is always true).
- The rule P erson(x) → ∃y *affiliation*(x, y) says that at least one affiliation is given for any person. Then *affiliation* is total on P erson, and the rule is mapped into the set of two constraints:
	- (1) the not-null constraint: *Affiliation*[*Affiliation*] NOT NULL, and
- (2) the inclusion dependency: P erson[Id] ⊆ *Affiliation*[P ersonId].
- (b) If rng(P) = D, and rngT ot(P) is a totality rule on range of P, then

{R*<sup>P</sup>* [A*<sup>d</sup> <sup>P</sup>* ] NOT NULL, R*D*[Id] <sup>⊆</sup> <sup>R</sup>*<sup>P</sup>* [A*<sup>r</sup> <sup>P</sup>* ]} ⊆ constr(P).

In this case, the rule is mapped into two constraints in relational schema: (1) the not-null constraint on the domain attribute of P (trivially satisfied for functional properties, since the domain attribute is then the primary key), and (2) the inclusion dependency defining inclusion of the primary key of R*<sup>D</sup>* in the range attribute of P.

- Conference(y) → ∃x ofConf(x, y) specifies that any conference has proceedings, i.e., ofConf is total on its range Conference. The rule is mapped to P roceedings[Id] NOT NULL (always true), and to inclusion dependency Conference[Id] ⊆ P roceedings[ConferenceId].
- The rule P aper(y) → ∃x authorOf(x, y) says that at least one author must be given for any paper. Then *authorOf* is total on P aper, and the rule is mapped into the set of two constraints:
	- (1) the not-null constraint: *AuthorPaper* [*AuthorId*] NOT NULL, and
- (2) the inclusion dependency: P aper[Id] ⊆ *AuthorPaper* [P aperId]. 7. If key(P) is a key rule, then

R*<sup>P</sup>* [A*<sup>r</sup> <sup>P</sup>* ] UNIQUE ∈ constr(P),

i.e., the UNIQUE constraint is enforced for the range attribute assigned to P. For example, if name(x1, y) ∧ name(x2, y) → x<sup>1</sup> = x<sup>2</sup> says that a person is uniquely determined by its name, then this rule is mapped to the constraint P erson[N ame] UNIQUE in relational schema.

Meta-schema mappings for some predicates of the ontological schema in Example [1](#page-2-2) into relational schema in Example [2,](#page-4-1) is given in Table [2.](#page-8-0)

| Predicate (P )   | rel (RP )   | domAtt (Ad<br>P ) | rngAtt (Ar<br>P ) | constr(P )                                                                                                                                                                                   |
|------------------|-------------|-------------------|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Person(UP)       | Person      |                   |                   | {Person[Id] PRIMARY KEY}                                                                                                                                                                     |
| name(FDP)        | Person      | Id                | Name              | {Person[Name] NOT NULL,<br>Person[Name] UNIQUE}                                                                                                                                              |
| affiliation(MDP) | Affiliation | PersonId          | Affiliation       | {Affiliation[PersonId] →<br>Person[Id]}                                                                                                                                                      |
| inProc (FOP)     | Paper       | Id                | ProcId            | {Paper[ProcId] →<br>Proceedings[Id], Proceedings[Id]<br>⊆ Paper[ProcId]}                                                                                                                     |
| authorOf (MOP)   | AuthorPaper | AuthorId          | PaperId           | {AuthorPaper[AuthorId] →<br>Person[Id],<br>AuthorPaper[PaperId] →<br>Paper[Id], Paper[Id] ⊆<br>AuthorPaper[PaperId],<br>AuthorPaper[AuthorId] NOT<br>NULL, AuthorPaper[PaperId]<br>NOT NULL} |

<span id="page-8-0"></span>**Table 2.** Meta-schema mapping of an ontological schema into relational schema.

### **3.2 Mapping of an Ontology to Relational Database Instance**

Now, we show haw extensional part of an ontology O = (Σ, R*<sup>c</sup>* ∪ R*w*, A) is mapped to an relational database instance. This extensional part consists of the set A and all consequences of it with respect to the set R*<sup>c</sup>* of integrity constraint rules, denoted A∪R*c*. This extensional part, called the canonical model, is obtained by using the *chase procedure* [\[7](#page-14-14)[,8](#page-14-7)], denoted A∪R*<sup>c</sup>* = chaseR*<sup>c</sup>* (A).

<span id="page-8-2"></span>*Example 3.* For the following sets from an ontology O:

– a set of facts (N*<sup>i</sup>* denotes a labeled null in LabNull) A = {P erson(N1), P erson(p2), name(N1, john), name(N2, ann), name(p1, john)}, N1, N<sup>2</sup> ∈ LabNull, and – a set of integrity constraint rules R*<sup>c</sup>* = {name(x, y) → P erson(x), P erson(x) → ∃y name(x, y), name(x1, y) ∧ name(x2, y) → x<sup>1</sup> = x2};

it is easy to show that the canonical model is

A∪R*<sup>c</sup>* = {P erson(p1), P erson(p2), P erson(N2), name(p1, john), name(p2, N3), name(N2, ann)}.

<span id="page-8-1"></span>**Definition 4.** *Let* O = (T , A) *be an ontology, where* T = (Σ, R*<sup>c</sup>* ∪ R*w*)*, and* M : T → *Sch be a mapping from the ontological schema* T *to a relational schema Sch . A mapping,* m*, of the extensional component of* O *to an instance of Sch , is defined by the following set of rules executed on the canonical model* A∪R*c:*

*–* C(x) → ∃r R*<sup>C</sup>* (r) ∧ r.Id = x*, for each* C ∈ UP*E; –* <sup>P</sup>(x, y) → ∃r R*<sup>P</sup>* (r) <sup>∧</sup> r.A*<sup>d</sup> <sup>P</sup>* <sup>=</sup> <sup>x</sup> <sup>∧</sup> r.A*<sup>r</sup> <sup>P</sup>* = y*, for each* P ∈ BP*E; –* <sup>R</sup>*<sup>P</sup>* (r) <sup>∧</sup> isLN(r.A*<sup>r</sup> <sup>P</sup>* ) <sup>→</sup> r.A*<sup>r</sup> <sup>P</sup>* = NULL*, for each data property* P*.* *where* R*<sup>C</sup>* , R*<sup>P</sup>* , A*<sup>d</sup> <sup>P</sup>* , A*<sup>r</sup> <sup>P</sup> are defined in the definition of* M *(Definition [3\)](#page-5-0). The function* isLN(x) *returns* TRUE *if* x ∈ LabNull*, and* FALSE *otherwise.*

Note, that the right-hand sides of rules in m (Definition [4\)](#page-8-1) are specified in *relational tuple calculus* (RTC) [\[1](#page-14-4)]. Labeled nulls, which are values of data properties, are replaced by NULL, while the other are left in the relational database instance and are interpreted as "regular" constants (satisfying UNA).

*Example 4.* For the canonical model in Example [3,](#page-8-2) and the mapping discussed in Sect. [3.1,](#page-5-1) we obtain m(A∪R*c*) = I, where

*P erson<sup>I</sup>* <sup>=</sup> {[*Id* : *<sup>p</sup>*1*, name* : *john*]*,* [*Id* : *<sup>p</sup>*2*, name* : NULL]*,* [*Id* : <sup>N</sup>2*, name* : *ann*]}*.*

### **4 Faceted Queries and Their First-Order Form**

#### **4.1 Formulating Faceted Queries Using a Faceted Interface**

The main assumption in the faceted search paradigm is that a user is provided with a faceted interface that is a hierarchical view over the underlying information source, in our case – over an ontology with the ontological schema T = (Σ, R*<sup>c</sup>* ∪ R*w*). An ontology is, in general, a complex network not a hierarchy. To deal with this issue, in DAFO system a user starts with writing a keyword query that is a sequence of unary predicates from the ontology. In this way she/he specifies an interesting subset of the ontology and its hierarchical arrangement (consistent with the ordering of the keywords in the sequence).

![](_page_9_Figure_8.jpeg)

<span id="page-9-0"></span>**Fig. 3.** A faceted interface (a), faceted query tree (b), and first-order faceted query before rewriting (c), and after rewriting (d). (Color figure online)

For example, (P aper, ACMConf, DEXAConf, P erson) is the keyword query in Fig. [3](#page-9-0) that initiates a process of the faceted search. It means that the user intends to create a query returning a set of objects of type P aper (the first component of the sequence), and the query will involve conditions on remaining components of the sequence. The system prepares an appropriate hierarchical view of the ontology and presents it in a form of a *faceted interface*, Fig. [3\(](#page-9-0)a). Note that all unary predicates given in the keyword query, as well as binary predicates connecting them, are selected (checked). In DAFO, a faceted interface is implemented by a *tree view* object that is an instance of *TreeView class* [\[23](#page-15-9)].

Now, a user can interactively and iteratively operate on the faceted interface creating the expected final *faceted query*. In the running example, the faceted query is presented as the tree in Fig. [3\(](#page-9-0)b) (count = indicates the expected number of elements in the answer). Note that:

- the blue color of a node indicates that the set of its children is a *disjunctive set* (components are connected by OR),
- the red color of a node indicates that the set of its children forms a *conjunctive set* (components are connected by AND),
- a disjunctive set of constants (or constant set names) is written as a list and prefixed by ANY, e.g., *ANY ('INRIA France', 'PUT Poland')* in Fig. [3\(](#page-9-0)c),
- a conjunctive set of constants (or constant set names) is written as a list and prefixed by ALL, e.g., *ALL ('%database%', '%query%')* in Fig. [4\(](#page-11-0)b), where *%database%* identifies a set of strings containing *'database'* as a substring.

While operating on the faceted interface, a user can perform the following operations: (a) selecting/unselecting nodes, (b) expanding/collapsing subtrees, (c) switching: disjunctive/conjunctive, (d) cloning (duplicating) subtrees, (e) inserting values, (f) removing unselected nodes.

In result of operating over a faceted interface, a final faceted query is obtained. In Fig. [3\(](#page-9-0)b) the faceted query is presented as a tree. The meaning of the query is: *"Get papers presented at an ACM or a DEXA conference and written by a person affiliated to 'INRIA France' or 'PUT Poland'"*.

The faceted query in Fig. [4\(](#page-11-0)a) means: "Get ACM papers, which concerns both databases and queries" (for simplicity, we assume that the subjects of a paper are included in its title).

#### **4.2 Transformation of Faceted Queries into First-Order Form**

To obtain an executable form of a faceted query, we have to make the following transformations:

- 1. Transformation of the tree form of faceted query into first-order form, called *first-order faceted query* (FOFQ).
- 2. Rewriting FOFQ into an extensional form, i.e., into a form in which no intensional predicate occurs.

![](_page_11_Figure_1.jpeg)

<span id="page-11-0"></span>**Fig. 4.** Faceted query tree (a), first-order faceted query before rewriting (b), after rewriting (c), and its translation to SQL (d).

3. Translation of extensional FOFQ into SQL query.

We start with a formal definition of a faceted query tree.

**Definition 5 (Faceted query tree).** *A faceted query tree is an tree expression* fqt *conforming to the syntax:*

<span id="page-11-1"></span>
$$\begin{array}{l} fqt ::= (root, qt) \\ qt ::= \circ \{ ct\_1, \dots, ct\_k \} \\ ct ::= (C, \varepsilon) \mid (C, \circ \{ pt\_1, \dots, pt\_m \}) \\ pt ::= (P, qt) \mid (P^-, qt) \mid (P, \circ \{ vs\_1, \dots, vs\_n \}) \\ vs ::= (a, \varepsilon) \mid (patt, \varepsilon), \end{array} \tag{1}$$

*where: (a)* ◦ ∈ {∨,∧}*; (b) a pair* (lab, ◦{t1, ··· , t*n*}) *denotes a tree with a root labeled by* lab *and a set* {t1, ··· , t*n*} *of subtrees; when* ◦ *is* ∨*, then the set of subtrees is treated as a disjunctive set, if* ◦ *is* ∧*, then the set of subtrees is treated as a conjunctive set; (c) "*root*" is a distinguished label outside the ontology,* C ∈ UP*,* P ∈ BP*,* a ∈ Const*,* patt *is a pattern denoting a set of constant values (strings) belonging to* String*.*

*Example 5.* The faceted query tree in Fig. [3\(](#page-9-0)b) is the following expression consistent with the syntax [\(1\)](#page-11-1) (the root label will be always omitted):

$$T\_1 = \vee\{(Paper, \wedge\{(presentedAt, \vee\{(ACMCConf, \varepsilon), (DEXACConf, \varepsilon)\})\},\\(writtenBy, \vee\{(Person, \wedge\{(?INRIAFname', \varepsilon), ('PUTPoldand', \varepsilon)\})\})\}$$

For the faceted query tree in Fig. [4\(](#page-11-0)a), we have:

$$T\_2 = \vee \{ \{ ACMPaper, \wedge \{ (paperTitle, \wedge \{ (\%dataase\%, \varepsilon), (\%query\%, \varepsilon) \}) \} \} \}$$

The semantics of a faceted query tree is given by a first-order query (FOFQ), obtained by means of the semantic function [[ ]]*x*, where x is a free variable in FOFQ.

**Definition 6 (First order faceted query).** *The semantics of a faceted query tree* fqt*, denoted* [[fqt]]*<sup>x</sup> is the first-order query defined as follows:*

$$\begin{array}{c}
  \begin{array}{l}
    [fq]_x = [qt]_x \\
    [qt]_x = [ct]_x \circ \cdots \circ [ct]_x
  \end{array} \\
  \begin{array}{l}
    [(C,\varepsilon)]_x = C(x) \\
    [(C,\varepsilon)]_x = C(x) \\
    [(P,qt)]_x = \exists y\,(P,x,y) \land [qt]_y \\
    [(P,qt)]_x = \exists y\,(P,x,y) \land [qt]_y
  \end{array}
\end{array} \tag{2}$$
 
$$\begin{array}{l} \left[\left(P^{-},qt\right]\right]\_{x} = \exists y\left(P(y,x)\land\left[qt\right]\_{y}\right) \\ \left[\left(P,\circ\left[vs\_{1},\cdots,vs\_{n}\right)\right]\right]\_{x} = \exists y\left(P(x,y)\land\left[\left[vs\_{1}\right]\_{y}\circ\cdots\circ\left[vs\_{n}\right]\_{y}\right)\right) \\ \left[\left(a,\varepsilon\right)\right]\_{x} = x = a \\ \left[\left(patt,\varepsilon\right)\right]\_{x} = x \text{ LKE } patt, \end{array} \end{array} \tag{2}$$

*where the variable* y *under existential quantifier is "fresh:, i.e., any variable is at most once quantified.*

In Fig. [3\(](#page-9-0)c) and in Fig. [4\(](#page-11-0)b), there are first-order faceted queries in forms of syntactic trees. Note, that those queries are built from both extensional and intensional predicates.

### **4.3 Transformation into Extensional First-Order Form and to SQL**

Now, we show how a first-order faceted query is rewritten into an extensional form, i.e., a form, in which no intensional predicate appears. In the rewriting process, any atom built from an intensional predicate is rewritten according to the following procedure:

- 1. Let P(y1, y2) be an atom in q, where P is an intensional predicate. Let ω = α(x1, x2, x3) → P(x1, x2) be a rewriting rule of the form (RW-1). (RW-2) or (RW-3) in Table [1,](#page-3-0) and let variables x1, x2, x<sup>3</sup> do not appear in q.
- 2. The unification of P(y1, y2) and P(x1, x2), implies the substitution θ = [x1/y1, x2/y2], i.e., any occurrence of x*<sup>i</sup>* must be substituted by y*i*, i = 1, 2.
- 3. The atom P(y1, y2) is replaced in q with α(x1, x2, x3)[x1/y1, x2/y2], i.e., with the body of the rewriting rule with variables substituted accordingly.
- 4. A similar procedure is applied for any atom C(y), where C is an intensional predicate, and the rewriting rule is of the form (RW-3) or (RW-4).

*Example 6.* Let us consider the following query, that is a fragment of FOFQ in Fig. [3\(](#page-9-0)c):

$$q(x) = PAP(x) \land \exists x\_1 \; presentedAt(x, x\_1) \land ACMConf(x\_1).$$

To rewrite the atom presentedAt(x, x1), we can use the following rewriting rule of the form (RW-1), given in Example [1\(](#page-2-2)4), in which names of variables are changed to be different from those in the considered FOFQ in Fig. [3\(](#page-9-0)c):

∃x<sup>4</sup> inP roc(x5, x4)∧P roceedings(x4)∧ofConf(x4, x6) → presentedAt(x5, x6).

Then, the substitution unifying the considered atom and the head of the rewriting rule, is θ = [x5/x, x6/x1]. Thus, after rewriting we have

$$\begin{array}{c} q\_1(x) = Apper(x) \land \exists x\_4 \; in \, Proc(x, x\_4) \land Proccedings(x\_4) \land \exists x\_1 \; of \, Conf(x\_4, x\_1) \\ \land ACMConf(x\_1). \end{array}$$

Extensional forms of the considered faceted queries are presented in Figs. [3\(](#page-9-0)d) and [4\(](#page-11-0)c), respectively. Finally, the extensional FOFQ is translated into SQL query. For our running examples, results of these translations are shown in Fig. [5,](#page-13-0) and in Fig. [4\(](#page-11-0)d), respectively.

```
SELECT DISTINCT x.Id FROM Paper as x
JOIN Proceedings as x4 ON x.ProcId = x4.Id
JOIN Conference as x1 ON x4.ConfId = x1.Id AND x1.Acronym LIKE '%ACM%'
JOIN AuthorPaper as x_x2 ON x.Id = x_x2.PaperId
JOIN Person as x2 ON x_x2.PersonId = x2.Id
JOIN Affiliation as x2_x3 ON x2.Id = x2_x3.PersonId
AND x2_x3.Affiliation IN ('INRIA, France','PUT, Poland')
UNION SELECT DISTINCT x.Id FROM Paper as x
JOIN Proceedings as x4 ON x.ProcId = x4.Id
JOIN Conference as x1 ON x4.ConfId = x1.Id AND x1.Acronym LIKE '%DEXA%'
JOIN AuthorPaper as x_x2 ON x.Id = x_x2.PaperId
JOIN Person as x2 ON x_x2.PersonId = x2.Id
JOIN Affiliation as x2_x3 ON x2.Id = x2_x3.PersonId
AND x2_x3.Affiliation IN ('INRIA, France','PUT, Poland')
```

```
Fig. 5. SQL query created from extensional FOFQ in Fig. 3(d).
```
#### **5 Summary**

In this paper, we presented solutions to some model and language transformations in ontological databases. We considered an ontological database as a knowledge base, where its schema (ontological schema) is defined in a form of an ontology, and the extensional part is stored in a relational database. We studied two transformation problems in such scenario: (a) a mapping from the terminological part of the ontology into relational database schema (a meta-schema mapping), and (b) a mapping from an extensional part of the ontology into relational database instance (a schema mapping). We focused also on transformations connected with formulating faceted queries over an ontological schema. A faceted query is created using a faceted interface, and must be: (a) transformed into a first-order query, (b) rewritten into extensional first-order query, and finally (c) translated into SQL query, that is executed in relational database. The considerations are illustrated by solutions implemented in the DAFO (*Data Access based in Faceted queries over Ontologies*) system [\[14](#page-14-15)[,17](#page-14-16)].

This research has been supported by Polish Ministry of Science and Higher Education under grant 04/45/DSPB/0185.

## **References**

- <span id="page-14-4"></span>1. Abiteboul, S., Hull, R., Vianu, V.: Foundations of Databases. Addison-Wesley, Reading (1995)
- <span id="page-14-1"></span>2. Arenas, M., Grau, B.C., Kharlamov, E., Marciuska, S., Zheleznyakov, D.: Faceted search over ontology-enhanced RDF data. In: ACM CIKM 2014, pp. 939–948 (2014)
- <span id="page-14-13"></span>3. Baader, F., Calvanese, D., McGuinness, D., Nardi, D., Petel-Schneider, P. (eds.): The Description Logic Handbook: Theory Implementation and Applications. Cambridge University Press, New York (2003)
- <span id="page-14-5"></span>4. Cal`ı, A., Gottlob, G., Lukasiewicz, T., Pieris, A.: *Datalog* + */*− : a family of languages for ontology querying. In: de Moor, O., Gottlob, G., Furche, T., Sellers, A. (eds.) Datalog 2.0 2010. LNCS, vol. 6702, pp. 351–368. Springer, Heidelberg (2011). [https://doi.org/10.1007/978-3-642-24206-9](https://doi.org/10.1007/978-3-642-24206-9_20) 20
- <span id="page-14-6"></span>5. Cal`ı, A., Gottlob, G., Pieris, A.: Advanced processing for ontological queries. PVLDB **3**(1), 554–565 (2010)
- <span id="page-14-0"></span>6. Calvanese, D., De Giacomo, G., Lembo, D., Lenzerini, M., Poggi, A., Rosati, R.: Ontology-based database access. In: SEBD 2007, pp. 324–331 (2007)
- <span id="page-14-14"></span>7. Calvanese, D., Giacomo, G., Lembo, D., Lenzerini, M., Rosati, R.: Tractable reasoning and efficient query answering in description logics: the DL-Lite family. J. Autom. Reason. **39**(3), 385–429 (2007)
- <span id="page-14-7"></span>8. Fagin, R., Kolaitis, P.G., Miller, R.J., Popa, L.: Data exchange: semantics and query answering. Theor. Comput. Sci. **336**(1), 89–124 (2005)
- <span id="page-14-8"></span>9. Fagin, R., Kolaitis, P.G., Popa, L.: Data exchange: getting to the core. ACM Trans. Database Syst. **30**(1), 174–210 (2005)
- <span id="page-14-3"></span>10. Gottlob, G., Orsi, G., Pieris, A.: Query rewriting and optimization for ontological databases. ACM Trans. Database Syst. **39**(3), 25:1–25:46 (2014)
- <span id="page-14-12"></span>11. Gruber, T.R.: Toward principles for the design of ontologies used for knowledge sharing? Int. J. Hum. Comput. Stud. **43**(5–6), 907–928 (1995)
- <span id="page-14-9"></span>12. Hahn, R., Bizer, C., Sahnwaldt, C., Herta, C., Robinson, S., B¨urgle, M., D¨uwiger, H., Scheel, U.: Faceted Wikipedia search. In: Abramowicz, W., Tolksdorf, R. (eds.) BIS 2010. LNBIP, vol. 47, pp. 1–11. Springer, Heidelberg (2010). [https://doi.org/](https://doi.org/10.1007/978-3-642-12814-1_1) [10.1007/978-3-642-12814-1](https://doi.org/10.1007/978-3-642-12814-1_1) 1
- <span id="page-14-2"></span>13. Motik, B., Horrocks, I., Sattler, U.: Bridging the gap between OWL and relational databases. J. Web Semantics **7**(2), 74–89 (2009)
- <span id="page-14-15"></span>14. Pankowski, T.: Exploring ontology-enhanced bibliography databases using faceted search. In: Kamps, J., Tsakonas, G., Manolopoulos, Y., Iliadis, L., Karydis, I. (eds.) TPDL 2017. LNCS, vol. 10450, pp. 27–39. Springer, Cham (2017). [https://doi.org/](https://doi.org/10.1007/978-3-319-67008-9_3) [10.1007/978-3-319-67008-9](https://doi.org/10.1007/978-3-319-67008-9_3) 3
- <span id="page-14-10"></span>15. Pankowski, T.: Rewriting and executing faceted queries over ontology-enhanced databases. In: 21st Conference on Knowledge-Based and Intelligent Systems (KES 2017), pp. 137–146. Procedia Computer Science, Elsevier (2017)
- <span id="page-14-11"></span>16. Pankowski, T., Brzykcy, G.: Data access based on faceted queries over ontologies. In: Hartmann, S., Ma, H. (eds.) DEXA 2016. LNCS, vol. 9828, pp. 275–286. Springer, Cham (2016). [https://doi.org/10.1007/978-3-319-44406-2](https://doi.org/10.1007/978-3-319-44406-2_21) 21
- <span id="page-14-16"></span>17. Pankowski, T., Brzykcy, G.: Faceted query answering in a multiagent system of ontology-enhanced databases. In: Jezic, G., Chen-Burger, Y.-H.J., Howlett, R.J., Jain, L.C. (eds.) Agent and Multi-Agent Systems: Technology and Applications. SIST, vol. 58, pp. 3–13. Springer, Cham (2016). [https://doi.org/10.1007/978-3-](https://doi.org/10.1007/978-3-319-39883-9_1) [319-39883-9](https://doi.org/10.1007/978-3-319-39883-9_1) 1
- <span id="page-15-6"></span>18. Reiter, R.: On closed world data bases. In: Logic and Data Bases, pp. 55–76 (1977)
- <span id="page-15-1"></span>19. Sequeda, J.F., Arenas, M., Miranker, D.P.: OBDA: query rewriting or materialization? In practice, both!. In: Mika, P., Tudorache, T., Bernstein, A., Welty, C., Knoblock, C., Vrandeˇci´c, D., Groth, P., Noy, N., Janowicz, K., Goble, C. (eds.) ISWC 2014. LNCS, vol. 8796, pp. 535–551. Springer, Cham (2014). [https://doi.](https://doi.org/10.1007/978-3-319-11964-9_34) [org/10.1007/978-3-319-11964-9](https://doi.org/10.1007/978-3-319-11964-9_34) 34
- <span id="page-15-7"></span>20. Sherkhonov, E., Cuenca Grau, B., Kharlamov, E., Kostylev, E.V.: Semantic faceted search with aggregation and recursion. In: d'Amato, C., Fernandez, M., Tamma, V., Lecue, F., Cudr´e-Mauroux, P., Sequeda, J., Lange, C., Heflin, J. (eds.) ISWC 2017. LNCS, vol. 10587, pp. 594–610. Springer, Cham (2017). [https://doi.org/10.](https://doi.org/10.1007/978-3-319-68288-4_35) [1007/978-3-319-68288-4](https://doi.org/10.1007/978-3-319-68288-4_35) 35
- <span id="page-15-0"></span>21. Skjæveland, M.G., Giese, M., Hovland, D., Lian, E.H., Waaler, A.: Engineering ontology-based access to real-world data sources. J. Web Sem. **33**, 112–140 (2015)
- <span id="page-15-2"></span>22. SPARQL Query Language for RDF (2008). [http://www.w3.org/TR/rdf-sparql](http://www.w3.org/TR/rdf-sparql-query)[query](http://www.w3.org/TR/rdf-sparql-query)
- <span id="page-15-9"></span>23. TreeView Class (2017). [https://msdn.microsoft.com/en-us/library/system.](https://msdn.microsoft.com/en-us/library/system.windows.forms.treeview(v=vs.110).aspx) [windows.forms.treeview\(v=vs.110\).aspx](https://msdn.microsoft.com/en-us/library/system.windows.forms.treeview(v=vs.110).aspx)
- <span id="page-15-4"></span>24. Tunkelang, D.: Faceted Search. Morgan & Claypool Publishers, San Rafael (2009)
- <span id="page-15-5"></span>25. Tzitzikas, Y., Manolis, N., Papadakos, P.: Faceted exploration of RDF/S datasets: a survey. J. Intell. Inf. Syst. **48**, 1–36 (2016)
- <span id="page-15-3"></span>26. Vega-Gorgojo, G., Slaughter, L., Giese, M., Heggestøyl, S., Soylu, A., Waaler, A.: Visual query interfaces for semantic datasets: an evaluation study. J. Web Semantics **39**, 81–96 (2016)
- <span id="page-15-8"></span>27. Wagner, A., Ladwig, G., Tran, T.: Browsing-oriented semantic faceted search. In: Hameurlain, A., Liddle, S.W., Schewe, K.-D., Zhou, X. (eds.) DEXA 2011. LNCS, vol. 6860, pp. 303–319. Springer, Heidelberg (2011). [https://doi.org/10.1007/978-](https://doi.org/10.1007/978-3-642-23088-2_22) [3-642-23088-2](https://doi.org/10.1007/978-3-642-23088-2_22) 22