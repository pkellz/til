# Scaling Reads

One strategy for scaling Postgres reads is to use replication.

A "replica" is a database instance that mirrors another (primary) instance such that:

1) ***all database writes are directed to the 'primary' only*** and,
2) the primary will broadcast the changes it just received to every replica so that all replicas can stay up to date.

Most applications are going to be more read-heavy than write-heavy so having a single instance where all data is written but multiple instances where data is read enables higher read throughput and greater availability in the event that one of your database instances goes down.

![image](https://github.com/user-attachments/assets/62daf04f-21ca-4a9f-bca2-8768bd389fe6)

