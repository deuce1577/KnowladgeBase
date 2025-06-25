![](_page_0_Picture_0.jpeg)

![](_page_0_Picture_1.jpeg)

Available online at www.sciencedirect.com Available online at www.sciencedirect.com Available online at www.sciencedirect.com

![](_page_0_Picture_3.jpeg)

Procedia Computer Science 112 (2017) 137–146 Procedia Computer Science 00 (2017) 000–000 Procedia Computer Science 00 (2017) 000–000

www.elsevier.com/locate/procedia www.elsevier.com/locate/procedia

#### International Conference on Knowledge Based and Intelligent Information and Engineering Systems, KES2017, 6-8 September 2017, Marseille, France International Conference on Knowledge Based and Intelligent Information and Engineering Systems, KES2017, 6-8 September 2017, Marseille, France

# Rewriting and Executing Faceted Queries over Ontology-Enhanced Databases Rewriting and Executing Faceted Queries over Ontology-Enhanced Databases

Tadeusz Pankowski Tadeusz Pankowski

*Institute of Control and Information Engineering, Pozna´n University of Technology, Pl. M. S.-Curie 5, 60-965, Pozna´n, Poland Institute of Control and Information Engineering, Pozna´n University of Technology, Pl. M. S.-Curie 5, 60-965, Pozna´n, Poland*

#### Abstract Abstract

Faceted search is an attractive and user friendly information retrieval paradigm. It combines two traditional approaches, namely keyword search (allowing the direct search) and navigational search (allowing for browsing the information space by iteratively narrowing the scope). In creating a faceted query, the user is provided with a tree-shaped faceted interface, which is a hierarchical view of an ontology describing the information domain. In this paper, we discuss a transformation and translation process leading from a graphical form of a faceted query, through first order forms (without and with rewriting) to a SQL query. The crucial in this is the mapping of the ontology into a relational database, and translating to SQL. In DAFO system we have used a commercial RDBMS with optimization capabilities. We report our experimental results which show very promising execution times. Faceted search is an attractive and user friendly information retrieval paradigm. It combines two traditional approaches, namely keyword search (allowing the direct search) and navigational search (allowing for browsing the information space by iteratively narrowing the scope). In creating a faceted query, the user is provided with a tree-shaped faceted interface, which is a hierarchical view of an ontology describing the information domain. In this paper, we discuss a transformation and translation process leading from a graphical form of a faceted query, through first order forms (without and with rewriting) to a SQL query. The crucial in this is the mapping of the ontology into a relational database, and translating to SQL. In DAFO system we have used a commercial RDBMS with optimization capabilities. We report our experimental results which show very promising execution times.

© 2017 The Authors. Published by Elsevier B.V. Peer-review under responsibility of KES International c 2017 The Authors. Published by Elsevier B.V. Peer-review under responsibility of KES International. c 2017 The Authors. Published by Elsevier B.V. Peer-review under responsibility of KES International.

*Keywords:* faceted search, OBDA systems, ontology-enhanced databases *Keywords:* faceted search, OBDA systems, ontology-enhanced databases

#### 1. Introduction 1. Introduction

Faceted search is a widely accepted interactive and iterative way of exploring information space. In contrast to paradigms based on query languages (such as SQL, SPARQL, XQuery), the faceted search allows for browsing the information space by iteratively narrowing the scope. It is a de facto standard for information retrieval in e-commerce application1,2,3. In faceted search, a user is provided with a graphical interface (faceted interface) used to choose (by clicking) some properties of the expected set of answers. The choice can by next changed and step by step refined when the user takes into account more and more details. The main advantage of this method is on facilitating the query formulation process through an interactive and user friendly way, and that the user is relieved of the necessity of knowing the details of data structures. The data structure is gradually revealed along with the ongoing process of query formulation. Faceted search is a widely accepted interactive and iterative way of exploring information space. In contrast to paradigms based on query languages (such as SQL, SPARQL, XQuery), the faceted search allows for browsing the information space by iteratively narrowing the scope. It is a de facto standard for information retrieval in e-commerce application1,2,3. In faceted search, a user is provided with a graphical interface (faceted interface) used to choose (by clicking) some properties of the expected set of answers. The choice can by next changed and step by step refined when the user takes into account more and more details. The main advantage of this method is on facilitating the query formulation process through an interactive and user friendly way, and that the user is relieved of the necessity of knowing the details of data structures. The data structure is gradually revealed along with the ongoing process of query formulation.

1877-0509

1877-0509 © 2017 The Authors. Published by Elsevier B.V. Peer-review under responsibility of KES International 10.1016/j.procs.2017.08.186 1877-0509 c 2017 The Authors. Published by Elsevier B.V. Peer-review under responsibility of KES International. Peer-review under responsibility of KES International.

1877-0509 c 2017 The Authors. Published by Elsevier B.V.

10.1016/j.procs.2017.08.186

<sup>∗</sup> Corresponding author. Tel.: +0-000-000-0000 ; fax: +0-000-000-0000. *E-mail address:* tadeusz.pankowski@put.poznan.pl ∗ Corresponding author. Tel.: +0-000-000-0000 ; fax: +0-000-000-0000. *E-mail address:* tadeusz.pankowski@put.poznan.pl

Related work: The faceted search as a relatively new data retrieval paradigm which combines the keyword search and navigational search1,2. This paradigm is widely accepted in commercial applications and was used for querying documents, databases and semantic data, e.g., 4,5,6,7. In recent years, there are some papers developing formal foundation for faceted search, notably8,9,10. In8, it was shown that faceted queries are equivalent to a subclass of monadic first order positive existential queries. In<sup>11</sup> we proposed a method of generating faceted queries staring from a keyword query, and transforming them into first order queries. In12, we proposed a way of answering faceted queries in a multiagent system. The OBDA (and also DAFO) methodology refers to data integration and date exchange methods.

2 *Tadeusz Pankowski* / *Procedia Computer Science 00 (2017) 000–000*

