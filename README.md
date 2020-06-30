# Crokage
Crowd Knowledge Answer Generator. Feel free to try our [tool](http://isel.ufu.br:9000/). For more details about the technique, please check our original [paper](https://dl.acm.org/citation.cfm?id=3339130). This project is an extension of the original [project](https://github.com/muldon/CROKAGE-replication-package).

## About

### Goal
To provide comprehensive solutions for daily programming tasks containing code examples and succinct explanations (limited to Java in this initial vesion). 

### Input / Output
- Input: API related query in natural language.
- Output: Code examples containing explanations.  

### How
CROKAGE receives as input a query written in natural language and uses state-of-art text retrieval models combined with three state-of-art API recommender tools to retrieve the most related Stack Overflow answers to that query, sorted by relevance. CROKAGE then uses natural language processing to extract the code and relevant sentences to compose a summary containing the solution for the query.  

### Features and comparison with other state-of-art works (AnswerBot and BIKER)
- [AnswerBot](https://dl.acm.org/citation.cfm?id=3155650) is limited as it does not provide code.
- [BIKER](https://dl.acm.org/citation.cfm?id=3238191) is limited as its documentation is limited to JAVA SE and does not provide code for every query.
- [CROKAGE](https://dl.acm.org/citation.cfm?id=3339130) address both limitations by providing relevant code and explanations in form of summaries.

## Prerequisites

Note: all the experiments were conducted over a server equipped with 86 GB RAM, 3.1 GHz on 12 cores and 64-bit Linux Mint Cinnamon operating system. We strongly recommend a similar or better hardware environment. The operating system however could be changed. 

Softwares:
1. [Java 1.8] 
2. [Postgres 9.4] - Configure your DB to accept local connections. An example of *pg_hba.conf* configuration:

```
...
# TYPE  DATABASE        USER            ADDRESS                 METHOD
# "local" is for Unix domain socket connections only
local   all             all                                     md5
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
...
```
3. [PgAdmin] (we used PgAdmin 4) but feel free to use any DB tool for PostgreSQL. 


## Configuring the dataset
1. Download the SO Dump of March 2019 [here](http://lascam.facom.ufu.br/companion/crokageemse/StackOverflow2019-emse-with-bert.backup). This is a preprocessed dump, downloaded from the [official web site](https://archive.org/details/stackexchange)  containing the main tables we use. We only consider Java posts. **Postsmin** table (representing **posts** table) has extra columns with the preprocessed data used by Crokage. 

2. On your DB tool, create a new database named **stackoverflow2019emse-min**. This is a query example:
```
CREATE DATABASE stackoverflow2019emse-min
  WITH OWNER = postgres
       ENCODING = 'UTF8'
       TABLESPACE = pg_default
       LC_COLLATE = 'en_US.UTF-8'
       LC_CTYPE = 'en_US.UTF-8'
       CONNECTION LIMIT = -1;
```
3. Restore the downloaded dump to the created database. 

Obs: restoring this dump would require at least 10 Gb of free space. If your operating system runs in a partition with insufficient free space, create a tablespace pointing to a larger partition and associate the database to it by replacing the "TABLESPACE" value to the new tablespace name: `TABLESPACE = tablespacename`. 

4. Assert the database is sound. Execute the following SQL command: `select id, title,body,processedtitle,processedbody,code, processedcode from postsmin po limit 10`. The return should list the main fields for 10 posts. 



## Running Crokage.

## Download auxiliary files

### 1- Make a home dir, for example /home/user/crokage

### 2- Git clone this project inside the home dir:
```
git clone https://github.com/muldon/crokage-emse-replication-package.git
```
In the end, you will have the structure: ``/home/user/crokage/crokage-emse-replication-package`` 

### 3- Download the jar
Download our fat jar [here](http://lascam.facom.ufu.br/companion/crokageemse/crokage.jar). Place it along with the downloaded files (``/home/user/crokage/crokage-emse-replication-package``).

### 4 - Check your instalation 
Make sure your crokage folder (``/home/user/crokage/crokage-emse-replication-package``) must contain this structure:

```.
..
./data 
crokage.jar
main.properties   (not "main.properties.txt") 
...
```
Obs: if for some reason you opt to zip and download, make sure the extracted file ``main.properties`` does not change to ``main.properties.txt``. 

Obs 2: for now we only provide the replication package containing the files for the reproduction of CROKAGE, along with the results of the User Study, described in our paper. The complete source code will be released soon. 



### Setting Parameters

Edit `main.properties` and set the **######### Must be set** following parameters: 

`CROKAGE_HOME` = the root folder of the project (ex ``/home/user/crokage/crokage-emse-replication-package``).

`spring.datasource.username` = your db user

`spring.datasource.password=` = your db password

`spring.datasource.url=` your database URL, as for example: `jdbc:postgresql://localhost:5432/stackoverflow2019emse-min`. 



### Running the jar 
Open a terminal, go to the folder where the jar file and main.properties are located and run the following command: `java -Xms1024M -Xmx50g -jar crokage.jar --spring.config.location=./main.properties` . This command use the file `main.properties` to overwrite the default parameters which must be set as described above.


### Results

The results are displayed in the terminal/console but also stored in the database in tables **metricsresults**. The following query should return the results:  
```
select * from metricsresults
```



## Tool
We implemented our approach in form of a [tool](http://isel.ufu.br:9000/) to assist developers with their daily programming issues. The figure below shows the tool architecture. We follow a REST (Representational State Transfer) architecture. The tool is in beta version and only provide solutions for Java language, but we expect to release the full version soon. If you wish to use our tool to provide solutions to your natural language queries, please follow the instructions [here](https://github.com/muldon/CROKAGE-replication-package#user-content-running-the-tool-mode-2---obtaining-the-solutions-via-rest-interface).

![CROKAGE's architecture](https://github.com/muldon/CROKAGE-replication-package/blob/master/tool-architecture.png)



## Citation

If you intend to use this work, please cite us:

```
@inproceedings{silva2019recommending,
  title={Recommending comprehensive solutions for programming tasks by mining crowd knowledge},
  author={Silva, Rodrigo FG and Roy, Chanchal K and Rahman, Mohammad Masudur and Schneider, Kevin A and Paixao, Klerisson and de Almeida Maia, Marcelo},
  booktitle={Proceedings of the 27th International Conference on Program Comprehension},
  pages={358--368},
  year={2019},
  organization={IEEE Press}
}
```



## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE.txt) file for details


[Java 1.8]: http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html
[Postgres 9.4]: https://www.postgresql.org/download/
[PgAdmin]: https://www.pgadmin.org/download/



