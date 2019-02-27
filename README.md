[Instance](https://neo4j.het.io/browser/)


### Graph Stats

##### Find number of each node type in graph
>MATCH (node)<br/>
>RETURN<br/>
>  head(labels(node)) AS label,<br/>
>  count(*) AS count<br/>
>ORDER BY count DESC

##### Find number of each edge type in graph
>MATCH ()-[rel]->()<br/>
>RETURN<br/>
>  type(rel) AS rel_type,<br/>
>  count(*) AS count<br/>
>ORDER BY count DESC

##### Get examples of each edge/node type
>MATCH ()-[rel]->()<br/>
>WITH type(rel) AS rel_type, collect(rel) AS rels<br/>
>WITH rels[toInteger(rand() * size(rels))] AS rel<br/>
>RETURN startNode(rel), rel, endNode(rel)

### Multiple Sclerosis (MS) examples

##### Find Disease node for MS
>MATCH (d: Disease) WHERE d.name =~ '(?i).\*Multiple Sclerosis.\*'<br/>
>RETURN d

##### Find Compounds that treat MS
>MATCH (d: Disease) WHERE d.name =~ '(?i).\*Multiple Sclerosis.\*'<br/>
>MATCH compound_for_ms = ((c: Compound)-[:TREATS_CtD]-(d))<br/>
>RETURN compound_for_ms

##### Find Genes that bind Compounds that trat MS
>MATCH (d: Disease) WHERE d.name =~ '(?i).\*Multiple Sclerosis.\*'<br/>
>MATCH compound_for_ms = ((c: Compound)-[:TREATS_CtD]-(d))<br/>
>MATCH ms_genes_with_compound= ((c)-[:BINDS_CbG]-(g:Gene)-[]-(d))<br/>
>RETURN ms_genes_with_compound

##### Find Molecular Functions (GeneOntology) for the Genes that bind Compounds that trat MS
>MATCH (d: Disease) WHERE d.name =~ '(?i).\*Multiple Sclerosis.\*'<br/>
>MATCH compound_for_ms = ((c: Compound)-[:TREATS_CtD]-(d))<br/>
>MATCH ms_genes_with_compound= ((c)-[:BINDS_CbG]-(g:Gene)-[]-(d))<br/>
>MATCH mf = ((g)-[:PARTICIPATES_GpMF]-(:MolecularFunction))<br/>
>RETURN ms_genes_with_compound, mf

### Myelin examples

##### Find all GeneOntology nodes involving Myelin
>MATCH (cc: CellularComponent) WHERE cc.name =~ '(?i).\*Myelin.\*'<br/>
>MATCH (m: MolecularFunction) WHERE m.name =~ '(?i).\*Myelin.\*'<br/>
>MATCH (b: BiologicalProcess) WHERE b.name =~ '(?i).\*Myelin.\*'<br/>
>RETURN cc, m, b

##### Find Compounds that Bind that Participate in GeneOntology nodes involving Myelin
>MATCH (cc: CellularComponent) WHERE cc.name =~ '(?i).\*Myelin.\*' <br/>
>MATCH (m: MolecularFunction) WHERE m.name =~ '(?i).\*Myelin.\*'<br/>
>MATCH (b: BiologicalProcess) WHERE b.name =~ '(?i).\*Myelin.\*'<br/>
>MATCH compounds_for_myelin_genes = ((c: Compound)-[:BINDS_CbG]-(:Gene)-[:PARTICIPATES_GpCC]-(cc)), ((c)-[]-(:Gene)-[:PARTICIPATES_GpMF]-(m)), ((c)-[]-(:Gene)-[:PARTICIPATES_GpBP]-(b))<br/>
>RETURN compounds_for_myelin_genes

##### Find Diseases Treated by Compounds that Bind Genes that Participate in GeneOntology nodes involving Myelin
>MATCH (cc: CellularComponent) WHERE cc.name =~ '(?i).\*Myelin.\*' <br/>
>MATCH (m: MolecularFunction) WHERE m.name =~ '(?i).\*Myelin.\*'<br/>
>MATCH (b: BiologicalProcess) WHERE b.name =~ '(?i).\*Myelin.\*'<br/>
>MATCH compounds_for_myelin_genes = ((c: Compound)-[:BINDS_CbG]-(:Gene)-[:PARTICIPATES_GpCC]-(cc)), ((c)-[]-(:Gene)-[:PARTICIPATES_GpMF]-(m)), ((c)-[]-(:Gene)-[:PARTICIPATES_GpBP]-(b))<br/>
>MATCH disease_compounds_treat = ((c)-[:TREATS_CtD]-(:Disease))<br/>
>RETURN compounds_for_myelin_genes, cc, m, b, disease_compounds_treat

### DWPC example

##### Find DWPC for meta path DaGiGpBP between MS and GeneOntology BiologicalProcess nodes[1](https://think-lab.github.io/d/220/)<br/>
>MATCH path = (n0:Disease)-[e1:ASSOCIATES_DaG]-(n1)-[:INTERACTS_GiG]-(n2)-[:PARTICIPATES_GpBP]-(n3:BiologicalProcess)<br/>
>WHERE n0.name = 'multiple sclerosis'<br/>
>  AND 'GWAS Catalog' in e1.sources<br/>
>  AND exists((n0)-[:LOCALIZES_DlA]-()-[:UPREGULATES_AuG]-(n2))<br/>
>WITH<br/>
>[<br/>
>  size((n0)-[:ASSOCIATES_DaG]-()),<br/>
>  size(()-[:ASSOCIATES_DaG]-(n1)),<br/>
>  size((n1)-[:INTERACTS_GiG]-()),<br/>
>  size(()-[:INTERACTS_GiG]-(n2)),<br/>
>  size((n2)-[:PARTICIPATES_GpBP]-()),<br/>
>  size(()-[:PARTICIPATES_GpBP]-(n3))<br/>
>] AS degrees, path, n3 as target<br/>
>WITH<br/>
>  target.identifier AS go_id,<br/>
>  target.name AS go_name,<br/>
>  count(path) AS PC,<br/>
>  sum(reduce(pdp = 1.0, d in degrees| pdp * d ^ -0.7)) AS DWPC,<br/>
>  size((target)-[:PARTICIPATES_GpBP]-()) AS n_genes<br/>
>  WHERE 5 <= n_genes <= 100 AND PC >= 2<br/>
>RETURN<br/>
>  go_id, go_name, PC, DWPC, n_genes<br/>
>ORDER BY DWPC DESC<br/>
>LIMIT 5

[MS example workshop](https://nbviewer.jupyter.org/github/baranzini-lab/PSPG_245B/blob/master/ms_example_notebook2.ipynb)
 
[Epilepsy example workshop](https://nbviewer.jupyter.org/github/baranzini-lab/PSPG_245B/blob/master/epilepsy_example_notebook.ipynb)

##### Other examples
>DISEASE = 'DOID:6364'<br/>
>DISEASE_NAME = "migraine"

>DISEASE = 'DOID:0050742'<br/>
>DISEASE_NAME = "nicotine_dependence"

##### For breast cancer please use:
>DISEASE = 'DOID:1612'<br/>
>DISEASE_NAME = "breast_cancer"

