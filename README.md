# MSDS 610 Final Project

# Relational Joins

## Objectives

- Overview
- Why do we need Joins? (The problems with Unnormalized tables)
- Different types of Relational Joins
- Triangular Arbitrage

## Overview
Relational Joins are clauses that allow us to link two or more tables in a database schema based on a key/keys that define the relationship between those tables.  

___
## Why do we need Joins? (The problems with Unnormalized tables)

Consider the dataset 'Pets and Owner', which describes various pet owners and their pets. The table includes the owners name and address and a unique identifier called owner_id, and pet information including name, gender, age, type of pet, and a unique identifier called pet_id.

![Big table](https://user-images.githubusercontent.com/20403651/193718099-90171bbf-cbf5-49c2-809a-67f3322bb24e.png)

The problem with one big table is that this table will encompass an owner’s every record, which causes redundant or repeated data (See below table). Now here we have stored every possible detail in one huge table but it comes with a cost. As mentioned we end up storing a lot of redundant/duplicate information. Hence, to solve this problem, we split our data up across multiple different tables, and create relationships between them. The process of splitting up data in this way to remove duplication and improve data integrity is known as **Normalization**.

![redundant](https://user-images.githubusercontent.com/20403651/193720117-ca482106-9f37-4f16-8199-95980c8235cc.png)


Once we have normalized our data (split into multiple tables), we have now solved the problem of the data integrity but have a new issue. We still want to see the data combined together. For example, let's say we were interested in knowing how many pets each owner has. In order to get this answer we need to combine the tables back together. To do this we use JOINS which are clauses to link two or more tables (usually based on the keys that define the relationship between those two tables). In both tables there is an id field called owner_id. We will use this field to join the two tables together. For all the examples in the notebook, we will use pd.merge which is the python-sql equivalent.
 

## Relational Joins under the hood.
The join operation is one of the most powerful and commonly used SQL operations, but little attention is paid to how the internal SQL engine breaks down the tasks of join operations. In this project, we also explore the common algorithms that databases use to compute them, including nested loop, hash, and merge joins.

### Nested Loops Join
It is the simplest form of join, which compares each row from one table (known as the outer table) to each row from the other table (known as the inner table) looking for rows that satisfy the join predicate.

```
for each row R1 in the outer table
    for each row R2 in the inner table
        if R1 joins with R2
          return (R1, R2)
```
The total number of rows compared and, thus, the cost of this algorithm is proportional to the size of the outer table multiplied by the size of the inner table. Since this cost grows quickly as the size of the input tables grows, in practice we try to minimize the cost by reducing the number of inner rows that we must consider for each outer row.

### Merge Join
The merge join works by simultaneously reading and comparing the two sorted inputs one row at a time.  At each step, we compare the next row from each input.  If the rows are equal, we output a joined row and continue.  If the rows are not equal, we discard the lesser of the two inputs and continue.  Since the inputs are sorted, we know that we are discarding a row that is less than any of the remaining rows in either input and, thus, can never join.

```
get first row R1 from input 1
get first row R2 from input 2
while not at the end of either input
  begin
  if R1 joins with R2
    begin
    return (R1, R2)
    get next row R2 from input 2
  end
  else if R1 < R2
    get next row R1 from input 1
  else
    get next row R2 from input 2
end
```

Unlike the nested loop join where the total cost may be proportional to the product of the number of rows in the input tables, with a merge join each table is read at most once and the total cost is proportional to the sum of the number of rows in the inputs.  Thus, merge join is often a better choice for larger inputs.

### Hash Joins
The hash join executes in two phases: build and probe.  During the build phase, it reads all rows from the first input (often called the left or build input), hashes the rows on the equijoin keys, and creates an in-memory hash table.  During the probe phase, it reads all rows from the second input (often called the right or probe input), hashes these rows on the same equijoin keys, and looks or probes for matching rows in the hash table.  Since hash functions can lead to collisions (two different key values that hash to the same value), we typically must check each potential match to ensure that it really joins.

```
for each row R1 in the build table
  begin
  calculate hash value on R1 join key(s)
  insert R1 into the appropriate hash bucket
end

for each row R2 in the probe table
  begin
  calculate hash value on R2 join key(s)
  for each row R1 in the corresponding hash bucket
    if R1 joins with R2
      return (R1, R2)
end
```
Note that unlike the nested loops and merge joins which immediately begin flowing output rows, the hash join is blocking on its build input.  That is, it must completely read and process its entire build input before it can return any rows.  Moreover, unlike the other join methods, the hash join requires a memory grant to store the hash table.


## Triangular Arbitrage

### How to Use this Project
Arbitrage is the process of buying low in one market and selling high in another. Triangular Arbitrage involves extending this two market event to three markets. ``` Arbitrage.ipynb``` identifies triangular arbitrage opportunities in the currency exchange ("forex") market and calculates the top "n" profitable opportunities given a date, an amount of starting funds, and the number of opportunities desired to be returned (n). The primary function in the file takes these inputs as follows.

``` TopN_Arbitrage(date, starting_funds, N)```

The second cell contains the function. The rest of the code breaks down the function line by line for explanatory purposes.


### How to Run this Project
This project requires a Python 3+ installation and following packages. The code was written in Python 3.9.
- pandas
- numpy
 
To ensure this code runs properly make sure the pandas and numpy packages are installed.

```
pip install pandas
```

```
pip install numpy
```

For more details on installation see the pandas and numpy installation pages.

- https://pandas.pydata.org/docs/getting_started/install.html
- https://numpy.org/install/

This project assumes the user has the ```RprtRateXchg_20170930_20220930.csv``` file downloaded into your current working directory for Python. If this is not the case, pandas will throw an error. To ensure this does not happen specify the proper pathing in any ```pd.csv_read("your file location")``` statement. In addition it is possible to use this project as starter code for use on your own data. To do so, alter the read csv statement to ```pd.read_csv("your new file")```.

In this way this project can be used on any number of different datasets as needed. **Keep in mind that each dataset is different and may require various data cleansing, preparation, or techniques not covered in this project. In addition where the purpose of this project is to show potential profit opportunities, it in no way functions as investment advice.**

___

## Credits
This project was an equal partnership between Harsh Praharaj and Adam Ansari as part of an assignment for MSDS 610 at the University of San Francisco.

- Harsh Praharaj https://github.com/HarshPraharaj
- Adam Ansari https://github.com/Adam-Ansari-USF

The following websites were used as references:
- Dataset https://fiscaldata.treasury.gov/datasets/treasury-reporting-rates-exchange/treasury-reporting-rates-of-exchange
- Triangular Arbitrage Definitions https://penpoin.com/triangular-arbitration/
