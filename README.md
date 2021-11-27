# EcoTourismDatadrivenDisicionmaking
Perform EDA and Data Analysis to Drop one Trip for future schedule

-- Number of people by fitness level

SELECT FitnessLevel.Description, Sum(Registration.NumberOfPeople) AS [Number of  People by Fitness Level]
FROM (FitnessLevel INNER JOIN Trip ON FitnessLevel.FitnessLevelNo = Trip.FitnessLevelNo) INNER JOIN (Customer INNER JOIN Registration ON Customer.CustomerNo = Registration.CustomerNo) ON Trip.TripNo = Registration.TripNo
GROUP BY FitnessLevel.Description;

--6 least revenue generating Trips

SELECT TOP 6 Trip.Destination, Sum(Registration.NumberOfPeople) AS [Total Number Of People], Sum([numberofpeople]*[price]) AS Revenue
FROM Trip INNER JOIN Registration ON Trip.TripNo = Registration.TripNo
GROUP BY Trip.Destination
ORDER BY Sum([numberofpeople]*[price]);

--View Cube Data

SELECT Registration.RegistrationNo, Registration.CustomerNo, [LastName] & ", " & [FirstName] AS FullName, Customer.State, Registration.TripNo, Trip.Destination, Year([StartDate]) AS YY, Month([StartDate]) AS MM, MonthName(Month([StartDate])) AS [Month], [EndDate]-[StartDate] AS Duration, Trip.Price, FitnessLevel.Description, Registration.NumberOfPeople, [NumberOfPeople]*[Price] AS Revenue, IIf([StartDate]<Date(),"Past","Upcoming") AS Timing
FROM (FitnessLevel INNER JOIN Trip ON FitnessLevel.FitnessLevelNo = Trip.FitnessLevelNo) INNER JOIN (Customer INNER JOIN Registration ON Customer.CustomerNo = Registration.CustomerNo) ON Trip.TripNo = Registration.TripNo;


---Cross tab Revenue by Years

TRANSFORM Sum(viewCube.Revenue) AS SumOfRevenue
SELECT viewCube.Destination, Sum(viewCube.Revenue) AS [Total Revenue]
FROM viewCube
GROUP BY viewCube.Destination
PIVOT viewCube.YY;

-- Number of Trips for each Destination

SELECT Trip.Destination, Count(Trip.TripNo) AS [Number of Trips]
FROM Trip
GROUP BY Trip.Destination
ORDER BY Count(Trip.TripNo) DESC;

-- Average Revenue by Destination

SELECT CrossTab_RevenuebyYears.Destination, [Total Revenue]/[Number of Trips] AS [Avergae Revenue per Destination]
FROM NumberOfTripsforEachDestination INNER JOIN CrossTab_RevenuebyYears ON NumberOfTripsforEachDestination.Destination = CrossTab_RevenuebyYears.Destination;

--Revenue in the year of 2020

SELECT Year([startdate]) AS [Year], Trip.Destination, Sum([NumberOfPeople]*[Price]) AS Revenue, Sum(Registration.NumberOfPeople) AS [Numbe Of People]
FROM Trip INNER JOIN Registration ON Trip.TripNo = Registration.TripNo
GROUP BY Year([startdate]), Trip.Destination
HAVING (((Year([startdate]))=2020))
ORDER BY Sum([NumberOfPeople]*[Price]) DESC;





