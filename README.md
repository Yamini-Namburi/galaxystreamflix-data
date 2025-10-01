Conceptual Data Model

A User has many Viewing Sessions.
A Device has many Viewing Sessions.
Content is watched in many Viewing Sessions.
A Viewing Session contains many Events.
A User gives many Ratings; Content receives many Ratings.

Logical Data Model

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