Contribution: In this paper, we focus on creation, transformation and efficient execution of faceted queries. The considerations are illustrated by an example of exploring bibliographical data (extracted from the DBLP bibliography13). The research is continuation of our previous studies 12,11, where the architecture of DAFO (Data Access with Faceted queries over Ontologies) system was proposed, and the execution of queries (also in a multiagent environment) was discussed. In12,11, the proposed method of execution aimed to achieve high flexibility in a multiagent system. However, then the execution time of the direct evaluation of first order faceted queries over a graph database (a set of triples), is not satisfactory. Our contributions in this paper are the following: (1) We propose transformation steps leading from a graphical form of a faceted query through first order forms to SQL query. (2) We discuss a mapping of an ontology to a relational database, and we point out which parts of the mapping should be materialized, and which not. In this way an ontology-enhanced relational database is created. (3) We present an empirical evaluation (using MS SQL Server).

The structure of the paper is as follows: In Section 2 we define an ontology of interest oriented to bibliography information space. Faceted search over ontologies in DAFA system is discussed in Section 3. A process of generating faceted queries and transforming them into first order form is proposed in Section 4. In Section 5, we show a mapping of the ontology to relational database, and translating faceted queries to SQL queries. We report also some experimental results. Section 6 summarizes the paper.

#### 2. Ontology and the running example

Formally, an ontology is a triple O = (Σ,R, A), where: (a) Σ is a *signature*, (b) R is a set of ontology rules (axioms, or *T Box*), and (c) A is a set of ontology assertions (facts, or ABox). Tha pair T = (Σ,R) is referred to as the *terminological part*, while A as the *extensional part* of an ontology.

Signature. A *signature* Σ = UP ∪ BP ∪ Const is a set of *unary*, (UP), and *binary* (BP) predicates, as well as a set Const of *constants*, occurring in axioms and assertions of the ontology. Sets of predicates are divided into *extensional* and *intentional* ones, i.e., UP = UP*<sup>E</sup>* ∪ UP*I*, and BP = BP*<sup>E</sup>* ∪ BP*I*. Extensional predicates are those, which explicitly occur in A, while intentional are defined by means of rules in R. A graphical form of the terminological part of Bibliography ontology, used in this paper as the running example, is depicted in Figure 1. Extensional predicates of the ontology are drawn by solid, while the intentional ones by dashed lines.

Rules. As a set of *ontology rules* we assume a subset of rules determined by OWL 2 RL. In Table 1, we list a set of categories of the OWL 2 RL rules referred to in this paper 11. All variables occurring in the rules are universally quantified. Further on in the paper, universal quantifications will be omitted. Rules in O are divided into two sets: *deductive rules* (denoted DR-*i*) and *integrity constraints* (denoted IC-*i*). The former can be used to deduce (infer) new facts, while the latter are used to check whether a given set of assertions is acceptable. Such interpretation of ontology axioms was studied in context of so called *extended knowledge bases* 14.

Assertions. A set of assertions is a set of atoms (sentences) of the form *C*(*a*), *a* = *b* and *P*(*a*, *b*), where *C* ∈ UP*E*, *P* ∈ BP*E*, and *a*, *b* ∈ Const.

Example 2.1. *Examples of deductive rules defining intentional predicates (universal quantifications are omitted):*

- 1. *sub*(*Author*, *Person*)*: Author is subsumed by Person: Author*(*x*) → *Person*(*x*)*.*
- 2. *dom*(*authorO f*, *Person*)*: Domain of authorOf is Person: authorO f*(*x*, *y*) → *Person*(*x*)*.*
- 3. *rng*(*authorO f*, *Paper*)*: Range of authorOf is Paper: authorO f*(*x*, *y*) → *Paper*(*y*)*.*
- 4. *spec*1(*acronym*, "%*KES*%", *KESConf*)*: KES conference is a conference with "%KES%" acronym*
	- *value(or pattern)-driven specialization: acronym*(*x*, *y*) ∧ *y* = "%*KES*%" → *KESCon f*(*x*)*.*

![](_page_2_Figure_1.jpeg)

Figure 1. Terminological part of Bibliography ontology.

Table 1. Categories of ontology rules used in Bibliography ontology.

