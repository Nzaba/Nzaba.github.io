---
title: "Opioid Crisis Dashboard"
date: "2018-05-03"
description: "Making Sense of The Opioid Crisis"
tags: ["Public Health"]
ShowToc: true
ShowBreadCrumbs: false
---

An online tool which sheds more light on the numbers behind the opioid crisis

<!--more-->

### Abstract

<p>According to the U.S. Drug Enforcement Administration, <a href="https://www.dea.gov/docs/2015%20NDTA%20Report.pdf">“overdose deaths, particularly from prescription drugs and heroin, have reached epidemic levels”</a>. From 1999 to 2016, more than 630,000 people have died from a drug overdose. On average, 115 Americans die every day from an opioid overdose.<a href="http://wonder.cdc.gov"><span class="math inline">\(^1\)</span></a></p>

### Introduction

<p>Opioid abuse is a pervasive problem that has persisted and been responsible for the deaths of thousands and the decreasing life expectancy of Americans. While it is true that every strata of society is in danger from the effects of this epidemic, its effects vary widely across age groups and gender. This project is significant because it is an accessible way to know more about this silent disaster. The aim is that people that don’t know about the crisis will be able to talk knowledgeably about what it is and how it affects society, and that those who already know about it will be able to correct and enhance their understanding of the numbers behind the situation.</p>
<p>The first wave began with increased prescribing of opioids in the 1990s<a href="https://www.annualreviews.org/doi/abs/10.1146/annurev-publhealth-031914-122957"><span class="math inline">\(^2\)</span></a>, with overdose deaths involving prescription opioids (natural and semi-synthetic opioids and methadone) increasing since at least 1999<a href="https://www.cdc.gov/drugoverdose/epidemic/index.html"><span class="math inline">\(^3\)</span></a>. The third wave began in 2013, with significant increases in overdose deaths involving synthetic opioids – particularly those involving illicitly-manufactured fentanyl (IMF). The IMF market continues to change, and IMF can be found in combination with heroin, counterfeit pills, and cocaine<a href="https://www.cdc.gov/drugoverdose/epidemic/index.html"><span class="math inline">\(^4\)</span></a>.</p>

### Data

<p>The data we used for our analysis was obtained from the following sources:</p>
<ol style="list-style-type: decimal">
<li><p><a href="https://wonder.cdc.gov/">The CDC.</a> This is an online database belonging to the CDC. It contains wide ranging data for Epidemiologic Research. We tried using R packages to query the website’s API, but they were not as user friendly as we hoped. Eventually we resorted to performing direct queries on the website itself, but this had its own limitations in terms of size. Forty queries were done (each had a limit of 75,000 lines) to obtain 1999 - 2016 data on drug poisoning mortality rates.</p></li>
<li><p><a href="https://data.world/cms/e4ka-3ncx">Data world website.</a> This dataset from the The Centers for Medicare &amp; Medicaid Services contains the information on the individual opioid prescribing rates of health providers that participate in Medicare Part D program. This file provides data on the number and percentage of prescription claims (includes new prescriptions and refills) for opioid drugs, and contains information on each provider’s name, specialty, state, and ZIP code. Available documentation can be found <a href="https://www.cms.gov/Research-Statistics-Data-and-Systems/Statistics-Trends-and-Reports/Medicare-Provider-Charge-Data/Downloads/Prescriber_Methods.pdf">here</a>.</p></li>
</ol>

### Findings

#### Patterns & Trends

<p>In the 2000s, the age groups that were most affected by drug related deaths were individuals that were 45 years or older. As the crisis expanded, and with the introduction of synthetic opioids such as Fentanyl in the early 2010s, the 25-34 age group became the most affected age group. Generally, all the age groups have experienced an upward trend, but this younger age group’s change is a good insight to keep in mind when we are finding ways to implement policies to address the drug crisis.</p>
<p>The trends across different racial groups show that the opioid crisis has predominantly affected the White population of the United States. However, the fact that the Black community’s deaths have more than sixtupled since 1999 cannot be overlooked. It is important to consider how the different races are affected by the problem. Additionaly, male mortality rate has stayed at a constant level above that of females with roughly twice as many deaths.</p>
<p>The problem is clearly a national problem, but it affects the young adult age group the most, and White people the most. Is the problem worse in certain regions of the country?</p>

