Ques: ● What are ACID properties and Explain Transactions in Hive

Ans:
# ACID stands for four traits of database transactions: 
Atomicity (an operation either succeeds completely or fails, it does not leave partial data), Consistency (once an application performs an operation the results of that operation are visible to it in every subsequent operation), Isolation (an incomplete operation by one user does not cause unexpected side effects for other users), and Durability (once an operation is complete it will be preserved even in the face of machine or system failure).  These traits have long been expected of database systems as part of their transaction functionality.  
- Up until Hive 0.13, atomicity, consistency, and durability were provided at the partition level.  Isolation could be provided by turning on one of the available locking mechanisms (ZooKeeper or in memory). 
- With the addition of transactions in Hive 0.13 it is now possible to provide full ACID semantics at the row level, so that one application can add rows while another reads from the same partition without interfering with each other.

# Transactions with ACID semantics have been added to Hive to address the following use cases:

*Streaming ingest of data.
- Many users have tools such as Apache Flume, Apache Storm, or Apache Kafka that they use to stream data into their Hadoop cluster.  While these tools can write data at rates of hundreds or more rows per second, Hive can only add partitions every fifteen minutes to an hour.  Adding partitions more often leads quickly to an overwhelming number of partitions in the table.  These tools could stream data into existing partitions, but this would cause readers to get dirty reads (that is, they would see data written after they had started their queries) and leave many small files in their directories that would put pressure on the NameNode.  With this new functionality this use case will be supported while allowing readers to get a consistent view of the data and avoiding too many files.

*Slow changing dimensions.
- In a typical star schema data warehouse, dimensions tables change slowly over time.  For example, a retailer will open new stores, which need to be added to the stores table, or an existing store may change its square footage or some other tracked characteristic.  These changes lead to inserts of individual records or updates of records (depending on the strategy chosen).  Starting with 0.14, Hive is able to support this.

*Data restatement. 
- Sometimes collected data is found to be incorrect and needs correction.  Or the first instance of the data may be an approximation (90% of servers reporting) with the full data provided later.  Or business rules may require that certain transactions be restated due to subsequent transactions (e.g., after making a purchase a customer may purchase a membership and thus be entitled to discount prices, including on the previous purchase).  Or a user may be contractually required to remove their customer’s data upon termination of their relationship.  Starting with Hive 0.14 these use cases can be supported via INSERT, UPDATE, and DELETE.

- Bulk updates using SQL MERGE statement.