| Id    | General form of rule                       | Name                             | Representation    |  |  |
|-------|--------------------------------------------|----------------------------------|-------------------|--|--|
| DR-1. | ∀x (D(x) → C(x))                           | subtype (subsumption)            | sub(D,C)          |  |  |
| DR-2. | ∀x, y (P(x, y) → C(x))                     | domain                           | dom(P,C)          |  |  |
| DR-3. | ∀x, y (P(x, y) → D(y))                     | range                            | rng(P, D)         |  |  |
| DR-4. | ∀x (P(x, y) ∧ y = a → C(x))                | specialization (value driven)    | spec1(P, a,C)     |  |  |
| DR-5. | ∀x, y (P(x, y) ∧ D(y) → C(x))              | specialization (type driven)     | spec2(P, D,C)     |  |  |
| DR-6. | ∀x, y,z (S (x,z) ∧ T(z, y) → P(x, y))      | chain (composition)              | chain(S, T, P)    |  |  |
| DR-7. | ∀x, y (S (y, x) → P(x, y))                 | inversion                        | inv(S, P)         |  |  |
| DR-8. | ∀x, y1, y2 (P(x, y1) ∧ P(x, y2) → y1 = y2) | functionality                    | f unc(P)          |  |  |
| DR-9. | ∀x1, x2, y (P(x1, y) ∧ P(x2, y) → x1 = x2) | key (functionality of inversion) | key(P)            |  |  |
| IC-1. | ∀x(C(x) → ∃y P(x, y)                       | existence                        | exists(C, P)      |  |  |
| IC-2. | ∀x(C(x) → ∃y P(x, y) ∧ y = a               | has value                        | hasValue(C, P, a) |  |  |

- 5. *spec*2(*presentedAt*, *KESConf* , *KESPaper*)*: KES paper is a paper presented at a KES conference – a type-driven specialization: presentedAt*(*x*, *y*) ∧ *KESCon f*(*y*) → *KES Paper*(*x*)*.*
- 6. *chain*(*inProceed*, *o fCon f*, *presentedAt*)*: presentedAt is the chain (composition) of inProceed and o fCon f : inProceedings*(*x*,*z*) ∧ *o fCon f*(*z*, *y*) → *presentedAt*(*x*, *y*)*.*
- 7. *inv*(*authorO f*, *writtenBy*)*: writtenBy is the inversion of authorO f : authorO f*(*x*, *y*) → *writtenBy*(*y*, *x*)*.*
- 8. *f unc*(*o fCon f*)*: o fCon f is a functional object property: o fCon f*(*x*, *y*1) ∧ *o fCon f*(*x*, *y*2) → *y*<sup>1</sup> = *y*2*.*
- 9. *key*(*con f Proceed*)*: con f Proceed has a key property (its inversion is a function): con f Proceed*(*x*1, *y*) ∧ *con f Proceed*(*x*2, *y*) → *x*<sup>1</sup> = *x*2*.*

*Examples of integrity constraints:*

*(1) exists*(*Author*, *authorO f*)*: For each author there exists at least one paper: Author*(*x*) → ∃*y authorO f*(*x*, *y*)*.*

*(2) hasValue*(*ACMCon f*, *acronym*, "*ACM*")*: For any ACM conference, the value of acronym is "ACM", ACMCon f*(*x*) → ∃*y acronym*(*x*, *y*) ∧ *y* = "*ACM*"*.*

# 3. Faceted search over ontologies in DAFO

Assume that a user wants to get information about papers presented at certain conferences and written by persons from some universities. It is rather unrealistic to demand that the user knows the ontology in details. Instead, her/his knowledge is rather superficial, and also the imagination of the final form of the query is rather vague.

![](_page_3_Figure_1.jpeg)

Figure 2. Steps in formulation and evaluation of a faceted query.

The user starts with an ordered keyword query *kq* = (*Paper*,*Con f erence*, *Person*) (Figure 2(a)) and expects that the underlying ontology will be viewed in a way consistent with this keyword query. She/he is interested in a view of the ontology in a form of a hierarchy where *Paper* is above *Con f erence* and *Person*, and that all properties of these concepts will be displayed. In response, DAFO provides a faceted interface as depicted in Figure 2(b). The faceted interface is a hierarchical view of a (part of) the ontology corresponding to the given keyword query. The query determines also the set of selected (checked) elements, which form the initial version of created faceted query. The selection in Figure 2(b) represents the query: *"Get papers presented at any conference and written by any person"*.

In next steps, the user continues to create the expected faceted query by refinement of the current form of the query. She/he selects and unselects visualized elements, and possibly inserts values of properties. These operations are performed in interactive and iterative way using the faceted interface. The system informs about the number of answers satisfying the current state of the faceted query (Figure 2(c)).

Any accepted version of the faceted query can be executed, and except of the count information, the entire set of answers can be displayed (Figure 2(f)).

In Figure 2, there are also depicted first order forms (as syntactic trees) of the considered faceted query: before rewriting (Figure 2(d)), and after rewriting (Figure 2(e)). They are not visible to the user, but they are used in the process of preparing the faceted query for execution. All variables in these expressions, except of *x*, are existentially quantified. The query visualized in Figure 2(d), has the textual form shown in Example 4.3.

Example 3.1. *The query "Papers presented at DEXA or KES conferences, and written by authors a*ffi*liated to "INRIA, France" or "PUT, Poland"."*

*q*(*x*) = *Paper*(*x*) ∧ ∃*x*<sup>1</sup> *presentedAt*(*x*, *x*1) ∧ (*DEXACon f*(*x*1) ∨ *KESCon f*(*x*1)) ∧∃*x*<sup>2</sup> *writtenBy*(*x*, *<sup>x</sup>*2) ∧ ∃*x*<sup>3</sup> *a f f iliation*(*x*2, *<sup>x</sup>*3) <sup>∧</sup> (*x*<sup>3</sup> <sup>=</sup> "*INRIA*, *France*" <sup>∨</sup> *<sup>x</sup>*<sup>3</sup> <sup>=</sup> "*PUT*, *Poland*"). (1)

*The form after rewriting is in Figure 2(e).*

## 4. Generating and rewriting faceted queries

In this section, we will present a process of generating faceted queries in DAFO. A user starts with specifying a keyword query. In response, a faceted interface and a first approximation of the expected faceted query are generated. The user can interactively improve the query using information provided by the interface.

### *4.1. Keyword query*

4 *Tadeusz Pankowski* / *Procedia Computer Science 00 (2017) 000–000*

answers satisfying the current state of the faceted query (Figure 2(c)).

quantified. The query visualized in Figure 2(d), has the textual form shown in Example 4.3.

*q*(*x*) = *Paper*(*x*) ∧ ∃*x*<sup>1</sup> *presentedAt*(*x*, *x*1) ∧ (*DEXACon f*(*x*1) ∨ *KESCon f*(*x*1))

answers can be displayed (Figure 2(f)).

*The form after rewriting is in Figure 2(e).*

*France" or "PUT, Poland"."*

Figure 2. Steps in formulation and evaluation of a faceted query.

The user starts with an ordered keyword query *kq* = (*Paper*,*Con f erence*, *Person*) (Figure 2(a)) and expects that the underlying ontology will be viewed in a way consistent with this keyword query. She/he is interested in a view of the ontology in a form of a hierarchy where *Paper* is above *Con f erence* and *Person*, and that all properties of these concepts will be displayed. In response, DAFO provides a faceted interface as depicted in Figure 2(b). The faceted interface is a hierarchical view of a (part of) the ontology corresponding to the given keyword query. The query determines also the set of selected (checked) elements, which form the initial version of created faceted query. The selection in Figure 2(b) represents the query: *"Get papers presented at any conference and written by any person"*. In next steps, the user continues to create the expected faceted query by refinement of the current form of the query. She/he selects and unselects visualized elements, and possibly inserts values of properties. These operations are performed in interactive and iterative way using the faceted interface. The system informs about the number of

Any accepted version of the faceted query can be executed, and except of the count information, the entire set of

In Figure 2, there are also depicted first order forms (as syntactic trees) of the considered faceted query: before rewriting (Figure 2(d)), and after rewriting (Figure 2(e)). They are not visible to the user, but they are used in the process of preparing the faceted query for execution. All variables in these expressions, except of *x*, are existentially

Example 3.1. *The query "Papers presented at DEXA or KES conferences, and written by authors a*ffi*liated to "INRIA,*

∧∃*x*<sup>2</sup> *writtenBy*(*x*, *<sup>x</sup>*2) ∧ ∃*x*<sup>3</sup> *a f f iliation*(*x*2, *<sup>x</sup>*3) <sup>∧</sup> (*x*<sup>3</sup> <sup>=</sup> "*INRIA*, *France*" <sup>∨</sup> *<sup>x</sup>*<sup>3</sup> <sup>=</sup> "*PUT*, *Poland*"). (1)

In general, a keyword query is a set of keywords 15. In this paper, we assume that keywords in the query are partially ordered (the order of keywords is essential, since it provides information about presenting the ontology.)

Definition 4.1. *A keyword query KQ over an ontology* O *with a signature* Σ = UP ∪ BP ∪ Const *is a sequence KQ* = (*C*0,*C*1,...,*CN*)*, N* ≥ 0*, of distinct unary predicates, i.e., Ci* ∈ UP*, for* 0 ≤ *i* ≤ *N.*

A specification of *KQ* defines a partial ordering, denoted by ≺, over the set {*C*0,*C*1,...,*CN*}. The first element, *C*<sup>0</sup> is the type of expected answers.

Example 4.1. *Any of the following sequences can be treated as keyword queries:*

- (*Paper*,*Con f erence*, *Person*)  *the intended faceted query will ask about papers in the space determined by conferences and persons, for example: conferences at which the paper was presented and persons being authors of the paper (see Figure 2(a)); alternatively, persons being PC members of the conference, or attending the conference, etc.;*
- (*KESCon f*, *ACMAuthor*)  *the intended faceted query will ask about KES conferences in the dimension defined by ACM authors;*
- (*Proceedings*)  *a faceted query will ask about, possibly all, proceedings.*

A keyword query provides only vague information about the intended query. The user will refine it using the faceted interface generated in response to the keyword query. An evaluation of a keyword query determines a set of paths over the underlying ontology. Any path starts with the first element of the query and ends with some element of it. Two consecutive unary predicates are connected with a binary predicate. Let *PSet*O(*KQ*) be a set of paths determined by *KQ* in ontology O.

Definition 4.2. *Let KQ* = (*C*0,*C*1,...,*CN*) *be a keyword query over* O*. Then PSet*O(*KQ*) = {*s* | *s* = (*C*0, *P*1,..., *Pm*,*Cm*)}, *where*

- *Pi connects Ci*−<sup>1</sup> *with Ci, i.e., dom*(*Pi*,*Ci*−1) ∈ R*, rng*(*Pi*,*Ci*) ∈ R*,* 1 ≤ *i* ≤ *m,* 0 ≤ *m* ≤ *N;*
- *intermediate unary predicates, Ck,* 0 < *k* < *m, may not belong to KQ,*
- *the order of unary predicates in s must obey the ordering,* ≺*, implied by unary predicates in KQ.*

Note, that in the definition above we use the fact that any subtype inherits all properties of its super type. It means, that if *C* is a subtype of *C* and *C* is a domain (range) of a predicate *P*, then also *C* is the domain (range) of *P*.

Example 4.2. *For the considered ontology, we have:*

- *PSet*O(*Paper*,*Con f erence*, *Person*) = {(*Paper*, *presentedAt*,*Con f erence*), (*Paper*, *writtenBy*, *Person*)}*,*
- *PSet*O((*KESCon f*, *ACMAuthor*) = {(*KESCon f*, *presentedPaper*, *Paper*, *writtenBy*, *ACMAuthor*)}*.*

Proposition 4.1. *The set PS et*O(*KQ*) *can be determined in a polynomial time, with respect to the length of KQ and the size of the ontology.*

The proposition follows from the fact that *PSet*O(*KQ*) can be obtained by means of depth-first algorithm on the ontology graph, and that all possible cycles in paths are eliminated. In fact, the algorithm search for all paths between two given nodes in the ontology graph.

## *4.2. Faceted interface*

A faceted interface (FI) is a view over the underlying ontology. This view is determined by the set *PSet*O(*KQ*) as follows:

- any path *s* in *PSet*O(*KQ*) is in FI,
- elements of any path *s* ∈ *PSet*O(*KQ*) are selected (checked) in FI,

6 *Tadeusz Pankowski* / *Procedia Computer Science 00 (2017) 000–000*

• if a unary predicate *C* is in FI, then all its properties (binary predicates having *C* as domain), and all subclasses of *C* are in FI.

A FI in Figure 2(b), includes two selected paths corresponding to the keyword query in Figure 2(a) (see Example 4.2). FI includes also properties of selected types (unary predicates) as well as subtypes of these types.

## *4.3. Faceted query*

A faceted query (FQ) is determined by the set of selected path in a FI. The initial faceted query follows from the given keyword query *KQ* and equals to *PSet*O(*KQ*), and is visualized on the created FI. Now, the user can operate over a current state of the FQ by means of the following operations:

- selecting and unselecting elements (nodes) in FI,
- inserting values of binary predicates,
- discarding unselected nodes,
- executing the current form of the query.

In fact, any click changes both FI and FQ. During these operations, the user is informed about cardinality of the set of answers (Figure 2(c)). A current form of a faceted query *f q* is represented by a minimal set of query paths over the ontology. A query path, in contrast to interface path, can contain also values as its last element. The path representation of *f q* will be denoted by *path*(*f q*), and if *s* ∈ *path*(*f q*), then *s* = (*C*0, *P*1,..., *Pm*,*Cm*), *s* = (*C*0, *P*1,..., *Pm*,*Cm*, *Pm*+1), or *s* = (*C*0, *P*1,..., *Pm*,*Cm*, *Pm*+<sup>1</sup>, *a*), where:

- *Ci* ∈ UP, *Pi* ∈ BP, *a* ∈ Const,
- R|= *dom*(*Pi*,*Ci*−1), 1 ≤ *i* ≤ *m* + 1, R |= *rng*(*Pi*,*Ci*), 1 ≤ *i* ≤ *m*,
- A∪R|= ∃*x Pm*+1(*x*, *a*),

where R |= φ and A∪R|= φ means that φ can be inferred in O using, respectively, R and A∪R.

The set of paths representing the faceted query α in Figure 2(c), is:

$$\begin{array}{l} \text{path}(\alpha) = \{ (\text{Paper}, \text{presentedAt}, \text{DEXConf}), \\ \qquad (\text{Papor}, \text{presentedAt}, \text{KES} \, \text{Conf}), \\ \qquad (\text{Papor}, \text{writtenBy}, \text{Person}, \text{off} \, \text{Hian}, \text{"INRIA}, \text{Fance} \text{"}), \\ \qquad (\text{Papor}, \text{writtenBy}, \text{Person}, \text{off} \, \text{Hian}, \text{"PUT}, \text{Pynan"}) \}. \end{array} \tag{2}$$

From a set of paths representing a faceted query, a textual form of the faceted query can be created. For example, from (2) we obtain the following text form of faceted query:

$$\alpha = \langle \text{Paper} \vert [(\text{presentedAt}, \text{at}\eta) \vert \langle \text{DEXACconf}, \text{KESConf} \rangle \wedge \langle \text{writtenBy}, \text{at}\eta \rangle \rangle \tag{5}$$

By creating the textual form (3) of a faceted query represented by the set of query paths (2), the following interpretation is applied:

1. *Paper* is the type of expected answers.

- 2. {*Paper*}[β] restricts the set of papers to those, which satisfy conditions specified in β, in our case to papers presented at given conferences, and written by authors affiliated to given institutions.
- 3. (*presentedAt*, any)/α says that we are interested in those elements from the domain of *presentedAt*, (*Paper* in this case), which has been presented at *any* conference from the set of conferences specified in α.
- 4. (*a f f iliation*, {"*INRIA*, *France*", "*PUT*, *Poznan*"}) says that we are interested in those elements from the domain of *a f f iliation*, (*Person* in this case), which are connected by the binary predicate *a f f iliation* to one of the two given constants.

The formal definition of a faceted query is given in Definition 4.3.

Definition 4.3. *A faceted query is an expression* α *conforming to the syntax:*

$$\begin{array}{l} \alpha ::= t \mid t[\beta] \mid \alpha \lor \alpha \\ \beta ::= b \mid b/\alpha \mid \beta \land \beta, \end{array}$$

*where: (a) t is a set* {*C*1,...,*Cn*} *of unary predicates; (b) b is a pair* (*P*, any) *or* (*P*, {*a*1,..., *an*})*, where* any *denotes any constant, and* {*a*1,..., *an*} *is a set of constants (possible values of property P).*

## *4.4. Translation to first order faceted queries*

The semantics of a faceted query α is defined as a first order formula 8, built from predicates and constants occurring in α, existential quantification (∃), connectives (∧, ∨), and variables. The semantics of α is defined by means of the semantic function *<sup>x</sup>*, where *x* is a variable, and the function is defined as follows 12:

1. {*A*1,..., *An*}*<sup>x</sup>* = *A*1(*x*) ∨···∨ *An*(*x*). 2. {*a*1,... *an*}*<sup>x</sup>* = (*a*<sup>1</sup> = *x*) ∨···∨ (*an* = *x*). 3. (*R*, any)*<sup>x</sup>*,*<sup>y</sup>* = *R*(*x*, *y*). 4. (*R*, {*a*1,..., *an*})*<sup>x</sup>*,*<sup>y</sup>* = *R*(*x*, *y*) ∧ {*a*1,... *an*}*<sup>y</sup>*. 5. *t*[*b*]*<sup>x</sup>* = *t<sup>x</sup>* ∧ ∃*y*(*b<sup>x</sup>*,*y*) 6. *t*[*b*/α]*<sup>x</sup>* = *t<sup>x</sup>* ∧ ∃*y*(*b<sup>x</sup>*,*<sup>y</sup>* ∧ α*<sup>y</sup>*). 7. *t*[β<sup>1</sup> ∧ β2]*<sup>x</sup>* = *t*[β1]*<sup>x</sup>* ∧ *t*[β2]*<sup>x</sup>*. 8. α<sup>1</sup> ∨ α<sup>2</sup>*<sup>x</sup>* = α<sup>1</sup>*<sup>x</sup>* ∨ α<sup>2</sup>*<sup>x</sup>*.

Variables under existential quantifier must be fresh, i.e., any variable is at most once quantified. First order forms of the considered faceted query (3) (Figure 2(c)), is given as a query syntactic tree in Figure 2(d).

## *4.5. Rewriting to extensional form*

In DAFO, any first order faceted query is rewritten into extensional form, i.e., into a form in which appear only extensional predicates. For example, the query syntactic tree in Figure 2(e) is the extensional form of the query in Figure 2(d).

As it follows from the previous subsection, a first order faceted query, *q*(*x*), is built from: (a) atoms of the form *C*(*x*), *P*(*x*, *y*), (*x* = *a*), where *x*, *y* are variables, *a* is a constant, *C* and *P* are predicates; (b) logical symbols ∧, ∨, and (c) existential quantifier ∃. Moreover, *x* is the only free variable in *q*(*x*). This class of queries is called monadic *positive existential queries* (monadic PEQ) 16,8.

Let *Ans<sup>q</sup>* <sup>O</sup> be a distinguished unary predicate to assert about answers to a query *<sup>q</sup>*(*x*) in ontology <sup>O</sup>. Then the atom *Ansq* O(*a*) says that *<sup>a</sup>* is an answer to *<sup>q</sup>*(*x*) in <sup>O</sup>, if *<sup>q</sup>*(*a*) is satisfied in <sup>O</sup>. More precisely, *<sup>q</sup>*(*a*) is satisfied in <sup>O</sup> if the set A closed with respect to R, is a model of *q*(*a*) (or *q*(*a*) can be deduced from A using R), i.e.,

$$\operatorname{An}s\_{\mathcal{O}}^q(a) \Leftrightarrow \mathcal{R} \cup \mathcal{R} \models q(a).$$

Alternatively, *q*(*x*) can be rewritten using R into *qE*(*x*), where all predicates appearing in *qE*(*x*) are extensional, i.e., *qE*(*x*) <sup>=</sup> *rewrite*R(*q*(*x*)). Then *AnsqE* <sup>O</sup> (*a*) says that *<sup>a</sup>* is an answer to *qE*(*x*) in <sup>O</sup>

$$\operatorname{Ans}\_{\mathcal{O}}^{q\_E}(a) \Leftrightarrow \mathcal{R} \vdash q\_E(a).$$

Then the evaluation of *qE*(*x*) can be based only on A.

Example 4.3. *The query "Get all authors": q*(*x*) = *Author*(*x*) *after rewriting has the form*

8 *Tadeusz Pankowski* / *Procedia Computer Science 00 (2017) 000–000*

$$q\_E(\mathbf{x}) = 
overset{\mathcal{R}}{\operatorname{g}}(q(\mathbf{x})) = 
overset{\mathcal{R}}{\operatorname{g}} 
underbrace{\operatorname{g}}\_{=\mathbf{I}}(\mathbf{x}) \land \exists \mathbf{x}\_1 \, \operatorname{a} 
hat{\operatorname{of}} f(\mathbf{x}, \mathbf{x}\_1). \tag{4}$$

Note, that without considering integrity constraints (IC-1, in Table 1), the rewriting (4) ensures only the implication: *Person*(*x*) ∧ ∃*x*<sup>1</sup> *authorO f*(*x*, *x*1) → *Author*(*x*). To guarantee the equivalence, the integrity constraint axiom *Author*(*x*) → ∃*x*<sup>1</sup> *authorO f*(*x*, *x*1) must be satisfied in the ontology.

### 5. Executing faceted queries

#### *5.1. Mapping to relational database*

In order to achieve a high degree of efficiency, in DAFO implementation the extensional part of the ontology is implemented using RDBMS9. Thus, two problems must be solved: (a) mapping the extensional part of the ontology into a relational database; (b) translating faceted queries to SQL queries. In the mapping, we first map the terminological part into database schema (the metaschema mapping level), and the assertional part to the database state (the schema mapping level). In result, we obtain an *ontology enhanced database*, since the intentional part of the ontology, i.e., a set of rules, still exists and can be utilized in query rewriting.

We start with a classification of binary predicates occurring in the ontology. Each binary predicate *P* ∈ BP is classified to one of the following four classes:

- BP*FDP functional data properties*, *P* is functional and its range is *String*;
- BP*MDP multivalued data properties*, *P* is not functional (is multivalued) and its range is *String*;
- BP*FOP functional object properties*, *P* is functional and its range is not *String*;
- BP*MOP multivalued object properties*, *P* is not functional (is multivalued) and its range is not *String*.

Let a database schema consists of the following six relational schemes (for simplicity, with restricted attribute sets):

```
Person(Id,Name), Affiliation(Id,PersonId,Affiliation), Paper(Id,Title,ProceedId),
AuthorPaper(Id,PersonId,PaperId), Proceedings(Id,BookTitle,Year,ConfId),
Conference(Id,Acronym,FullName,Year).
```
The mapping defined in Table 2, assigns elements of the above relational database schema to predicates, and also implies the existence of some foreign keys (inclusion dependencies) in the schema.

| Predicate name | Predicate class | Table       | Primary key | Domain column | Range column | Foreign key (inclusion dependency)                                 |
|----------------|-----------------|-------------|-------------|---------------|--------------|--------------------------------------------------------------------|
| Person         | UP              | Person      | Id          |               |              |                                                                    |
| name           | BPFDP           | Person      | Id          | Id            | Name         |                                                                    |
| affiliation    | BPMDP           | Affiliation | Id          | PersonId      | Affiliation  | Affiliation.PersonId ⊆ Person.Id                                   |
| authorOf       | BPMOP           | AuthorPaper | Id          | PersonId      | PaperId      | AuthorPaper.PersonId ⊆ Person.Id<br>AuthorPaper.PaperId ⊆ Paper.Id |
| Paper          | UP              | Paper       | Id          |               |              |                                                                    |
| inProceed      | BPFOP           | Paper       | Id          | Id            | ProceedId    | Paper.ProceedId ⊆ Proceedings.Id                                   |

Table 2. Mapping ontology predicates to elements of a relational database schema.

More precisely:

- 1. Each unary predicate, *C*, is mapped to a table *RC* with primary key *Id*, and *C*(*x*) → ∃*t* ∈ *RC* (*t*.*Id* = *x*).
- 2. Let *P* be a binary predicate with domain *C* and range *D*. We denote: *RP* a table assigned to *P*; *A<sup>d</sup> <sup>P</sup>* – the *domain* column, and *Ar <sup>P</sup>* – the *range* column assigned to *P*, both columns are in the set of attributes of *RP*. The domain column stores values from the domain of *P*, and the range column stores values of the range of *P*. Then:

- if *P* ∈ BP*FDP*, then *RP* = *RC*, *A<sup>d</sup> <sup>P</sup>* = *Id*, and: *P*(*x*, *y*) → ∃*t* ∈ *RC* (*t*.*Id* = *x* ∧ *t*.*A<sup>r</sup> <sup>P</sup>* = *y*).
- if *P* ∈ BP*MDP*, then *RP RC*, *A<sup>d</sup> <sup>P</sup>* is a foreign key referring to *RC*, and: *P*(*x*, *y*) → ∃*t* ∈ *RP*, ∃*r* ∈ *RC* (*t*.*Ad <sup>P</sup>* = *x* ∧ *t*.*A<sup>d</sup> <sup>P</sup>* = *r*.*Id* ∧ *t*.*A<sup>r</sup> <sup>P</sup>* = *y*).
- if *P* ∈ BP*FOP*, then *RP* = *RC*, *Ad <sup>P</sup>* = *Id*, *Ar <sup>P</sup>* is a foreign key referring to *RD*, and : *P*(*x*, *y*) → ∃*t* ∈ *RC*, ∃*r* ∈ *RD* (*t*.*Id* = *x* ∧ *t*.*A<sup>r</sup> <sup>P</sup>* = *y* ∧ *t*.*A<sup>r</sup> <sup>P</sup>* = *r*.*Id*).
- if *P* ∈ BP*MOP*, then *RP RC*, *RP RD*, *A<sup>d</sup> <sup>P</sup>* and *A<sup>r</sup> <sup>P</sup>* are foreign keys referring to *RC* and *RD*, and: *P*(*x*, *y*) → ∃*t* ∈ *RP*, ∃*r* ∈ *RC*, ∃*s* ∈ *RD* (*t*.*A<sup>d</sup> <sup>P</sup>* = *x* ∧ *t*.*A<sup>d</sup> <sup>P</sup>* = *r*.*Id* ∧ *t*.*A<sup>r</sup> <sup>P</sup>* = *y* ∧ *t*.*A<sup>r</sup> <sup>P</sup>* = *s*.*Id*).

A faceted query in an extensional first order form, can now be translated to SQL. The translation is driven by the mapping to a relational database schema.Our running example query, presented in Figure 2(e) with the mapping defined in Table 2, is translated to SQL presented in Figure 3. Note, that constants in rules of *spec*1 category can be *patterns* of the form *"%s%"* or regular constants *a*. The former are translated to LIKE conditions in SQL.

```
SELECT DISTINCT x.Id FROM Paper as x
  JOIN Proceedings as x4 ON x.ProcId = x4.Id
  JOIN Conference as x1 ON x4.ConfId = x1.Id
    AND x1.Acronym LIKE '%DEXA%'
  JOIN AuthorPaper as x_x2 ON x.Id = x_x2.PaperId
  JOIN Person as x2 ON x_x2.PersonId = x2.Id
  JOIN Affiliation as x2_x3 ON x2.Id = x2_x3.PersonId
    AND (x2_x3.Affiliation = 'INRIA, France'
    OR x2_x3.Affiliation = 'PUT, Poznan')
                                                         UNION
                                                         SELECT DISTINCT x.Id FROM Paper as x
                                                           JOIN Proceedings as x4 ON x.ProcId = x4.Id
                                                           JOIN Conference as x1 ON x4.ConfId = x1.Id
                                                             AND x1.Acronym LIKE '%KES%'
                                                           JOIN AuthorPaper as x_x2 ON x.Id = x_x2.PaperId
                                                           JOIN Person as x2 ON x_x2.PersonId = x2.Id
                                                           JOIN Affiliation as x2_x3 ON x2.Id = x2_x3.PersonId
                                                             AND (x2_x3.Affiliation = 'INRIA, France'
                                                             OR x2_x3.Affiliation = 'PUT, Poznan')
```
Figure 3. SQL form of the faceted query depicted in Figure 2(e).

## *5.2. Experiments*

In this section, we present experimental results of evaluating a faceted query in DAFO system. The evaluated query is the faceted query in Figure 2(c) translated to SQL (Figure 3). Results of the evaluation are displayed in Table 3.

| Volume of                 | Generating | Rewriting | Translation | Total time of    | Execution | Displaying | Total time | # of    |
|---------------------------|------------|-----------|-------------|------------------|-----------|------------|------------|---------|
| dataset                   | [msec]     | [msec]    | [msec]      | preparing [msec] | [msec]    | [msec]     | [msec]     | answers |
| dataset 1 ( 47 970 atoms) | 31         | 98        | 10          | 139              | 41        | 72         | 252        | 19      |
| dataset 2 ( 95 940 atoms) | 34         | 103       | 10          | 147              | 43        | 132        | 322        | 38      |
| dataset 3 (143 910 atoms) | 34         | 98        | 10          | 142              | 45        | 191        | 378        | 57      |
| dataset 4 (239 850 atoms) | 33         | 98        | 11          | 142              | 52        | 326        | 520        | 90      |
| dataset 5 (479 700 atoms) | 35         | 102       | 13          | 150              | 97        | 673        | 920        | 190     |

Table 3. Times of evaluation the faceted query in Figure 2(c) in five datasets.

The evaluation concerns the following stages:

- *Preparing*. This stage includes: (a) generating the first order form of the given faceted query, Figure 2(d); (b) rewriting it into a form with only extensional predicates Figure 2(e); and (c) translating to SQL query, Figure 3, using the mapping of the ontology to a relational database schema. The most time consuming is rewriting, which involves analysis of the entire terminological part of the ontology. This stage depends only on the terminological part of the ontology, thus is independent of the size of the dataset.
- *Execution*. A SQL query representing the faceted query is executed against a relational database, which is a materialized view defined in the mapping of the ontology into relational database schema. The high efficiency of the execution follows from the optimization capabilities implemented in commercial RDBMS (MS SQL Server, in this case).
- *Displaying*. Displaying of the set of answers requires the most time. This is due to the fact that each components constituting any answer (e.g., title of the paper, information about proceedings) are fetched from the database and the appropriate HTML document must be created.

In the evaluation we used Lenovo X1 Carbon Ultrabook laptop with Windows 10 Pro, 2.60 GHz Intel Core i7 processor, and 8GB RAM memory. The DAFO system is written in C# Visual Studio Community 2015, .NET Framework 4.6, Entity Framework 6.1. As the database server we use MS SQL Server 2016 (all under the license Microsoft Imagine Premium). The dataset was prepared by extracting data from DBLP<sup>17</sup> resources (from XML, HTML, and BibTex files), and enriched with data extracted form personal and conference home pages. For the evaluation purposes, the basic set (dataset 1) was multiplied two (dataset 2), three (dataset 3), five (dataset 4), and ten (dataset 5) times.

10 *Tadeusz Pankowski* / *Procedia Computer Science 00 (2017) 000–000*

#### 6. Summary

We presented some features of DAFO (Data Access with Faceted queries over Ontologies), which is an OBDA system. We focus on rewriting and execution of queries in DAFO system, starting from a faceted query formulation and ending in execution of its SQL counterpart in a relational database system. In DAFO approach, a conceptual model of the application domain is defined by an ontology with a set of OWL 2 RL rules. We shown how a hierarchical view over the ontology can be obtained using ordered keyword queries. In response to the ordered keyword query, the system generates the faceted interface and the initial form of the faceted query. The user can iteratively modify this query, taking into account the provided view of the ontology and the feedback information about cardinality of the current set of answers. We described the way how the faceted query is created from the user's selections made on the faceted interface. The faceted query is next translated into first order form (including both extensional and intentional predicates), rewritten into the extensional form (including only extensional predicates), and finally translated to SQL. The translation to SQL is controlled by a mapping of the underlying ontology to a relational database treated as a view over this ontology. To ensure high efficiency, this view is materialized as a state of a relational database, and the SQL form of the faceted query is executed against this database. We reported some computational experiments, which proved that this way of implementing DAFO systems is very promising. In all cases, the response time was less tan 1 second. This is due to the utilization of commercial RDBMS with excellent optimization capabilities. This research has been supported by Polish Ministry of Science and Higher Education under grant 04/45/DSPB/0163.

### References

- 1. Tunkelang, D.. *Faceted Search*. Morgan & Claypool Publishers; 2009.
- 2. Dumais, S.T.. Faceted search. In: *Encyclopedia of Database Systems*. Springer; 2009, p. 1103–1109.
- 3. Tzitzikas, Y., Manolis, N., Papadakos, P.. Faceted exploration of RDF/S datasets: a survey. *Journal of Intelligent Information Systems* 2016;p. 1–36.
- 4. Dork, M., Riche, N.H., Ramos, G., Dumais, S.T.. Pivotpaths: Strolling through faceted information spaces. ¨ *IEEE Trans Vis Comput Graph* 2012;18(12):2709–2718.
- 5. Zhuge, H., Wilks, Y.. Faceted search, social networking and interactive semantics. *World Wide Web* 2014;17(4):589–593.
- 6. Heim, P., Ertl, T., Ziegler, J.. Facet graphs: Complex semantic querying made easy. In: *7th Extended Semantic Web Conference, ESWC 2010*; vol. 6088 of *Lecture Notes in Computer Science*. Springer; 2010, p. 288–302.
- 7. Papadakos, P., Tzitzikas, Y.. Hippalus: Preference-enriched faceted exploration. In: *Workshops of the EDBT*/*ICDT*; vol. 1133 of *CEUR Workshop Proceedings*. CEUR-WS.org; 2014, p. 167–172.
- 8. Arenas, M., Grau, B.C., Kharlamov, E., Marciuska, S., Zheleznyakov, D.. Faceted search over ontology-enhanced RDF data. In: *ACM CIKM 2014*. ACM; 2014, p. 939–948.
- 9. Sequeda, J.F., Arenas, M., Miranker, D.P.. OBDA: query rewriting or materialization? in practice, both! In: *The Semantic Web ISWC 2014*; vol. 8796 of *Lecture Notes in Computer Science*. Springer; 2014, p. 535–551.
- 10. Wagner, A., Ladwig, G., Tran, T.. Browsing-oriented semantic faceted search. In: *DEXA 2011*; vol. 6860 of *LNCS*. Springer; 2011, p. 303–319.
- 11. Pankowski, T., Brzykcy, G.. Data access based on faceted queries over ontologies. In: *DEXA 2016*; vol. 9828 of *Lecture Notes in Computer Science*. Springer; 2016, p. 275–286.
- 12. Pankowski, T., Brzykcy, G.. Faceted Query Answering in a Multiagent System of Ontology-Enhanced Databases. In: *Agent and Multi-Agent Systems: Technologies and Applications (KES-AMSTA 2016)*; vol. 58 of *Smart Innovation Systems and Technologies*. Springer; 2016, .
- 13. Ley, M.. DBLP some lessons learned. *PVLDB* 2009;2(2):1493–1500.
- 14. Motik, B., Horrocks, I., Sattler, U.. Bridging the gap between OWL and relational databases. *Journal of Web Semantics* 2009;7(2):74–89.
- 15. Chen, Y., Wang, W., Liu, Z., Lin, X.. Keyword search on structured and semi-structured data. In: *ACM SIGMOD 2009*. 2009, p. 1005–1010.
- 16. Abiteboul, S., Hull, R., Vianu, V.. *Foundations of Databases*. Reading, Massachusetts: Addison-Wesley; 1995.
- 17. DBLP Computer Science Bibliography, 2017. http://dblp.org/.