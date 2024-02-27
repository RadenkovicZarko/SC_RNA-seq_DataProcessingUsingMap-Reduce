# SC RNA-seq Data Processing using Map/Reduce
## Introduction
Gene expression can be measured by quantifying RNA molecules in a tissue sample that originate from the transcript of that gene. (More information on this topic will be covered in the "Introduction to Bioinformatics" course next semester; for the purposes of this course, it's sufficient to view expressions as matrices of numbers.)

Single-cell RNA sequencing (SC RNA-seq) technology enables us to measure gene expressions at the level of individual cells in a sample. Thus, the data we work with is a matrix where rows represent different genes, columns represent different cells from the sample, and the values represent the expression of a given gene in a given cell.

In this project, you will implement standard steps in processing SC RNA-seq data using the map/reduce approach.

A common way to think about SC RNA-seq data is to view it as data about a set of cells, where each cell is a (multidimensional) point represented by a vector of gene expressions measured for that cell.

We often want to find groups (clusters) of cells, where cells with similar expressions are classified into the same group. It's often useful to visualize the data in 2D (to see the groups), using dimensionality reduction techniques such as TSNE or UMAP. The details of these methods go beyond the scope of this course, hence the project also includes a UMAP of the same dataset we are processing.

## Task Description
You are given a matrix of genetic expressions obtained by SC RNA-seq technology. You need to write code that performs the following processing:

Load the matrix in a suitable format for further processing with the following code:
```
import pandas as pd

df = pd.read_table('expressions.tsv', index_col=0)
data = [(cell, gene, value) for cell in df.columns for gene, value in df[cell].iteritems()]
Along with the matrix of values, a UMAP matrix for each cell is provided. You can load and plot the UMAP with the following code:
```
```
import matplotlib.pyplot as plt
import seaborn as sn

embedding = pd.read_table('umap.tsv')
embedding['cluster'] = 0
plt.figure(figsize=(8, 6))
plt.scatter(
    embedding.umap1,
    embedding.umap2,
    c=[sn.color_palette()[x] for x in embedding.cluster]
)
```
During the task, you will need to determine the clusters to which the cells belong, hence the line:

```
embedding['cluster'] = 0
```
Will be replaced by a line that inputs the calculated clusters. For example, if we have a map in the form clusters = {'cell_id': 'cluster_id'}:


```
embedding['cluster'] = embedding.cell.map(clusters)
```

## 1. Within-Cell Normalization
1.1. Use the reduce function to determine the average value of gene expressions within each cell. The result should be an array of tuples (cell, average-value). You can modify the last element of the result after the reduce function call.
1.2. Use the map function to perform centering of expression values around the mean value within the given cell.
1.3. Use the reduce function to determine the variance of expressions within each cell, starting from the results of the previous step.
1.4. Use the map function to determine the standard deviation of expressions within each cell, based on the array of variances from point 1.3.
1.5. Use the map function to perform standardization of expression values within each cell, based on the array from point 1.2.

## 2. Normalization of Gene Expression Values
2.1. Use the map and reduce functions to determine the variance of expressions for each gene.
2.2. Use the reduce function to determine the set of 500 most variable genes.
2.3. Use the reduce function to filter the array from point 1.5 so that it contains only genes found in the 500 most variable genes.
2.4. Sort the array created in task 2.3 so that it is lexicographically sorted by the name of the gene.
2.5. Use the reduce function to perform rank normalization of values within one gene.
2.6. Use the map function to discard the original values (pre-rank normalization) from the array from point 2.5.

## 3. K-means Clustering
3.1. Use the reduce function to group values from the array from point 2.5 by cell.
3.2. ...

## Definitions of Operations
Centering the values of expressions around the mean value of the array: subtracting the mean value of the array from all its elements.
Standard
