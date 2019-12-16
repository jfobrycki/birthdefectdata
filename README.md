# Final Project, December 2019

<!-- TOC -->

- Final Project
    - [Overview](#overview)
    - [Data sources](#data-sources)
    - [Methodology](#create-a-new-repository)
        - [Datasets](#Datasets)
        - [Merging](#Merging)
        - [Mapping](#Mapping)
    - [Limitations](#Limitations)
    - [Future Efforts](#future-efforts)
    - [Document summary](#document-summary)

<!-- /TOC -->

## Overview

The goal of this map is to identify areas that have birth defect surveillance programs and other areas where programs could be started by comparing locations with surveillance programs to total birth numbers by country. These surveillance programs are important for collecting and combining data about the types of birth defects that are occurring across regions and around the world. This information can then be used to identify which birth defects are most common and if there are temporal or geographic trends in the birth defects distribution. A further discussion about the limitations of this current map and avenues for next steps are discussed in the [Future Objectives] (#future-objectives) section.

The final submission for this project is a static map as a PNG. 


## Data sources

This project uses three primary datasources. More information about how data sources were combined and edited is provided in the [Methodology section](#Methodology)

<b>Total births</b>
<li>The United Nations maintains projections of global birth rates by countries and by region. This resource is available from this <a href="https://population.un.org/wpp/Download/Standard/CSV/">downloads page</a>. The specific dataset of interest is <a href="https://population.un.org/wpp/Download/Files/1_Indicators%20(Standard)/CSV_FILES/WPP2019_Fertility_by_Age.csv">Fertility Indicators</a>. </li>

<b>Birth defects surveillance</b>
<li>
<a href="http://www.icbsr.org" target="_blank">The International Clearinghouse for Birth Defects Surveillance and Research</a>is one place where multiple entities combine data about birth defects. There are other places where this information is combined, for example <a href="https://eu-rd-platform.jrc.ec.europa.eu/eurocat" target="new">EUROCAT</a>. 
For this map, the ICBDSR provides a <a href=" http://www.icbdsr.org/programme-description/" target="new">list of places where surveillance programs occur</a>. This list can be copied and pasted into a text document or excel file.
</li>  

<b>Country maps</b>
<li>
A shapefile that contains countries of the world is available from Natural Earth, specifically the <a href="https://www.naturalearthdata.com/downloads/50m-cultural-vectors/50m-admin-0-countries-2/">Admin 0 – Countries option without boundary lakes</a>. A direct download of this file is available <a href="https://www.naturalearthdata.com/http//www.naturalearthdata.com/download/50m/cultural/ne_50m_admin_0_countries_lakes.zip">here</a>.
        
<b>Data summary</b><br>
1. <a href="https://population.un.org/wpp/Download/Files/1_Indicators%20(Standard)/CSV_FILES/WPP2019_Fertility_by_Age.csv">The UN Fertility Indicators dataset</a>.
2. <a href=" http://www.icbdsr.org/programme-description/" target="new">A list of places where surveillance programs occur from the ICBDSR</a>.
3. <a href="https://www.naturalearthdata.com/http//www.naturalearthdata.com/download/50m/cultural/ne_50m_admin_0_countries_lakes.zip">The Natural Earth country shapefile without boundary lakes</a>.
  
## Methodology

### Datasets

#### The UN Fertility Indicators

<p>This dataset can be loaded into QGIS and will appear as WPP2019_Fertility_By_Age. As noted on the UN's page listed above, this file contains 
<li>Fertility indicators, by age, annualy and 5-year periods, from 1950-1955 to 2095-2100.</li>
<li>ASFR: Age-specific fertility rate (births per 1,000 women)</li>
<li>PASFR: Percentage age-specific fertility rate</li>
<li>Births: Number of births, both sexes combined (thousands)</li>

The key variable of interest is the Birth column. Rather than looking at all 5-year ranges, the final map targets the next 5 year time period of 2020 to 2025 and shows the total number of births for each country.

After opening the table of contents in QGIS, there are several steps that should be taken care of using SQL. Each country has several rows of data, including number of births by mother's age, the different 5-year ranges going up to 2100, and several different population growth estimates.


For this map, I selected the following options as shown in the SQL command I used.

<img src="images/SQL_Used.jpg">

To apply this SQL, I first created a new virtual layer by navigating to Layer > Create Layer > New Virtual Layer in QGIS.

The code selects five variables that will be carried into the new virtual layer. These variables are listed below the SELECT command and including the sum of births. By summing across mother's age categories, I could generate an estimate for the total number of births in a country. The FROM command specified the new virtual layer should be created from the WPP2019_Fertility_By_Age dataset. The WHERE command specified that only the table rows from the in which MidPeriod was equal to 2023 and the estimate population growth (Variant) was not changes ("No change"). Finally, the LocID column provided unique country codes that worked as a grouping variable.

The result was a new virtual layer that looked like this.

<img src="images/TableCheck.jpg">

In the image, row 236 is highlighted that shows the results for the United States. This cell indicates that between 2020 and 2025 (5 years), about 20,093,000 children will be born. The birth column results are in thousands as noted above. 

To double check this, I visited the CDCs page about <a href="https://www.cdc.gov/nchs/fastats/births.htm" target="new">Births and Natality</a>. This page showed the total number of births in 2018 was about 3.8 million. An estimate of 20 million births over 5 years seems reasonable and this indicates the SQL function worked correctly.
</p>

#### The ICBDSR Locations

The data available on ICBDSR locations was only available as a table on the website. This information was copied and pasted into an excel document called Surveillance_Locations_v01. I used text to columns to split the data into columns. After editing and adding some columns, I saved this file as a csv.

#### Countries of the world

The shapefile from Natural Earth was downloaded, unzipped,and loaded into QGIS. 

## Merging

A challenge in making the final map is that there were three datasets without a common linking code or country numbering system. An initial spatial join btween the Natural Earth shapefile and the five year birth totals by country resulted in over 30 countries that were missed due to spelling or naming variations. One option that I tried was <a href="https://spatialthoughts.com/2019/09/26/fuzzy-table-joins-in-qgis/" target="new">fuzzy matching</a> that could provide a method for partial spelling matches. 

The option I used for getting the different data sources to have a common element for merging was to create the column by hand. This involved using Excel and conditional formatting to identify where spelling diffences were occuring between country names betwen the Natural Earth list of countries and the new virtual layer made from the UN dataset. Along with the country spelling, I used the three letter country code available in the Natural Earth dataset, ADM0_A3.

The end result of this process was the following:
<li>The edited UN dataset had similar country names as the Natural Earth dataset and the edited UN dataset had a column of three letter country codes.</li>
<li>The list of surveillance programs also had the same spelling as the Natural Earth dataset and the three letter country code.</li>

<b>Data note</b><br>
When working with the edited UN dataset as a csv file, I rounded the number of births to the nearest whole number. Without rounding, QGIS did not seem to be reading the value correctly and I was not able to convert to a number. After rounded to the nearest whole number, the data column of births was read as a string and this could be converted to a number.
<img src="images/ConversionStep.jpg">
<br>

Now that the three data files had similar naming systems, joins were used to add the UN birth data and the ICBDSR data to the Natural Earth shapefile.

<img src="images/JoinExample.jpg">

## Mapping

WIth all data joined together, two types of visualizations were conducted.

The first created a chloropleth map of the total number of births by country. A graduated, single color ramp was selected because the values shown were a single variable that was increasing in magnitude. Four categories were selected to show contrasts among the countries but to avoid having too many shades of green on the map.

<img src="images/SymbologyExample.jpg">


<img src="images/MapExample.jpg">

The second visualization highlighted the countries where some form of birth defect surveillance was occurring. Rather than showing both pieces of information in one map, I chose to show the information in two maps. Data can be considered to be illuminating as it provides new information on a given topic. With incomplete data, an entire situation might be unable to be characterized. I selected this visual contrast through the two maps. A single map with country outlines showing which countries have birth defects surveillance is a possibility too.

<img src="images/SurveillanceExample.jpg">

For display, I selected the World Robinson projection, EPSG 54030 because it provided a globe-like feel to the map by having the edges be rounded. I found this projection listed <a href="https://futuremaps.com/blogs/news/top-10-world-map-projections" target="new"> on a page from the The Future Mapping Company</a>.

## Limitations

These maps show the countries that are listed on the ICBDSR as having surveillance programs. There are many surveillance programs that can occur at local, regional, and national levels. This map is not intended to convey that collectively we have no information about birth defects in other countries. Instead, the map is intended to help spur discussion about ways to help organize and combine datasets across the world to improve our collective understanding of birth defects. Additionally, surveillance programs do not always operate on a national scale. These maps show an entire country because that was a reasonable merge to the Natural Earth dataset. There are multi-country efforts that were not shown on these maps.

## Future efforts

<li> Create a map that shows the coverage areas of birth defect surveillance programs within countries</li>
<li> Add layers that indicate which parts of the world are covered by other birth defect surveillance programs </li>
<li> Generate estimates for the total number of births that are covered by surveillance programs </li>
<li> Analyze locations where new surveillance programs could be started to help increase our collective understanding of birth defects</li>
<li> A zoomable, interactive map that shows the locations of hospitals and clinics that participate in surveillance programs with shaded areas indicating the areas served by each hospital or clinic.

## Document Summary

The final documents used for these two maps are as follows:

<li><a href="maps/FiveYearBirthTotalsByCountry_v02.csv">The Five Year Birth Totals by Country</a> - calculated from the UN dataset, as a csv file </li>

<li><a href="maps/SurveillanceLocations_v01.csv">The locations of each surveillance program</a> - edited from the ICBDSR website </li>

<li><a href="https://www.naturalearthdata.com/http//www.naturalearthdata.com/download/50m/cultural/ne_50m_admin_0_countries_lakes.zip">The countries shapefile from Natural Earth</a></li>

<li>The map of total births between 2020 and 2025 available as a <a href="maps/BirthTotals_v01_1200px.png" target="new">1200 px</a> or <a href="maps/BirthTotals_v01_1200px.png" target="new">8000 px</a> wide PNG.</li>

<li>The map of birth surveillance locations availabe as a <a href="maps/SurveillanceMap_v01_1200px.png" target="new">1200 px</a> or <a href="maps/SurveillanceMap_v01_8000px.png" target="new">8000 px</a> wide PNG.</li>


