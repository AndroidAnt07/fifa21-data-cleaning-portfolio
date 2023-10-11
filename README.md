# fifa21-data-cleaning-portfolio


  --Getting rid of new line characters in Hits column
  select *
  from PortfolioProject..fifa21

  select hits, trim(hits) as Hits_Trimmed
  from PortfolioProject..fifa21


  --Changing to see if Weight and Height column have approiate data types
  
  select weight
  from PortfolioProject..fifa21

 select weight, Cast(SUBSTRING(weight,1,3)as int) as Weight_In_Lbs
  from PortfolioProject..fifa21
  group by Weight 
  order by Weight

  select height, Replace(Replace(height,'''','.'),'"',' ') as HeightConverted
  from PortfolioProject..fifa21

 ##With the above query the goal was too turn the weight and height columns into the appropriate data type. Everything worked out fine except the height. I was able to turn the height into decimal values but i couldnt figure out out to change the data type property. Whenever I tried to cast the height column as a float it was always off by 1. So below this line of text are different things I tried but still couldnt get the desired result##

 select height, HeightConverted --(CAST(SUBSTRING(height, 1, CHARINDEX('''',height)-1) As float) +
				--CAST(SUBSTRING(height, CHARINDEX('''', height) + 1, LEN(height)- CHARINDEX('''', height)-2) as float)
 from PortfolioProject..fifa21
 
 Alter Table portfolioproject..fifa21
 Add HeightConvertedd float

 UPDATE portfolioproject.dbo.fifa21
 SET HeightConvertedd = CAST(REPLACE(REPLACE(height, '`', '.'), '"','')AS float)


 Alter Table fifa21
 ADD newheight FLOAT;


 --Cleaning the value, wage and release clause columns

 --Value Column
 
 Select 
	Case
	When Value Like '%m' THEN
		REPLACE(Value, 'm', '00000')
	When value like '%k' Then 
		Replace(Value, 'k', '000')
	When value like '%â%' or value like '%‚%' or value like '%¬%' or value like '%.%' Then
		Replace(Replace(Replace(Replace(Value, 'â', ' '), '‚', ' '), '¬', ' '), '.', ' ')
		else Value
		end as Transformed_Value
	from PortfolioProject.dbo.fifa21

	Alter Table portfolioproject.dbo.fifa21
	Add Transformed_Value varchar(255)
	
	Update PortfolioProject..fifa21
	Set Transformed_Value = 
	Case
	When Value Like '%m' THEN
		REPLACE(Value, 'm', '00000')
	When value like '%k' Then 
		Replace(Value, 'k', '000')
	When value like '%â%' or value like '%‚%' or value like '%¬%' or value like '%.%' Then
		Replace(Replace(Replace(Replace(Value, 'â', ' '), '‚', ' '), '¬', ' '), '.', ' ')
		else Value
		end
	from PortfolioProject.dbo.fifa21

	select Value, Replace(Replace(REPLACE(REPLACE(Transformed_Value, 'â‚¬',''), 'k', ''), 'm', ''), '.','') as Transformed_Value
 from PortfolioProject..fifa21;


 --Wage Column--

 Select wage
 from PortfolioProject..fifa21

 select wage, replace(REPLACE(REPLACE(wage, 'â‚¬',''), 'k', ''), 'm', '') *1000 AS CleanedWage 
 from PortfolioProject..fifa21

 Alter Table portfolioproject..fifa21
 Add Transformed_Wage varchar(255)

 Update PortfolioProject..fifa21
 Set Transformed_Wage = replace(REPLACE(REPLACE(wage, 'â‚¬',''), 'k', ''), 'm', '') *1000 
 from PortfolioProject..fifa21

 Select wage, transformed_wage
 from PortfolioProject..fifa21

--Release Clause

 Select [Release Clause]
 from PortfolioProject..fifa21

  Select 
	Case
	When [Release Clause] Like '%m' THEN
		REPLACE([Release Clause], 'm', '00000')
	When [Release Clause] like '%k' Then 
		Replace([Release Clause], 'k', '000')
	When [Release Clause] like '%â%' or [Release Clause] like '%‚%' or [Release Clause] like '%¬%' or [Release Clause] like '%.%' Then
		Replace(Replace(Replace(Replace([Release Clause], 'â', ' '), '‚', ' '), '¬', ' '), '.', ' ')
		else [Release Clause]
		end as Transformed_Release_Clause
	from PortfolioProject.dbo.fifa21

	Alter Table portfolioproject..fifa21
	Add Transformed_Release_Clause nvarchar(255)

	Update PortfolioProject..fifa21
	Set Transformed_Release_Clause =
	Case
	When [Release Clause] Like '%m' THEN
		REPLACE([Release Clause], 'm', '00000')
	When [Release Clause] like '%k' Then 
		Replace([Release Clause], 'k', '000')
	When [Release Clause] like '%â%' or [Release Clause] like '%‚%' or [Release Clause] like '%¬%' or [Release Clause] like '%.%' Then
		Replace(Replace(Replace(Replace([Release Clause], 'â', ' '), '‚', ' '), '¬', ' '), '.', ' ')
		else [Release Clause]
		end
	from PortfolioProject.dbo.fifa21

	select [Release Clause],Replace(Replace(REPLACE(REPLACE(Transformed_Release_Clause, 'â‚¬',''), 'k', ''), 'm', ''), '.','') CleanedReleaseCause
 from PortfolioProject..fifa21



	--Seperating Joined Column into year, month, and day




	select joined, 
	datepart(year, joined) as year,
	datename(month, joined) as month,
	datepart(day, joined) as day
	from PortfolioProject..fifa21




	--Seperating team & contract into seperate columns
	


	select [Team & Contract]
	from PortfolioProject..fifa21

	select [Team & Contract], 
		Left([Team & Contract],LEN([Team & Contract])-
	CHARINDEX(' ',
	Reverse([Team & Contract]))-1) as TeamName,
		Right([Team & Contract],(CHARINDEX(' ',
	Reverse([Team & Contract]))+7)) as YearRange
	from PortfolioProject..fifa21

	Alter Table portfolioproject.dbo.fifa21
	add TeamName varchar(255)

	Update PortfolioProject..fifa21
	set TeamName = Left([Team & Contract],LEN([Team & Contract])-
	CHARINDEX(' ',
	Reverse([Team & Contract]))-1)



	select TeamName
	from PortfolioProject..fifa21

	Update PortfolioProject..fifa21
	set TeamName = Cast(LEFT(TeamName,Len(TeamName)-CHARINDEX(' ',
	REVERSE(TeamName)-3))As varchar)
	
	select
	LEFT(TeamName,Len(TeamName)-CHARINDEX(' ',
	REVERSE(TeamName))-3) as cleaned
	from PortfolioProject..fifa21


	Alter Table portfolioproject.dbo.fifa21
	add YearRange varchar(255)

	update PortfolioProject.dbo.fifa21
	set YearRange = Right([Team & Contract],(CHARINDEX(' ',
	Reverse([Team & Contract]))+7))

	select yearrange
	from PortfolioProject..fifa21



	--Showing off random skills 
	--Joins

	Select a.id, a.[team & contract], b.id, b.YearRange
	from PortfolioProject.dbo.fifa21 a
	Join PortfolioProject.dbo.fifa21 b
	on a.id=b.id



--Delete Unused Columns

Select *
from PortfolioProject..fifa21

Alter Table PortfolioProject.dbo.fifa21
Drop Column [Gk Handling], [Gk Kicking], [Gk Reflexes]
