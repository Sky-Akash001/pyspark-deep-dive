# PySpark Interview Notes

## 1. Why does groupBy cause shuffle?

groupBy requires records with the same key to be brought together.
Therefore Spark may redistribute data across partitions, creating a shuffle.

## 2. What is a broadcast join?

A small dataset is broadcast to executors so Spark can avoid
shuffling the large dataset.

Example:

broadcast(products)

Useful when one side of the join is sufficiently small.

## 3. What is data skew?

Data skew occurs when some partitions contain significantly more data
than others.

This can create straggler tasks.

## 4. What is salting?

Salting distributes records belonging to a heavily skewed key across
multiple artificial keys.

Typical approach:

original_key + random_salt

The salt must be handled on the other side of the join appropriately.

## 5. Why use window functions?

Window functions perform calculations across related rows without
collapsing them into a single row.

Examples:

- row_number()
- rank()
- lag()
- lead()
- running totals

## 6. Why Delta Lake?

Delta provides capabilities such as:

- ACID transactions
- Schema enforcement
- MERGE
- UPDATE / DELETE
- Time travel

while using Parquet-based data storage.

## 7. What is incremental processing?

Instead of processing the entire dataset repeatedly, process only
new or changed records using a watermark, CDC position, timestamp,
or another incremental key.

## 8. What is idempotency?

A pipeline is idempotent when processing the same input multiple times
does not create an incorrect additional result.

Delta MERGE is commonly used to achieve this for upsert workloads.

## 9. Why deduplicate before MERGE?

If multiple source records match the same target key, MERGE can become
ambiguous.

Therefore source records are often deduplicated first, commonly using:

row_number() over partitionBy(business_key)
orderBy(updated_at desc)

## 10. What does collect() do?

collect() brings all resulting rows to the driver.

It should not be used on large datasets.

It is acceptable for very small aggregated results.