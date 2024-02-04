## Objective:

The main objective of this project was to implement an end-to-end Data Visualization project with a huge dataset and working on its optimization to ensure smooth user experience.

## Workflow:

The dataset used here is Microsoft Contoso Retail Classic. The data has huge number of transactions and has 20+ tables and 16M+ transactions in fact tables. The Dataset first was downloaded and imported into SQL Server using the SQL backup function. The downloaded table was in normalized form and in Snowflake schema.

Next, ETL was prformed using SQL queries for the major transformations. During the ETL, two fact Sales tables were merged into one big table and a single primary key SalesKey was added. Columns which were not relevant for the analysis were removed and truncated such that a consistent fact table can be obtained.

Later the model was passed through Power Query for further transformations where redundant tables & columns were removed. Here, Product Category & Product Subcategoty tables were denormalised by merging them through a common column. The data was next imported in Power BI where Data modeling took place and model was converted to a Star Schemas and various Fact & Dimension were joined forming One-to-Many relationships. Finally, after adding the required DAX measures and Visuals, the analysis was performed.

## Important DAX Formulas:

	Cumulative Gross Margin =
		VAR LastDates =
			MAX(DatesMaster[Date])
		VAR Cumulative = 
			CALCULATE([Net Revenue],
				FILTER(ALL(DatesMaster),
					DatesMaster[Date] < LastDates
				)
			)
		RETURN Cumulative
	
	Moving Sales Total = 
		VAR LastDates = 
			MAX(DatesMaster[Date])
		VAR MovingPeriod = 180
		VAR ReqTable = 
			FILTER(ALL(DatesMaster),
				DatesMaster[Date] <= LastDates
				&&
				DatesMaster[Date] > LastDates - MovingPeriod
			)
		VAR MVT30 = 
			CALCULATE([Total Revenue],
						ReqTable
			)
		RETURN MVT30
	
	QTD Net Revenue =
		VAR LastDates = 
			MAX(DatesMaster[Date])
		VAR CY = 
			YEAR(LastDates)
		VAR CYQ = 
			QUARTER(LastDates)
		VAR FilterTab = 
			FILTER(ALL(DatesMaster),
				DatesMaster[Date] <= LastDates
				&&
				YEAR(DatesMaster[Date]) = CY
				&&
				QUARTER(DatesMaster[Date]) = CYQ
			)
		VAR QTDNR = 
			CALCULATE([Net Revenue],
					FilterTab
			)
		RETURN QTDNR
	
	YTD Net Revenue = 
		VAR LastDates = 
			MAX(DatesMaster[Date])
		VAR CY = 
			YEAR(LastDates)
		VAR ReqTable = 
			FILTER(ALL(DatesMaster),
				DatesMaster[Date] <= LastDates
				&&
				YEAR(DatesMaster[Date]) = CY
			)
		VAR YTD_Net_Rev = CALCULATE([Net Revenue],
								ReqTable
		)
		RETURN YTD_Net_Rev
	
	6-Month Moving Average =
    VAR LastDates = 
        MAX(DatesMaster[Date])
    VAR MovingPeriod = 180
    VAR ReqTable = 
        FILTER(ALL(DatesMaster),
            DatesMaster[Date] <= LastDates
            &&
            DatesMaster[Date] > LastDates - MovingPeriod
        )
    VAR Six_MVA = 
        CALCULATE(AVERAGEX(DatesMaster,
                            [Net Revenue]),
                            ReqTable
        )
    RETURN Six_MVA
	
	Revenue_per_transaction =
		DIVIDE([Net Revenue], COUNTROWS(Sales)) 

## Results:

The result of the analysis have been shown in five sheets:
1. Overall Sales Overview
2. Product Analysis with Drill Down
3. Sales Channel Analysis with Drill Down
4. Quarter-to-Date (QTD) Sales Analysis
5. Year-to-Date (YTD) Sales Analysis


## References:

Contoso File : https://www.microsoft.com/en-us/download/details.aspx?id=18279

DAX: https://www.sqlbi.com/

