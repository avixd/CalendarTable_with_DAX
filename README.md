# Calendar Table with DAX
## Code samples of a calendar table using DAX

### Refer to [this youtube video](https://youtu.be/NGUKMTdn27U) with a walk through of the code below

```
Sample Calendar Table= 
VAR _startdate =
    DATE ( 2022, 1, 1 )

VAR _enddate =
    DATE ( 2024, 1, 1 )

VAR _output =
    ADDCOLUMNS (
        ADDCOLUMNS (
            ADDCOLUMNS (
                CALENDAR (
                    _startdate,
                    _enddate
                ),
                "Year", YEAR ( [Date] ),
                "Month", MONTH ( [Date] ),
                "MonthName",
                    FORMAT (
                        MONTH ( [Date] ) * 29,
                        "MMM"
                    ),
                "MonthNameLong",
                    FORMAT (
                        MONTH ( [Date] ) * 29,
                        "MMMM"
                    ),
                "Day", DAY ( [Date] ),
                "Week", WEEKDAY ( [Date] )
            ),
            "QuarterNumber",
                ROUNDUP (
                    [Month] / 3,
                    0
                )
        ),
        "Quarter",
            "Q" & [QuarterNumber]
    )
RETURN
    _output
```

### An enhanced Calendar Table using DAX with the following additions are below:
1. Custom Fiscal Year Index Month Parameter
2. Fiscal Year, Fiscal Quarter and Fiscal Month
3. Past/Present/Future Flags
4. Weekday/Weekend Flag
5. Dy Name, Day of the Year etc.

```
Enhanced Calendar Table = 

// calendar table creation 

VAR _startdate =
    DATE ( 2023, 1, 1 ) // Start Date
VAR _enddate =
    DATE ( 2025, 1, 1 ) // End Date
VAR _fiscalmonthnumber = 4 /* Index Month.  Assuming FY is 1 April - 31 March, the index month =4 .Change the index month based on the FY start month*/
// calendar table creation 
VAR _calendartable =
    ADDCOLUMNS (
        ADDCOLUMNS (
            ADDCOLUMNS (
                CALENDAR ( _startdate, _enddate ),
                "CalendarYear", YEAR ( [Date] ),
                "CalendarMonthNumber", MONTH ( [Date] ),
                "CalendarMonthNameLong", FORMAT ( MONTH ( [Date] ) * 29, "MMMM" ),
                "CalendarMonthName", FORMAT ( MONTH ( [Date] ) * 29, "MMM" ),
                "CalendarMonthYearNumber",
                    YEAR ( [Date] ) * 100
                        + MONTH ( [Date] ),
                "FiscalYear",
                    IF (
                        MONTH ( [Date] ) < _fiscalmonthnumber /* Index Month*/
                        ,
                        // Assuming FY is 1 April - 31 March , change the index month based on the FY start month
                        YEAR ( [Date] ),
                        YEAR ( [Date] ) + 1
                    ),
                "FiscalMonthNumber",
                    SWITCH (
                        TRUE (),
                        MONTH ( [Date] ) = 4, 1,
                        //April
                        MONTH ( [Date] ) = 5, 2,
                        //May
                        MONTH ( [Date] ) = 6, 3,
                        //June
                        MONTH ( [Date] ) = 7, 4,
                        //July
                        MONTH ( [Date] ) = 8, 5,
                        //August
                        MONTH ( [Date] ) = 9, 6,
                        //September
                        MONTH ( [Date] ) = 10, 7,
                        //October
                        MONTH ( [Date] ) = 11, 8,
                        //November
                        MONTH ( [Date] ) = 12, 9,
                        //December
                        MONTH ( [Date] ) = 1, 10,
                        //January
                        MONTH ( [Date] ) = 2, 11,
                        //February
                        MONTH ( [Date] ) = 3, 12 //March
                    ),
                "Past/Present/Future Flag",
                    IF ( [Date] < TODAY (), "Past", IF ( [Date] = TODAY (), "Present", "Future" ) )
            ),
            "CalendarQuarterNumber", ROUNDUP ( [CalendarMonthNumber] / 3, 0 ),
            "FiscalQuarterNumber", ROUNDUP ( [FiscalMonthNumber] / 3, 0 ),
            "WeekdayNumber", WEEKDAY ( [Date] ),
            // Sunday = 1, Saturday = 7
            "Weekday", FORMAT ( WEEKDAY ( [Date] ), "DDDD" ),
            "WeekNumber", WEEKNUM ( [Date] ),
            "Week", "W" & WEEKNUM ( [Date] )
        ),
        "CalendarQuarter", "Q" & [CalendarQuarterNumber],
        "FiscalQuarter", "FQ" & [FiscalQuarterNumber],
        "Weekday/Weekend",
            IF ( OR ( [WeekdayNumber] = 1, [WeekdayNumber] = 7 ), "Week End", "Week Day" )
    )
RETURN
    _calendartable
```