#### Visualizations

<p>Below is a map that shows the top 10 counties that were most affected by the epidemic in 2016. The midwest seems to have been the most severely hit by the opioid epidemic.</p>

Add map1

<p>The choropleth map below is shaded in propotion to the number of age adjusted deaths in all states in 2014. Some of the cities with the highest Opioid prescription rates were Sacramento (CA), Piedmont (SD) and Stringtown (OK).</p>

Add map2


#### Shiny Widget

Add shiny

https://raymondnzaba.shinyapps.io/OpioidCrisis/

### Limitations & Conclusion

<p>This research hones in on the casualties caused by drug poisoning and overdoses yet the opioid crisis is a multifaceted problem involving policing and a whole drug trafficking and distribution industry. More time and resources are needed to obtain data that would ideally enrich the perspectives on the problem presented herein. Information on drug prescriptions and arrest information would help break down this sensitive topic even more and show the true severity of the opioid epidemic. The app is posted on the internet and can be used by anyone as a tool to inform and educate.</p>


### Technical Appendix

<pre class="r"><code>#Imports necessary packages
library(tidyverse)
library(ggplot2)
library(readr)
library(stringr)
library(maps)
library(ggmap)
library(mapproj)
library(leaflet)
library(httr)
library(plotly)
library(leaflet)
library(plotly)
library(RColorBrewer)</code></pre>
<pre class="r"><code>#Reads in the data that is grouped by Year, County, Drug Cause, and Age Groups for 1999-2016
YearCountyCauseAgeGroupsAllYears &lt;- as_tibble()
for (year in 1999:2016) {
  YearCountyCauseAgeGroupsAllYears &lt;- rbind(YearCountyCauseAgeGroupsAllYears,
                                            read_csv(paste0(&quot;../Data/WonderData/YearCountyCauseAgeGroups&quot;,year,&quot;.csv&quot;)))
}</code></pre>
<pre class="r"><code>#Reads in the data that is grouped by Year, County, Drug Cause, and Gender for 1999-2016
YearCountyCauseGenderAllYears  &lt;- as_tibble()
for (part in 1:9) {
  YearCountyCauseGenderAllYears &lt;- rbind(YearCountyCauseGenderAllYears,
                                         read_csv(paste0(&quot;../Data/WonderData/YearCountyCauseGender10States&quot;,part,&quot;.csv&quot;)))
}</code></pre>
<pre class="r"><code>#Reads in the data that is grouped by Year, County, Drug Cause, and Race for 1999-2016
YearCountyCauseRaceHomicideAllYears  &lt;- as_tibble()
for (part in 1:6) {
  YearCountyCauseRaceHomicideAllYears &lt;- rbind(YearCountyCauseRaceHomicideAllYears,
                                         read_csv(paste0(&quot;../Data/WonderData/YearCountyCauseRaceHomicideAllYears10States&quot;,part,&quot;.csv&quot;)))
}

#Reads in the data that is grouped by Year, County, Drug Cause, and Race for 1999-2016
YearCountyCauseRaceAllYears &lt;- rbind(read_csv(&quot;../Data/WonderData/YearCountyCauseRaceUndeterminedAllYears.csv&quot;),
                                     read_csv(&quot;../Data/WonderData/YearCountyCauseRaceSuicidesAllYears.csv&quot;),
                                     read_csv(&quot;../Data/WonderData/YearCountyCauseRaceUnintentional1999-2007.csv&quot;),
                                     read_csv(&quot;../Data/WonderData/YearCountyCauseRaceUnintentional2008-2016.csv&quot;),
                                     YearCountyCauseRaceHomicideAllYears)</code></pre>
