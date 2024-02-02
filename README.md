# CryptoApiPowerBi
Power Bi that makes API calls to Coinbase API. In order to keep refresh time minimized for this Proof of Concept dashboard, large dataset were imported. The .pbix file itself will take some time to download.

#### Purpose: Proof of Concept dashboard for Crypto clients that makes anonymous API calls to Coinbase API. 

#### Sources: 

<b>1.REST API calls that allow anonymous access to <a href = "https://docs.cloud.coinbase.com/sign-in-with-coinbase/docs/api-currencies"> Coinbase API </a>. </b>

![image](https://github.com/RemoteDataEngineer/CryptoApiPowerBi/assets/140629527/9483fd7a-e691-4347-9744-270db44fd2a4)

<ul>
<li> GetCoinbaseAPICountries - List of Countries from API call to Coinbase API </li>
<li> GetCoinbaseAPICryptoCurrencies - List of Crypto Currencies from API call to Coinbase API</li>
<li> GetCoinbaseAPICurrencies - List of Traditional Currencies from API call to Coinbase API</li>
<li> GetCoinbaseAPIExchangeRates - List of Currency Exchange Rates</li>
</ul>

<b> 2. Imported Data to minimize refresh times for this Proof of Concept. Importing this data made the file huge but it won't keep end users waiting to view a POC Dashboard </b>
   
![image](https://github.com/RemoteDataEngineer/CryptoApiPowerBi/assets/140629527/5c8c762b-3360-4224-9c7a-77f3c4c8c63e)

<ul>
<li> Readin_GeoNames_allCountries - List of Countries along with their latitude and longitude </li>
<li> ReadInCountry - List of Currencies by Nation API</li>
<li> ReadInGlossary - Glossary of Terms used in this dashboard</li>
</ul>

#### Transformation: 
<ul>
<li> DateDimension = Calendar( Date(2015, 1, 1), Date(2030,12,31))</li>
<li> DimCurrencies = 
UNION(
    SELECTCOLUMNS(GetCoinbaseAPICryptoCurrencies, "Currency Type", GetCoinbaseAPICryptoCurrencies[CurrencyType], "Currency Name", GetCoinbaseAPICryptoCurrencies[CurrencyName], "Currency Code", GetCoinbaseAPICryptoCurrencies[CurrencyCode]),
    SELECTCOLUMNS(GetCoinbaseAPICurrencies, "Currency Type", GetCoinbaseAPICurrencies[CurrencyType], "Currency Name", GetCoinbaseAPICurrencies[CurrencyName], "Currency Code", GetCoinbaseAPICurrencies[CurrencyCode])
)s</li>
<li> DimBridgeCurrenciesCountries = 
    SUMMARIZE(
        DimCurrencies,
        [Currency Code],       
	    "Currency Name",      MAXX(FILTER(DimCurrencies,          [Currency Code] = EARLIER([Currency Code])), [Currency Name]),
        "Currency Type",      MAXX(FILTER(DimCurrencies,          [Currency Code] = EARLIER([Currency Code])), [Currency Type]),
        "2CharCountryCode",   MAXX(FILTER(ReadInCountryCurrency,  [Currency Code] = EARLIER([Currency Code])), [2CharCountryCode]),
        "Country",            MAXX(FILTER(ReadInCountryCurrency,  [Currency Code] = EARLIER([Currency Code])), [Country])
    )
</li>
<li> DimBridgeCurrenciesCountriesCoordinates = 
    SUMMARIZE(
        DimBridgeCurrenciesCountries,
        [Currency Code], [Currency Name], [Currency Type], [2CharCountryCode],     [Country],       
        "Latitude",           MAXX(FILTER(ReadIn_GeoNames_allCountries,  [CountryGeoCode] = EARLIER([2CharCountryCode])), [Latitude]),
        "Longitude",            MAXX(FILTER(ReadIn_GeoNames_allCountries,  [CountryGeoCode] = EARLIER([2CharCountryCode])), [Longitude])
    )
</li>
</ul>

<b> 3. Dashboard </b>
   
 
   ![image](https://github.com/RemoteDataEngineer/CryptoApiPowerBi/assets/140629527/d1bb27df-fad8-4dec-9038-254f4ff926fa)

   Includes links for hiring me and to different tabs on the dashboard. Press Ctrl + Mouse Click to follow links.
   

  ![image](https://github.com/RemoteDataEngineer/CryptoApiPowerBi/assets/140629527/b4a3fe45-b609-4a96-8997-5d6113628387)
  <ol type = "a">
  <li> Navigation - Links back to first tab with Navigation Links </li>
   <li> Country Name Slicer - Select multiple countries to compare exchange rates with the American Dollar</li>
   <li> Currency Name Slicer - Filter down different currencies</li>
   <li> Exchange Rate Slide - Slide to filter down to currencies at a certain rate compared to the American Dollar</li>
   <li> Currency Type - Click either Traditional or crypto to narrow down the reports to one currency type or another</li>
   <li> Glossary - Links to Glossary tab</li>
   <li> Exchange Rate by Country - Click on a bubble to narrow down report to geographical location clicked</li>
   <li> Exchange Rates - Table of Exchange Rates compared to the American Dollar</li>
</ol>


  ![image](https://github.com/RemoteDataEngineer/CryptoApiPowerBi/assets/140629527/4d0daaec-2a13-4c3b-b284-0da414b4a3d9)
  Glossary listing for all terms used in Dashboard

<b> 4. Troubleshooting </b>
<ol type = "a">
  <li> Press the refresh button if you don't see any data </li>
  <li> All the bubbles on the map appear to be the same size - Use the slider to change the beginning range to a value greater than 0.1 </li>
</ol>
