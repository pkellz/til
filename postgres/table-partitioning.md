# Table Partitioning

You foresee one of your Postgres tables becoming unwieldy due to it's size (say >100 million rows). One potential scaling optimization is to partition your table into several smaller tables.

Say you have a 'posts' table for a very popular social media app. Your users create >1 million posts every month. A few things you need to consider at this scale is:

1) Every write to the 'posts' table requires indexes to be rebuilt for the entire table (assuming you're not using partial indexes). Considering >100 million rows you can imagine this will cause writes to be cumbersome!

2) Considering the nature of the application (social media), users will typically prefer seeing the *latest* posts anyway. 

So you could create a partition-able 'posts' table and partition it by the month each post was created:
```SQL
-- The main 'posts' table which is partitioned by the 'created_at' field
-- This table contains ALL posts but Postgres keeps track of where the divisions / 'partitions' should be.
CREATE TABLE posts (
    id SERIAL,
    user_id INT,
    content TEXT,
    created_at TIMESTAMP
) PARTITION BY RANGE (created_at)
```

And if we wanted to create a brand new table that only contained posts from May 2025, we could do:
```SQL
/* Creates a 'posts_2025_05' table that contains posts from the original 'posts'
 table ONLY IF the 'created_at' date is sometime in May 2025 */
CREATE TABLE posts_2025_05 PARTITION OF posts 
    FOR VALUE FROM ('2025-05-01') TO ('2025-06-01')
```

Now we have a separate (and much smaller!) table to work with that only contains posts for a certain time period.

### What did we gain?
- One upside is that writes to the new table only requires rebuilding the indexes for this new table.

- Also, our writes are now more scalable since different database sessions can write to different partitions simultaneously.

- Going back to the fact that users generally care about the latest posts, we can store the latest partitions of posts in (faster) warmer storage while older partitions can live in (less expensive) cold storage.

- Partitioning your data might make it less cumbersome to do bulk data loading operations since you can load 1 partition in at a time.