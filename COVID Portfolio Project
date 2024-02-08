 SELECT*
FROM ProjectPortfolio..CovidDeaths$
WHERE continent IS NOT NULL
ORDER BY 3,4

--SELECT *
--FROM ProjectPortfolio..[Covid vaccinations]
--ORDER BY 3,4


-- SELECT DATA THAT WE WILL BE USING

SELECT location,date,total_cases,new_cases,total_deaths,population
FROM ProjectPortfolio..CovidDeaths$
ORDER BY 1,2

-- TOTAL CASES VS TOTAL DEATHS
-- Shows likely hood of dying if you contact covid in kenya

SELECT location,date,total_cases,total_deaths,(total_deaths/total_cases)*100 AS death_percentage
FROM ProjectPortfolio..CovidDeaths$
WHERE location LIKE '%nya'and continent IS NOT NULL
ORDER BY 1,2

--Looking at the total cases vs the population in kenya
--shows total population that contracted covid
SELECT location,date,total_cases,population,(total_cases/population)*100 AS total_cases_percentage
FROM ProjectPortfolio..CovidDeaths$
WHERE location LIKE '%nya'
ORDER BY 1,2

-- Looking at countries with the highest infection rate compared to population

SELECT location,
       MAX(total_cases) AS highest_infection_count,
	   MAX((total_cases/population))*100 AS Percentpopulationinfected
FROM ProjectPortfolio..CovidDeaths$
--WHERE location LIKE '%nya'
GROUP BY location,population
ORDER BY 3 desc

--Showing countries with highest death count per population 
SELECT location,MAX(cast(total_deaths as int))AS TotalDeathCount
FROM ProjectPortfolio..CovidDeaths$
--WHERE location LIKE '%nya'
WHERE continent IS NOT NULL
GROUP BY location
ORDER BY 2 desc

--LET'S BREAK THINGS DOWN BY CONTINENT

--SHOWING THE CONTINENTS WITH THE HIGHEST DEATH COUNT

SELECT continent,MAX(cast(total_deaths as int))AS TotalDeathCount
FROM ProjectPortfolio..CovidDeaths$
--WHERE location LIKE '%nya'
WHERE continent IS NOT NULL
GROUP BY continent
ORDER BY 2 desc



-- GLOBAL NUMBERS
SELECT
           SUM(new_cases) AS cases,
		   SUM(CAST(new_deaths AS int)) AS deaths,
		   SUM(CAST(new_deaths AS int))/SUM(new_cases)*100 as DeathPercentage
FROM ProjectPortfolio..CovidDeaths$
--WHERE location LIKE '%nya' 
WHERE continent IS NOT NULL
--GROUP BY date
ORDER BY 1,2

-- Looking at total population vs vaccinations

SELECT dea.continent, 
       dea.location,
	   dea.date,
	   dea.population,
	   vac.new_vaccinations,
	   sum(CONVERT(int, vac.new_vaccinations )) OVER (partition by dea.location,dea.date) as RollingPeopleVaccinated
FROM ProjectPortfolio..CovidDeaths$ dea
JOIN ProjectPortfolio..[Covid vaccinations] vac
    ON dea.location=vac.location
	and dea.date = vac.date
WHERE dea.continent IS NOT NULL
	ORDER BY 2,3


--USE A CTE

WITH PopvsVac(continent,location, date,population,new_vaccinations,RollingPeopleVaccinated)
as
(
SELECT dea.continent, 
       dea.location,
	   dea.date,
	   dea.population,
	   vac.new_vaccinations,
	   sum(CONVERT(int, vac.new_vaccinations )) OVER (partition by dea.location,dea.date) as RollingPeopleVaccinated
	  
FROM ProjectPortfolio..CovidDeaths$ dea
JOIN ProjectPortfolio..[Covid vaccinations] vac
    ON dea.location=vac.location
	and dea.date = vac.date
WHERE dea.continent IS NOT NULL
	--ORDER BY 2,3
)
SELECT *,(RollingPeopleVaccinated/population*100)
FROM PopvsVac


--Temp table
DROP TABLE IF exists #PercentPopulationVaccinated 

CREATE TABLE #PercentPopulationVaccinated
(
Continent nvarchar(255),
Location nvarchar(255),
Date datetime,
population numeric,
new_vaccination numeric,
RollingPeopleVaccinated numeric
 )
 INSERT INTO #PercentPopulationVaccinated
 SELECT dea.continent, 
       dea.location,
	   dea.date,
	   dea.population,
	   vac.new_vaccinations,
	   sum(CONVERT(int, vac.new_vaccinations )) OVER (partition by dea.location,dea.date) as RollingPeopleVaccinated
	  
FROM ProjectPortfolio..CovidDeaths$ dea
JOIN ProjectPortfolio..[Covid vaccinations] vac
    ON dea.location=vac.location
	and dea.date = vac.date
--WHERE dea.continent IS NOT NULL
	--ORDER BY 2,3
SELECT *,(RollingPeopleVaccinated/population*100)
FROM #PercentPopulationVaccinated



--CREATING VIEW TO STORE DATA FOR LATER VISUALIZATION

create view PercentPopulationVaccinated as
SELECT dea.continent, 
       dea.location,
	   dea.date,
	   dea.population,
	   vac.new_vaccinations,
	   sum(CONVERT(int, vac.new_vaccinations )) OVER (partition by dea.location,dea.date) as RollingPeopleVaccinated
	  
FROM ProjectPortfolio..CovidDeaths$ dea
JOIN ProjectPortfolio..[Covid vaccinations] vac
    ON dea.location=vac.location
	and dea.date = vac.date
WHERE dea.continent IS NOT NULL
--ORDER BY 2,3


