Conceptual Data Model :


A User has many Viewing Sessions.

A Device has many Viewing Sessions.

Content is watched in many Viewing Sessions.

A Viewing Session contains many Events.

A User gives many Ratings; Content receives many Ratings.


Logical Data Model :


Two business processes drive a galaxy schema: viewing and rating.

fact_viewing_session is an accumulating snapshot; grain = one row per session.

It stores watch_duration_seconds, number_of_pauses/seeks, buffering_seconds, percent_completed, is_completed_flag, plus start/end timestamps.

Start/end date and time-of-day foreign keys enable weekday/weekend and peak-hour analysis.

fact_content_rating is transactional; grain = one row per rating action (user, content, timestamp).

Conformed dimensions shared by both facts: dim_user, dim_content, dim_device, dim_date, dim_time_of_day.

dim_user is SCD2, tracking subscription/region history with effective_start/end and is_current.

dim_content is SCD2, unifying movies + TV with hierarchy fields (show → season → episode), runtime, and genres.

dim_device is SCD1 (latest device type/OS/app_version); dim_date/time are small, static reference tables.

This constellation answers KPIs via simple aggregates/joins on the facts, avoiding scans over raw events.



Physical Data Model:



DISTSTYLE (how data is distributed across nodes)


dim_user + fact_viewing_session colocated on user_key

- Means both tables are stored on the same Redshift slices by user_key. So when you query “watch time by user,” Redshift doesn’t have to move data around = faster joins.

dim_content + fact_content_rating colocated on content_key
- Same logic: ratings usually join with content, so we keep them on the same slices = fast lookups for popularity/ratings.

Small dimensions (date, time, device) → DISTSTYLE ALL
- These are tiny tables, so Redshift makes a copy on every node. Every join is local and cheap.


SORTKEY (how rows are ordered on disk)

fact_viewing_session → sorted by session_start_ts
- Most queries filter by date/time (e.g., “last month’s watch sessions”). Sorting by start timestamp means Redshift only scans the blocks it needs = less data to read, faster queries.

fact_content_rating → sorted by rating_date_key
- Ratings are also analyzed by time (e.g., “ratings last quarter”), so sorting by date gives the same range-scan speedup.

Dimensions sorted by their natural keys
- e.g., user_id, content_id. This just makes joins and lookups cleaner and more efficient.


ENCODE (compression for storage and speed)

AZ64 for numeric/timestamp
- Optimized format for numbers and dates; uses less space and is quick for aggregations.

BYTEDICT for low-cardinality attributes
- Great for columns with only a few possible values (e.g., genre, device type, is_weekend). Redshift stores them like a dictionary = very small.

ZSTD for text fields
- Best general-purpose compression for long text like titles or emails.

