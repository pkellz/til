# Partial Indexes in Postgres

PostgreSQL supports the concept of partial indexes - *indexes that only span a subset of rows*. Say your university has a `users` table that contains "student" users and "faculty" users.

If the vast majority of queries are for finding "faculty" users you could create an index that only indexes users with type 'faculty'. That way you have lower index overhead because you're not including information you don't typically care about - that is, regular "student" users.

```SQL
-- Standard index that indexes both students and faculty rows
CREATE INDEX idx_users_email ON users(email)

-- Only indexes rows for users of type 'faculty'
CREATE INDEX idx_faculty_users ON users(email) WHERE type = 'faculty'
```