# Spark Joins Hands-On

A local, hands-on walkthrough of how Spark actually executes a join — broadcast
vs. shuffle (sort-merge), which join types broadcast can and can't support,
why unmatched rows behave the way they do, and what skew does to a sort-merge
join. All of it runs on a laptop, no cluster required.


## Where the actual lesson is

Everything else — cluster sizing, broadcast vs. shuffle joins, which join
types broadcast supports, the outer-join gotcha, the datasets, and production
best practices — lives in the notebook, not here:

**[`scripts/workshop.ipynb`](spark_joins_hands_on/workshop.ipynb)**

Work through it top to bottom in order; later cells depend on the
`SparkSession` and datasets created earlier in the notebook.


