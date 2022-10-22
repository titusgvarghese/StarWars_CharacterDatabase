# StarWars_CharacterDatabase

Created a Character Database using MS SQL Service Manager to generate various character reports
![starwarsproject](https://user-images.githubusercontent.com/46428703/197353405-65d12dc8-cf5a-461e-84ba-7f4615994031.png)

STEPS TO CREATE STAR WARS CHARACTER DATABASE
1.	Brainstormed which tables would be necessary 
Production table (Primary Table)
-	Table with a distinct list of movies/books/etc.
o	PK - PROD_ID
o	FK – 
o	NAME
o	YEAR_RELEASED
Production Type (Look-Up Table)
-	Table with distinct list of production types ()
o	PK - PROD_TYPE_ID
o	TYPE
o	Movie, Series, Game, Novel
Character table (Primary Table)  
-	Table with a distinct list of characters including their attributes.
o	PK - CHARACTER_ID
o	FK - SPECIES_ID
o	NAME
o	DOB
o	HEIGHT
o	WEIGHT
Character Production table (Intersection Table)
-	Table to connect characters to their respective production
o	PK - PROD_ID
o	PK - CHARACTER_ID
Species table (Look-Up Table)
-	Table with distinct list/description of species.
o	PK - SPECIES_ID
o	NAME
o	DESCRIPTION
Order of Creation:
1.	SPECIES
2.	CHARACTER
3.	PRODUCTION
4.	CHARCTER_PROD
5.	PRODUCTION_TYPE



ENTITY RELATION DIAGRAM
*Attached*


2.	Created the Tables
USE [MFGTrain]
GO
/****** Object:  Table [dbo].[LKP_SPECIES] Script Date: 7/23/2021 2:32:40 PM ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO

CREATE TABLE [dbo].[LKP_SPECIES](
	[SPECIES_ID] [int] NOT NULL PRIMARY KEY,
	[NAME] [varchar](50) NOT NULL,
	[DESCRIPTION] [varchar](255) NOT NULL,
)
GO

CREATE TABLE [dbo].[DIM_CHARACTER](
	[CHARACTER_ID] [int] NOT NULL PRIMARY KEY,
	[SPECIES_ID] [int] NOT NULL,
	[NAME] [varchar](255) NOT NULL,
	[DOB] [varchar](255) NOT NULL,
	[HEIGHT] [varchar](255) NOT NULL,
	[WEIGHT] [varchar](255) NOT NULL,
	[SYNOPSIS] [varchar](255) NOT NULL,
	FOREIGN KEY (SPECIES_ID)
		REFERENCES LKP_SPECIES(SPECIES_ID)  
)
GO

CREATE TABLE [dbo].[LKP_PRODUCTION_TYPE](
	[PROD_TYPE_ID] [int] NOT NULL PRIMARY KEY,
	[TYPE] [varchar](255) NOT NULL
)
GO

CREATE TABLE [dbo].[BASE_PRODUCTION](
	[PROD_ID] [int] NOT NULL PRIMARY KEY,
	[PROD_TYPE_ID] [int] NOT NULL,
	[NAME] [varchar](255) NOT NULL,
	[YEAR_RELEASED] [INT] NOT NULL,
	FOREIGN KEY (PROD_TYPE_ID)
		REFERENCES LKP_PRODUCTION_TYPE(PROD_TYPE_ID)  
)
GO

CREATE TABLE [dbo].[INTERSECT_CHARACTER_PROD](
	[PROD_ID] [int] NOT NULL,
	[CHARACTER_ID] [int] NOT NULL,
	PRIMARY KEY (PROD_ID, CHARACTER_ID),
	FOREIGN KEY (PROD_ID)
		REFERENCES BASE_PRODUCTION(PROD_ID),
	FOREIGN KEY (CHARACTER_ID)
		REFERENCES DIM_CHARACTER(CHARACTER_ID)  
)
GO


3.	Gathered Star Wars Character Data
[ATTACHED IN EXCEL FILE CALLED STAR WARS CHARACTER DATABASE (SWCDB)]
4.	Import the Data into Microsoft SQL Server Management
MFGTRAIN > TASKS > IMPORT DATA
5.	Insert the data from the imported tables to the tables created initially
USE [MFGTrain]
GO

INSERT INTO LKP_SPECIES SELECT * FROM SPECIES$

INSERT INTO DIM_CHARACTER SELECT * FROM CHARACTER$

INSERT INTO LKP_PRODUCTION_TYPE SELECT * FROM PRODUCTION_TYPE$

INSERT INTO BASE_PRODUCTION SELECT * FROM PRODUCTION$

INSERT INTO INTERSECT_CHARACTER_PROD SELECT * FROM CHARACTER_PROD$
6.	Initial Issues 
Issue with inserting the SYNOPSIS Column
varchar(255) was not large enough to handle one of the Synopsis’s
	Had to be update to varchar(255)
Issue with null values in PRODUCTION and PRODUCTION_TYPE tables
USE [MFGTrain]
GO

DELETE FROM PRODUCTION_TYPE$ WHERE PROD_TYPE_ID IS NULL;

DELETE FROM PRODUCTION$ WHERE PROD_ID IS NULL;

Had to drop the Intersect Character Production table
USE [MFGTrain]
GO

DROP TABLE INTERSECT_CHARACTER_PROD;

7.	Inserted the data once again




8.	Generated example reports

Find all productions that Luke Skywalker appeared in.
USE MFGTRAIN;
SELECT C.NAME, P.NAME 
FROM DIM_CHARACTER C 
INNER JOIN INTERSECT_CHARACTER_PROD CP ON C.CHARACTER_ID = CP.CHARACTER_ID 
INNER JOIN BASE_PRODUCTION P ON CP.PROD_ID = P.PROD_ID 
WHERE C.NAME = 'Luke Skywalker'

Generate a list of each distinct species in SWCDB
USE MFGTRAIN;
SELECT [SPECIES_ID], [NAME], [DESCRIPTION] FROM LKP_SPECIES

Generate a report with character name, species, character synopsis, and all Productions that the character has appeared in.
USE MFGTRAIN;
SELECT C.NAME, S.NAME, C.SYNOPSIS, P.NAME
FROM LKP_SPECIES S 
INNER JOIN DIM_CHARACTER C ON S.SPECIES_ID = C.SPECIES_ID
INNER JOIN INTERSECT_CHARACTER_PROD CP ON CP.CHARACTER_ID = C.CHARACTER_ID
INNER JOIN BASE_PRODUCTION P ON P.PROD_ID = CP.PROD_ID;


Generate a list of Star Wars Productions with Production Name and Production Type (Movie, Series, Game, Novel), and year released
USE MFGTRAIN;
SELECT P.NAME, PT.TYPE, P.YEAR_RELEASED
FROM BASE_PRODUCTION P
INNER JOIN LKP_PRODUCTION_TYPE PT ON P.PROD_TYPE_ID = PT.PROD_TYPE_ID

[RESULTS ARE ATTACHED IN EXCEL FILE CALLED STAR WARS CHARACTER DATABASE (SWCDB) REPORTS]

9.	Possible Extensions to the project
-	Create a user interface
-	Add an affiliations table to add to the project database
-	...


