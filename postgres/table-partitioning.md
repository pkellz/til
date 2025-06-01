# Table Partitioning

You foresee one of your Postgres tables becoming unwieldy due to it's size (say >100 million rows). One potential scaling optimization is to partition your table into several smaller tables.

Your (very popular) social media app has a 'posts' table. Your users create >1 million posts each month. A few things you should consider at this scale is:

1) Every write to the 'posts' table requires indexes to be rebuilt for the ***entire*** table (assuming you're not using partial indexes). At >100 million rows you can imagine this leads to slow & cumbersome writes!

2) Considering the nature of the application &mdash; social media, users will generally prefer seeing the *latest* posts anyway. 

You could create your 'posts' table such that it is divided / "partitioned" based on the date each post was created:
```SQL
-- Creates the main 'posts' table which is partitioned by the 'created_at' field.
-- Posts with the same 'created_at' date live in the same partition.
-- This table contains *ALL* posts but Postgres keeps track of where the divisions / partitions should be.

CREATE TABLE posts (
    id SERIAL,
    user_id INT,
    content TEXT,
    created_at TIMESTAMP
) PARTITION BY RANGE (created_at)
```

So now if you wanted to create a brand new table that only contained posts from May 2025, you could do:
```SQL
-- Creates a 'posts_2025_05' table (a subset of the original 'posts' table)
-- This table ONLY contains posts with a 'created_at' date in May 2025 

CREATE TABLE posts_2025_05 PARTITION OF posts 
    FOR VALUE FROM ('2025-05-01') TO ('2025-06-01')
```

Now we have a separate (and much smaller) table to work with that only contains posts for a certain time period.

### Benefits
- Faster writes &mdash; writes to this smaller table only require rebuilding indexes for this table instead of for ***every*** post.

- Scalable writes &mdash; different database sessions can write to different partitions simultaneously.

- Going back to the fact that users generally care more about recent posts, we can store the latest partitions of posts in (faster) warmer storage while older partitions can live in (less expensive) cold storage.

- Easier data-loading &mdash; Partitioning your data might make it less cumbersome to do bulk data loading operations. You can load 1 partition in at a time instead of 1 gigantic operation.