TABLE 
    day_of_week AS "Day",
    midnight_open AS "Midnight", 
    adr_high AS "ADR High", 
    adr_low AS "ADR Low", 
    rolling_eq AS "EQ",
    rolling_adr AS "ADR"
FROM "Journal/Daily"
WHERE midnight_open != null
SORT date DESC
LIMIT 5

