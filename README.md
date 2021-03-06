Neo4j-Cypher-project


Cypher query to load data from the Comma Separated value (CSV) file namely:gene_gene_associations_50k.csv into a Neo4j graph database table

LOAD CSV WITH HEADERS FROM "file:///C:/Users/Manish/Downloads/Big%20Data%20Analytics/neo4j_module_datasets/gene_gene_associations_50k.csv" AS geneAction
MERGE (n:TrialGene {Name:geneAction.OFFICIAL_SYMBOL_A})
MERGE (m:TrialGene {Name:geneAction.OFFICIAL_SYMBOL_B})
MERGE (n) -[:AssociationType {AssociatedWith:geneAction.EXPERIMENTAL_SYSTEM}]-> (m)

As part of the mini-project,I have tried to answer few of the questions associated with the above mentioned dataset using the Neo4j Cypher queries.

1. Determination of number of nodes in the graph database table imported into Neo4j from the CSV file.

Cypher Query:match (n:TrialGene) return count(n) 

Insight:From the above cypher query we have found that there are 9656 nodes or genes in the graph DB table.

2.Determination of number of edges (edges are relationship between genes) in the graph database table.

Cypher Query:match(n:TrialGene)-[r]->() return count(r) 
Insight:We have found 46621 relationships present in between 9656 genes(nodes) present in Graph Database. 

3. Determine if there are any self associations present amongst the genes in the graph DB table.If yes,determine the count these relationships.

Cypher Query:match (n)-[r]->(n) return count(r)   
Insight:Yes,there are few self associations amongst the 9656 genes.The above query provides us the count of such relationships to be 1221.Thus,we 
can infer that all 46621 associations is not only amongst two genes but it can be between the same gene.

4.Determine the number of gene associations which are only between two pairs genes along with the name of the genes.

Cypher Query:match (n)-[r]->(m) where m <> n return distinct n, m, count(r)
Insight:We have found that there are 37823 associations between only two pair of genes.(Please see Github repo:Neo4j-Cypher-project for Graph)

5.Determine the pair of nodes with the maximum number of multi-edges or multiple associations between them.

Cypher Query:match (n)-[r]->(m) where m <> n return distinct n, m, count(r) as myCount order by myCount desc limit 1
Insight:We have found genes:BRCA1 and RBBP8 are having 7 multiple associations between them and are having the maximum number of associations between them.
We can also guess that for any of the genes to work fine,it is mandatory for the other gene also to be functional.  (Please see Github repo:Neo4j-Cypher-project for Graph)

6.Create a graph to show the network of genes which are 2-neighborhood of the node whose name is �BRCA1�?

Cypher Query:match (n)-[r:AssociationType...2]->() where n.Name="BRCA1" return n          (Please see Github repo:Neo4j-Cypher-project for Graph)

7. Determine the number of shortest paths there are between the node named �BRCA1� and the node named �NBR1�. 

Cypher Query:MATCH p = allShortestPaths(OFFICIAL_SYMBOL_A-[r:AssociationType*]-OFFICIAL_SYMBOL_B)
WHERE OFFICIAL_SYMBOL_A.Name='BRCA1' AND OFFICIAL_SYMBOL_B.Name = 'NBR1'
RETURN EXTRACT(num IN NODES(p)| num.Name) AS Paths  (Please see Github repo:Neo4j-Cypher-project for Graph)

Insight:After executing the above Graph query we have found that there are 9 shortest paths between Genes:BRCA1 and NBR1.


8. Determine the top 2 genes which are influencing maximum number of gene via chemical interactions (highest outdegree).

Cypher Query:match (n:TrialGene)-[r]->()
return n.Name as Node, count(r) as Outdegree
order by Outdegree desc
union
match (a:TrialGene)-[r]->(leaf)
where not((leaf)-->())
return leaf.Name as Node, 0 as Outdegree-----------

Insight:After executing the above query,I have found that genes:SNCA and BRCA1 are the influencial amongst all the genes and hence I think these normal functioning of these genes is quite mandatory for the system of the genes to function is the desired manner.

9. create the degree histogram for the network, then Determine how many nodes (genes) are in the graph having a degree of 3.
 
Cypher Query:match (n:TrialGene)-[r]-()
with n, count(distinct r) as degree
set n.deg = degree
return n.Name, n.deg ORDER BY n.deg DESC limit 10

match (n:TrialGene)-[r]-()
where 2 < n.deg AND n.deg < 4 
return count(n),n.Name            ------821


Insight:We have found that there are 821 genes which are having in total 3 associations with other genes which can be both inwards or outwards interactions.