<pre class="r"><code>X2015_Gaz_counties_national &lt;- read_csv(&quot;../Data/WonderData/2015_Gaz_counties_national.csv&quot;)</code></pre>
<pre class="r"><code>PState &lt;- read_csv(&quot;../Data/Drug_Poisoning_Mortality_by_State__United_States.csv&quot;)
prescription&lt;- read_csv(&quot;../Data/Opioid analgesic prescriptions dispensed from US retail pharmacies, Q4 2009-Q2 2015.csv&quot;)
zip_codes &lt;- read_csv(&quot;../Data/zip_codes_states.csv&quot;)
stateabbr &lt;- read.csv(&quot;https://raw.githubusercontent.com/plotly/datasets/master/2011_us_ag_exports.csv&quot;)
load(&quot;../Data/prescriber.Rda&quot;)</code></pre>
<pre class="r"><code>#cleans up the data that has age groups
ageGroupsData &lt;- YearCountyCauseAgeGroupsAllYears %&gt;% 
  mutate(percentTotalDeaths = parse_number(`% of Total Deaths`),   
         crudeRateLower95Confint = parse_number(`Crude Rate Upper 95% Confidence Interval`),
         crudeRateUpper95Confint = parse_number(`Crude Rate Lower 95% Confidence Interval`),
         crudeRate = parse_number(`Crude Rate`),
         population = parse_number(Population)) %&gt;%
  separate(County, c(&quot;County&quot;, &quot;State&quot;), sep = &quot;,&quot;) %&gt;%
  left_join(X2015_Gaz_counties_national, by = c(&quot;County&quot; = &quot;NAME&quot;)) %&gt;%
  rename(drugAlcoholInducedCause = `Drug/Alcohol Induced Cause`,
         ageGroups = `Ten-Year Age Groups Code`,
         latitude = INTPTLAT,
         longitude = INTPTLONG) %&gt;%
  select(Year, County, State, drugAlcoholInducedCause, ageGroups, Deaths, population, crudeRate, crudeRateLower95Confint, crudeRateUpper95Confint, latitude, longitude) %&gt;%
  mutate(State = str_trim(State),
         ageGroups = ifelse(ageGroups == &quot;1&quot;, &quot;&lt;1&quot;, ageGroups),
         ageGroups = factor(ageGroups, levels = c(&quot;&lt;1&quot;, &quot;1-4&quot;, &quot;5-14&quot;, &quot;15-24&quot;, &quot;25-34&quot;, &quot;35-44&quot;, &quot;45-54&quot;, &quot;55-64&quot;, &quot;65-74&quot;, &quot;75-84&quot;, &quot;85+&quot;)))</code></pre>
<pre class="r"><code>#cleans up the data that has gender
genderData &lt;- YearCountyCauseGenderAllYears %&gt;%
   mutate(percentTotalDeaths = parse_number(`% of Total Deaths`),   
         crudeRateLower95Confint = parse_number(`Crude Rate Upper 95% Confidence Interval`),
         crudeRateUpper95Confint = parse_number(`Crude Rate Lower 95% Confidence Interval`),
         crudeRate = parse_number(`Crude Rate`),
         population = parse_number(Population),
         ageAdjRate = parse_number(`Age Adjusted Rate`),
         gender = as.factor(Gender))%&gt;%
  separate(County, c(&quot;County&quot;, &quot;State&quot;), sep = &quot;,&quot;) %&gt;%
  left_join(X2015_Gaz_counties_national, by = c(&quot;County&quot; = &quot;NAME&quot;)) %&gt;%
  rename(drugAlcoholInducedCause = `Drug/Alcohol Induced Cause`,
         latitude = INTPTLAT,
         longitude = INTPTLONG) %&gt;%
  select(Year, County, State, drugAlcoholInducedCause, Deaths, population, crudeRate, crudeRateLower95Confint, crudeRateUpper95Confint, ageAdjRate, gender, latitude, longitude) %&gt;%
  mutate(State = str_trim(State))</code></pre>
<pre class="r"><code>#cleans up the data that has race
raceData &lt;- YearCountyCauseRaceAllYears %&gt;%
  mutate(percentTotalDeaths = parse_number(`% of Total Deaths`),  
         Year = parse_number(Year),
         crudeRateLower95Confint = parse_number(`Crude Rate Upper 95% Confidence Interval`),
         crudeRateUpper95Confint = parse_number(`Crude Rate Lower 95% Confidence Interval`),
         crudeRate = parse_number(`Crude Rate`),
         population = parse_number(Population),
         ageAdjRate = parse_number(`Age Adjusted Rate`),
         ageAdjRateLowerConfint = parse_number(`Age Adjusted Rate Lower 95% Confidence Interval`),
         ageAdjRateUpperConfint = parse_number(`Age Adjusted Rate Upper 95% Confidence Interval`),
         race = as.factor(Race),
         Deaths = parse_number(Deaths))%&gt;%
  separate(County, c(&quot;County&quot;, &quot;State&quot;), sep = &quot;,&quot;) %&gt;%
  left_join(X2015_Gaz_counties_national, by = c(&quot;County&quot; = &quot;NAME&quot;)) %&gt;%
  rename(drugAlcoholInducedCause = `Drug/Alcohol Induced Cause`,
         latitude = INTPTLAT, 
         longitude = INTPTLONG) %&gt;%
  select(Year, County, State, drugAlcoholInducedCause, Deaths, population, crudeRate, crudeRateLower95Confint, crudeRateUpper95Confint, ageAdjRate, ageAdjRateLowerConfint, ageAdjRateUpperConfint,race, latitude, longitude) %&gt;%
  mutate(State = str_trim(State))</code></pre>
<pre class="r"><code>#Select and rename relevant columns
PState_wrangled &lt;-PState %&gt;% 
  select(State,Year,Sex,`Age Group`,`Race and Hispanic Origin`,`Crude Death Rate` ,`Age-adjusted Rate`) %&gt;% 
  mutate(Age_Group=`Age Group`,Race=`Race and Hispanic Origin`,Crude_Death_Rate=`Crude Death Rate`,Age_Adjusted_Rate=`Age-adjusted Rate`) %&gt;% 
  select(-`Age Group`,-`Race and Hispanic Origin`,-`Crude Death Rate`,-`Age-adjusted Rate`) </code></pre>
<pre class="r"><code>#Parse and make column names more readable
presc&lt;-prescription %&gt;% 
  mutate(Yearly_totals_all_opioid_analgesics = as.integer(`Yearly totals (All Opioid Analgesics)`),
         Yearly_totals_HOTMF = as.integer(`Yearly totals (H+O+T+M+F)`),
        Yearly_totals_HO = as.integer(`Yearly totals (H+O)`,
        Yearly_totals_ER_LA_opioid_analgesics = as.integer(`Yearly totals (ER/LA Opioid Analgesics)`)))</code></pre>
<pre class="r"><code>#top opioid prescribers
prescribers&lt;-prescriber %&gt;% 
  separate(`Opioid Prescribing Rate`, c(&quot;Opioid_Prescribing_Rate&quot;,&quot;junk&quot;), sep = &quot;%&quot;) %&gt;% 
  mutate(Opioid_Prescribing_Rate=as.integer(Opioid_Prescribing_Rate)) %&gt;% 
  select(-junk,-NPI) %&gt;% 
  group_by(`Specialty Description`) %&gt;% 
  summarize(count = n(),Opioid_Prescribing_Rate=sum(Opioid_Prescribing_Rate)/count) %&gt;% 
  drop_na() %&gt;% 
  arrange(desc(Opioid_Prescribing_Rate)) %&gt;% 
  head(4)</code></pre>
<pre class="r"><code>#opioid prescription rates by zip code
rates&lt;-prescriber %&gt;% 
  separate(`Opioid Prescribing Rate`, c(&quot;Opioid_Prescribing_Rate&quot;,&quot;junk&quot;), sep = &quot;%&quot;) %&gt;% 
  mutate(Opioid_Prescribing_Rate=as.integer(Opioid_Prescribing_Rate),
         NPPES_zip_code=as.character(`NPPES Provider Zip Code`)) %&gt;% 
  select(-junk,-NPI,-`NPPES Provider Zip Code`) %&gt;% 
  group_by(NPPES_zip_code) %&gt;% 
  summarize(count=n(),Opioid_Prescribing_Rate=sum(Opioid_Prescribing_Rate)/count) %&gt;% 
  arrange(desc(Opioid_Prescribing_Rate)) </code></pre>
<pre class="r"><code>#clean table containing state abbreviations
stateabbr &lt;- stateabbr %&gt;% 
  select(code,state)</code></pre>
<pre class="r"><code>#Consolidate zipcode and prescribing rate data, death and drug info
rates&lt;-rates %&gt;% 
  left_join(zip_codes,by=c(&quot;NPPES_zip_code&quot; = &quot;zip_code&quot;)) %&gt;% 
  na.omit() %&gt;% 
  group_by(state) %&gt;% 
  summarize(count=n(), Opioid_Prescribing_Rate=sum(Opioid_Prescribing_Rate)/count) %&gt;% 
  arrange(desc(Opioid_Prescribing_Rate)) %&gt;% 
  head(4)</code></pre>
<pre class="r"><code>#Wrangle state death records to display on map
PS&lt;-PState_wrangled %&gt;%
  filter(Year==2014) %&gt;% 
  group_by(State) %&gt;% 
  summarize(count=n(),Crude_Death_Rate=sum(Crude_Death_Rate)/count,
            Age_Adjusted_Rate=sum(Age_Adjusted_Rate)/count) %&gt;% 
  filter(State!=&quot;United States&quot;) %&gt;% 
  left_join(stateabbr,by=c(&quot;State&quot; = &quot;state&quot;)) %&gt;% 
  select(State,Crude_Death_Rate,code,Age_Adjusted_Rate) %&gt;% 
  mutate(code=replace(code, State==&quot;California&quot;, &quot;CA&quot;),Crude_Death_Rate=round(Crude_Death_Rate, 1),
         Age_Adjusted_Rate=round(Age_Adjusted_Rate, 1))%&gt;% 
  left_join(rates,by=c(&quot;code&quot; = &quot;state&quot;))%&gt;% 
  mutate(Opioid_Prescribing_Rate=round(Opioid_Prescribing_Rate,1))</code></pre>
<pre class="r"><code>ageGroupsData %&gt;%
  filter(ageGroups != &quot;NS&quot;, Year == 2016, drugAlcoholInducedCause == &quot;Drug poisonings (overdose) Unintentional (X40-X44)&quot;) %&gt;%
  group_by(Year, ageGroups) %&gt;%
  summarize(numObs = n(), TotalDeaths = sum(Deaths)) %&gt;%
  plot_ly(x = ~ageGroups, y = ~TotalDeaths, type = &quot;bar&quot;, name = &quot;bargraph&quot;)</code></pre>
<pre class="r"><code>raceData %&gt;%
  filter(Year == 2016, drugAlcoholInducedCause == &quot;Drug poisonings (overdose) Unintentional (X40-X44)&quot;) %&gt;%
  group_by(Year, race) %&gt;%
  summarize(numObs = n(), TotalDeaths = sum(Deaths)) %&gt;%
  plot_ly(x = ~race, y = ~TotalDeaths, type = &quot;bar&quot;, color = ~race) </code></pre>
<pre class="r"><code>genderData %&gt;%
  filter(Year == 2016, drugAlcoholInducedCause == &quot;Drug poisonings (overdose) Unintentional (X40-X44)&quot;) %&gt;%
  group_by(Year, gender) %&gt;%
  summarize(numObs = n(), TotalDeaths = sum(Deaths)) %&gt;%
  plot_ly(x = ~gender, y = ~TotalDeaths, type = &quot;bar&quot;, color = ~gender) </code></pre>
<pre class="r"><code>ageGroupsData %&gt;%
  group_by(ageGroups, Year) %&gt;%
  filter(drugAlcoholInducedCause == &quot;Drug poisonings (overdose) Unintentional (X40-X44)&quot;|
           drugAlcoholInducedCause == &quot;All other drug-induced causes&quot;|
           drugAlcoholInducedCause == &quot;Drug poisonings (overdose) Suicide (X60-X64)&quot;|
           drugAlcoholInducedCause == &quot;Drug poisonings (overdose) Undetermined (Y10-Y14)&quot;) %&gt;%
  summarize(numObs = n(), TotalDeaths = sum(Deaths)) %&gt;%
  plot_ly(x = ~Year, y = ~TotalDeaths, type = 'scatter', color = ~ageGroups, mode = &quot;lines+markers&quot;) </code></pre>
<pre class="r"><code>raceData %&gt;%
  group_by(race, Year) %&gt;%
  filter(drugAlcoholInducedCause == &quot;Drug poisonings (overdose) Unintentional (X40-X44)&quot;|
           drugAlcoholInducedCause == &quot;All other drug-induced causes&quot;|
           drugAlcoholInducedCause == &quot;Drug poisonings (overdose) Suicide (X60-X64)&quot;|
           drugAlcoholInducedCause == &quot;Drug poisonings (overdose) Undetermined (Y10-Y14)&quot;) %&gt;%
  summarize(numObs = n(), TotalDeaths = sum(Deaths)) %&gt;%
  plot_ly(x = ~Year, y = ~TotalDeaths, type = 'scatter', color = ~race, mode = &quot;lines+markers&quot;) </code></pre>
<pre class="r"><code>genderData %&gt;%
  group_by(gender, Year) %&gt;%
  filter(drugAlcoholInducedCause == &quot;Drug poisonings (overdose) Unintentional (X40-X44)&quot;|
           drugAlcoholInducedCause == &quot;All other drug-induced causes&quot;|
           drugAlcoholInducedCause == &quot;Drug poisonings (overdose) Suicide (X60-X64)&quot;|
           drugAlcoholInducedCause == &quot;Drug poisonings (overdose) Undetermined (Y10-Y14)&quot;) %&gt;%
  summarize(numObs = n(), TotalDeaths = sum(Deaths)) %&gt;%
  plot_ly(x = ~Year, y = ~TotalDeaths, type = 'scatter', color = ~gender, mode = &quot;lines+markers&quot;) </code></pre>
<pre class="r"><code>myTemp &lt;- ageGroupsData %&gt;%
  group_by(County) %&gt;%
  summarize(NumObs = n(), SumDeaths = sum(Deaths), avgLong = mean(longitude), avgLat = mean(latitude)) %&gt;%
  arrange(desc(SumDeaths)) %&gt;%
  head(25)

leaflet(myTemp) %&gt;%
  addTiles() %&gt;%
  addCircles(lng = ~avgLong, lat = ~avgLat, radius = ~SumDeaths/100) %&gt;%
  setView(lng = -91.39, lat = 38.42, zoom = 5)</code></pre>
<pre class="r"><code>#Exploratory visual showing trends in single states
PState_wrangled %&gt;% 
  filter(State==&quot;Wyoming&quot;) %&gt;% 
ggplot(.,aes(x=Year,y=Crude_Death_Rate,color=Sex))+geom_line()+facet_wrap(~Race)</code></pre>
<pre class="r"><code># give state boundaries a white border
l &lt;- list(color = toRGB(&quot;white&quot;), width = 2)</code></pre>
<pre class="r"><code># specify some map projection/options for plotly map
g &lt;- list(
  scope = 'usa',
  projection = list(type = 'albers usa'),
  showlakes = TRUE,
  lakecolor = toRGB('white')
)</code></pre>
<pre class="r"><code>#Customize tooltip for death rates and prescriptions map
PS$hover &lt;- with(PS, paste(State,'&lt;br&gt;',&quot;Crude death rate:&quot;, Crude_Death_Rate,'&lt;br&gt;',&quot;Opioid Prescribing Rate&quot;, Opioid_Prescribing_Rate,'&lt;br&gt;',&quot;&lt;br&gt;&quot;))</code></pre>
<pre class="r"><code>#Make death rates and prescriptions plot
plot_geo(PS, locationmode = 'USA-states') %&gt;%
  add_trace(
    z = ~Age_Adjusted_Rate, text = ~hover, locations = ~code,
    color = ~Age_Adjusted_Rate, colors = 'Purples'
  ) %&gt;%
  colorbar(title = &quot;Rate&quot;) %&gt;%
  layout(
    title = 'Average death rates breakdown ',
    geo = g
  )</code></pre>
<pre class="r"><code>#Shows crude death rates by state
br_down&lt;-PState_wrangled %&gt;%
  filter(State==&quot;United States&quot;) %&gt;% 
  group_by(State) </code></pre>
<pre class="r"><code>#Countrywide breakdown by race
br_down %&gt;%
  filter(Sex==&quot;Female&quot;,Age_Group==&quot;All Ages&quot;) %&gt;% 
ggplot(.,aes(x=Year,y=Crude_Death_Rate))+geom_line()+facet_wrap(~Race)</code></pre>



{{< css.inline >}}

<style>
.emojify {
	font-family: Apple Color Emoji, Segoe UI Emoji, NotoColorEmoji, Segoe UI Symbol, Android Emoji, EmojiSymbols;
	font-size: 2rem;
	vertical-align: middle;
}
@media screen and (max-width:650px) {
  .nowrap {
    display: block;
    margin: 25px 0;
  }
}
</style>

{{< /css.inline >}}
