StreamFlix User Engagement Analysis
Business Scenario
StreamFlix is a popular video streaming platform that offers a vast library of movies and TV shows. To improve content acquisition strategy and enhance user experience, the product team wants to deeply understand user engagement. The platform generates millions of viewing events daily, which are captured and streamed into the AWS ecosystem.
The business needs to analyze key metrics such as:
What is the total watch time per user, per genre, and per device type for the last month?
What are the peak viewing hours on weekdays versus weekends?
Which content has the highest completion rate (i.e., watched from beginning to end)?
What is the average duration of a viewing session?
How do user ratings correlate with content popularity?
Technical Context
Data Source: A Kinesis data stream provides real-time user viewing events in JSON format. Each event represents a significant action (e.g., play, pause, stop, seek, rate).
Data Warehouse: AWS Redshift.
Key Business Entities: Users, Content (Movies, TV Shows, Seasons, Episodes), Devices, Viewing Sessions, and Ratings.
Your Task
Design a data model for the StreamFlix data warehouse that can efficiently store and serve user engagement analytics. The model must handle a high volume of event data and support complex OLAP queries.
Expected Outcomes
1. Conceptual Data Model:
Create a diagram that outlines the key entities and their interactions. It should clearly show how a user interacts with content through viewing sessions on different devices.
2. Logical Data Model (Dimensional Model):
Given the different business processes (viewing and rating), design a Galaxy (or Constellation) Schema with at least two fact tables.
Define your Fact Tables:
fact_viewing_session: This table should capture the complete lifecycle of a single viewing session for a piece of content. Decide if this should be a Transactional or an Accumulating Snapshot fact table and justify your choice. What is the grain? What are the key measures (e.g., watch_duration_seconds, is_completed_flag)?
fact_content_rating: A transactional fact table to store user ratings. What is its grain?
Define your Dimension Tables:
dim_user: Contains user demographic and subscription information.
dim_content: A dimension that must handle the hierarchy of TV Show -> Season -> Episode as well as standalone Movies. How would you model this?
dim_device: Information about the device used for streaming (e.g., TV, mobile, web).
dim_date and dim_time_of_day: To support time-based analysis.
Provide a clear diagram of your galaxy schema, illustrating how dimensions are shared across the fact tables.
3. Physical Data Model:
Write the complete DDL SQL scripts to create your fact and dimension tables in AWS Redshift.
Your DDL should include:
Clear primary and foreign key definitions.
Data types suited for the data (e.g., how would you store the hierarchical content data?).
Redshift-specific optimizations:
Define DISTSTYLE and SORTKEY for your fact tables. Justify your choices based on expected query patterns (e.g., joining on user_id or filtering by date).
Consider the use of compression (ENCODE) for columns with low cardinality or repetitive data.
Explain how your data model would be populated from the raw JSON event stream. You don't need to write the ETL code, but you should describe the logic of how you would transform the stream of play/pause/stop events into a single row in the fact_viewing_session table.
