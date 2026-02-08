TABLE 
    day_of_week AS "Day", 
    market_profile AS "Profile", 
    news_event AS "News",
    standard_deviation AS "SD"
FROM "Journal/Daily"
WHERE market_profile != null
SORT date DESC