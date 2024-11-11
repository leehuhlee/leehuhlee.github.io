---
layout: post
title: "[C#, React] Finshark"
date: 2024-10-18
excerpt: "React"
tags: [C#, React]
comments: false
---

# React Install
- create `FinShark` folder

### Required
* npm
- download <a href="https://nodejs.org/en">node.js<a>
- restart your computer
- run `npm` on visual studio code

<figure>
  <a href="/assets/img/posts/react_finshark/0.jpg"><img src="/assets/img/posts/react_finshark/0.jpg"></a>
	<figcaption>React Install</figcaption>
</figure>

* React
- run `npm install create-react-app` to create react app

<figure>
  <a href="/assets/img/posts/react_finshark/1.jpg"><img src="/assets/img/posts/react_finshark/1.jpg"></a>
	<figcaption>React Install</figcaption>
</figure>

- run `npx create-react-app frontend --template typescript` to create react frontend

<figure class="half">
  <a href="/assets/img/posts/react_finshark/2.jpg"><img src="/assets/img/posts/react_finshark/2.jpg"></a>
  <a href="/assets/img/posts/react_finshark/3.jpg"><img src="/assets/img/posts/react_finshark/3.jpg"></a>
	<figcaption>React Install</figcaption>
</figure>

- open extensions in visual studio code
- download `ES7+ React/Redux/React-Native snippets` to use react snippets

<figure>
  <a href="/assets/img/posts/react_finshark/4.jpg"><img src="/assets/img/posts/react_finshark/4.jpg"></a>
	<figcaption>React Install</figcaption>
</figure>

# JSX(JavaScript XML)
- a syntax extension for JavaScript primatily used in React.
- allows to write HTML-like code directly within JavaScript.

### Image
- create `assets` folder in src folder
- create `image` folder in assets folder
- download any iphone image and save it in image folder
- name it to `iphone.jpg`

<figure>
  <a href="/assets/img/posts/react_finshark/6.jpg"><img src="/assets/img/posts/react_finshark/6.jpg"></a>
	<figcaption>JSX</figcaption>
</figure>

### Card
- create `components` folder in src folder
- create `Card` folder in components
- create `Card.tsx` and `Card.css` files in card folder

<figure>
  <a href="/assets/img/posts/react_finshark/5.jpg"><img src="/assets/img/posts/react_finshark/5.jpg"></a>
	<figcaption>JSX</figcaption>
</figure>

* Card.tsx
- type `tsrafce` to use snippets

{% highlight tsx %}
import iphone from '../../assets/images/iphone.jpg'
import './Card.css'

type Props = {}

const Card = (props: Props) => {
  return (
    <div className='card'>
        <img 
            src={iphone}
            alt='image'
        />
        <div className='details'>
            <h2>AAPL</h2>
            <p>$110</p>
        </div>
        <p className='info'>Lorem ipsum, dolor sit amet consectetur adipisicing elit. Sequi, ex.</p>
    </div>
  )
}

export default Card
{% endhighlight %}

### App
- `App.tsx` and `App.css` are already in src folder

* App.tsx
{% highlight tsx %}
import './App.css';
import Card from './components/Card/Card';

function App() {
  return (
    <div className="App">
      <Card/>
    </div>
  );
}

export default App;
{% endhighlight %}

* App.css
{% highlight css %}
.card {
    width: 350px;
    height: 450px;
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    padding: 50px;
    border-radius: 10px;
    overflow: hidden;
    box-shadow: rgba(50, 50, 93, 0.25) 0px 6px 12px -2px,
      rgba(0, 0, 0, 0.3) 0px 3px 7px -3px;
  }
  
  .card img {
    width: 180px;
    height: 180px;
    border-radius: 50%;
    margin: 5px;
  }
  
  .card .details {
    margin: 10px;
  }
  
  .card .details h2 {
    font-weight: 600;
  }
  
  .card .details p {
    text-transform: uppercase;
    font-weight: 300;
  }
{% endhighlight %}

## Run
- type `cd frontend` in terminal
- type `npm run start`

<figure class="half">
  <a href="/assets/img/posts/react_finshark/7.jpg"><img src="/assets/img/posts/react_finshark/7.jpg"></a>
  <a href="/assets/img/posts/react_finshark/8.jpg"><img src="/assets/img/posts/react_finshark/8.jpg"></a>
	<figcaption>JSX</figcaption>
</figure>

# Props
- means properties.
- are used to pass data from one component to another; [Data Flow](#data-flow).
- are `read-only`.

## Interface vs Type
- In TypeScript, both `interface` and `type` are used to define the structure of objects

* interface
- Extensibility: interfaces can be extended using the `extends` keyword to create more complex types.
- Declaration merging: If there is more than two interfaces with same name, TypeScript will merge them together.

* type
- Cannot extend: type cannot be extended.
- No delaration merging: If there is more than two types with same name, it occurs error.

### CardList
- create `CardList` folder in components folder
- create `CardList.tsx` and `CardList.css` files in CardList folder

* CardList.tsx
{% highlight tsx %}
import Card from '../Card/Card'

interface Props {}

const CardList = (props: Props) => {
  return (
    <div>
        <Card companyName='Apple' ticker='APPL' price={100}/>
        <Card companyName='Microsoft' ticker='MSFT' price={200}/>
        <Card companyName='Tesla' ticker='TSLA' price={300}/>
    </div>
  )
}

export default CardList
{% endhighlight %}

* Card.tsx
{% highlight tsx %}
import iphone from '../../assets/images/iphone.jpg'

interface Props {
  companyName: string;
  ticker: string;
  price: number;
}

const Card = ({ companyName, ticker, price }: Props) => {
  return (
    <div className='card'>
        <img 
            src={iphone}
            alt='image'
        />
        <div className='details'>
            <h2>{companyName} ({ticker})</h2>
            <p>${price}</p>
        </div>
        <p className='info'>Lorem ipsum, dolor sit amet consectetur adipisicing elit. Sequi, ex.</p>
    </div>
  )
}

export default Card
{% endhighlight %}

### App

* App.tsx
{% highlight tsx %}
import './App.css';
import CardList from './components/CardList/CardList';

function App() {
  return (
    <div className="App">
      <CardList/>
    </div>
  );
}

export default App;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/9.jpg"><img src="/assets/img/posts/react_finshark/9.jpg"></a>
	<figcaption>Props</figcaption>
</figure>

# Function Components

## React.FC
- React Function Component
- Automatic typing of childern: React.FC component can implicitly accept children.
- Prop types enforcement: React.FC ensures that props passed to the component match the specified interface.

## JSX.Element
- `JSX.Element` is the standard return type for functional components in React that use JSX to render UI elements
- `JSX.Element` is essentially the type definition for a React element, such as an HTML tag, a React component, or a fragment

### Card

* Card.tsx
{% highlight tsx %}
import iphone from '../../assets/images/iphone.jpg'

interface Props {
  companyName: string;
  ticker: string;
  price: number;
}

const Card: React.FC<Props> = ({ companyName, ticker, price }: Props): JSX.Element => {
  return (
    <div className='card'>
        <img 
            src={iphone}
            alt='image'
        />
        <div className='details'>
            <h2>{companyName} ({ticker})</h2>
            <p>${price}</p>
        </div>
        <p className='info'>Lorem ipsum, dolor sit amet consectetur adipisicing elit. Sequi, ex.</p>
    </div>
  )
}

export default Card
{% endhighlight %}

### CardList

* CardList.tsx
{% highlight tsx %}
import Card from '../Card/Card'

interface Props {}

const CardList: React.FC<Props> = (props: Props): JSX.Element => {
  return (
    <div>
        <Card companyName='Apple' ticker='APPL' price={100}/>
        <Card companyName='Microsoft' ticker='MSFT' price={200}/>
        <Card companyName='Tesla' ticker='TSLA' price={300}/>
    </div>
  )
}

export default CardList
{% endhighlight %}

# Hook
- React hooks are special function that enable stateful logic and side-effects in functional components.
- Hooks allow to use features like state and lifecycle events without converting the component to a class component.

## useState
- a function that allows to update the state value: initialization and updating.

### Search
- create `Search` folder in components folter
- create `Search.tsx` and `Search.css` in search folder

* Search.tsx
{% highlight tsx %}
import React, { useState } from 'react'

type Props = {}

const Search : React.FC<Props> = (props: Props): JSX.Element => {
  const [search, setSearch] = useState<string>("");
  const onClick = (e: any) => {
    setSearch(e.target.value);
    console.log(e);
  };

  return (
    <div>
        <input value={search} onChange={(e) => onClick(e)}/>
    </div>
  )
}

export default Search
{% endhighlight %}

### App

* App.tsx
{% highlight tsx %}
import './App.css';
import CardList from './components/CardList/CardList';
import Search from './components/Search/Search';

function App() {
  return (
    <div className="App">
      <Search/>
      <CardList/>
    </div>
  );
}

export default App;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/10.jpg"><img src="/assets/img/posts/react_finshark/10.jpg"></a>
	<figcaption>Hook</figcaption>
</figure>

# Event Handler

## ChangeEvent
- `ChangeEvent` helps strongly type the event object for form elements such as `<input>`, `<textarea>`, or `<select>`.
- `ChangeEvent` ensures that TypeScript can provice autocomplete and type safety when accessing event properties like `event.target.value`.

## SyntheticEvent
- Different browsers handle slightly diffrently, but `SyntheticEvent` abstracts those differences. React can be worked with a consistent event object across all browers.
- React pools event objects to improve performance. Instead of creating a new event object for every event, React reuses them, reducing memory consumption.

### Search

* Search.tsx
{% highlight tsx %}
import React, { ChangeEvent, useState, SyntheticEvent } from 'react'

type Props = {}

const Search : React.FC<Props> = (props: Props): JSX.Element => {
  const [search, setSearch] = useState<string>("");
  const handlechange = (e: ChangeEvent<HTMLInputElement>) => {
    setSearch(e.target.value);
    console.log(e);
  };
  const onClick = (e: SyntheticEvent) => {
    console.log(e);
  };

  return (
    <div>
        <input value={search} onChange={(e) => handlechange(e)}/>
        <button onClick={(e) => onClick(e)}/>
    </div>
  )
}

export default Search
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/11.jpg"><img src="/assets/img/posts/react_finshark/11.jpg"></a>
	<figcaption>Event Handler</figcaption>
</figure>

# Modeling

## axios
- returns promises, making it easy to work with asynchronous code using `.then()` and `.catch()`, or with `async/await`.
- can make requests using `get`, `post`, `put` and `delete`.
- provides a straightforward mechanism to handle errors, such as failed requests or responses with error status codes.

## dotenv
- allows to define environment variables in a `.env` file and load them into `process.env`, making them accessible within your Node.js application.
- keeps sensitive information, such as API keys or database passwords, out of your codebase.

### Required
- type `npm install axios --save` in terminal
- type `npm install --save-dev @types/axios` in terminal
- type `npm install dotenv --save` in terminal

### Environment
- open <a href="https://site.financialmodelingprep.com/">financial modeling prep</a> to get your api key.

* .env
- create `.env` file on frontend folder
- open `.env` file and type `REACT_APP_API_KEY=` with your api key.

### Company Model
- create `company.d.tsx` file on src folder

* company.d.tsx
{% highlight tsx %}
export interface CompanySearch {
    currency: string;
    exchangeShortName: string;
    name: string;
    stockExchange: string;
    symbol: string;
  }
  
  export interface CompanyProfile {
    symbol: string;
    price: number;
    beta: number;
    volAvg: number;
    mktCap: number;
    lastDiv: number;
    range: string;
    changes: number;
    companyName: string;
    currency: string;
    cik: string;
    isin: string;
    exchange: string;
    exchangeShortName: string;
    industry: string;
    website: string;
    description: string;
    ceo: string;
    sector: string;
    counter: string;
    fullTimeEmployees: string;
    phone: string;
    address: string;
    city: string;
    state: string;
    zip: string;
    dcfDiff: number;
    dcf: number;
    image: string;
    ipoDate: string;
    defaultImage: boolean;
    isEtf: boolean;
    isActivelyTrading: boolean;
    isAdr: boolean;
    isFund: boolean;
  }
  
  export interface CompanyKeyRatios {
    dividendYielTTM: number;
    dividendYielPercentageTTM: number;
    peRatioTTM: number;
    pegRatioTTM: number;
    payoutRatioTTM: number;
    currentRatioTTM: number;
    quickRatioTTM: number;
    cashRatioTTM: number;
    daysOfSalesOutstandingTTM: number;
    daysOfInventoryOutstandingTTM: number;
    operatingCycleTTM: number;
    daysOfPayablesOutstandingTTM: number;
    cashConversionCycleTTM: number;
    grossProfitMarginTTM: number;
    operatingProfitMarginTTM: number;
    pretaxProfitMarginTTM: number;
    netProfitMarginTTM: number;
    effectiveTaxRateTTM: number;
    returnOnAssetsTTM: number;
    returnOnEquityTTM: number;
    returnOnCapitalEmployedTTM: number;
    netIncomePerEBTTTM: number;
    ebtPerEbitTTM: number;
    ebitPerRevenueTTM: number;
    debtRatioTTM: number;
    debtEquityRatioTTM: number;
    longTermDebtToCapitalizationTTM: number;
    totalDebtToCapitalizationTTM: number;
    interestCoverageTTM: number;
    cashFlowToDebtRatioTTM: number;
    companyEquityMultiplierTTM: number;
    receivablesTurnoverTTM: number;
    payablesTurnoverTTM: number;
    inventoryTurnoverTTM: number;
    fixedAssetTurnoverTTM: number;
    assetTurnoverTTM: number;
    operatingCashFlowPerShareTTM: number;
    freeCashFlowPerShareTTM: number;
    cashPerShareTTM: number;
    operatingCashFlowSalesRatioTTM: number;
    freeCashFlowOperatingCashFlowRatioTTM: number;
    cashFlowCoverageRatiosTTM: number;
    shortTermCoverageRatiosTTM: number;
    capitalExpenditureCoverageRatioTTM: number;
    dividendPaidAndCapexCoverageRatioTTM: number;
    priceBookValueRatioTTM: number;
    priceToBookRatioTTM: number;
    priceToSalesRatioTTM: number;
    priceEarningsRatioTTM: number;
    priceToFreeCashFlowsRatioTTM: number;
    priceToOperatingCashFlowsRatioTTM: number;
    priceCashFlowRatioTTM: number;
    priceEarningsToGrowthRatioTTM: number;
    priceSalesRatioTTM: number;
    dividendYieldTTM: number;
    enterpriseValueMultipleTTM: number;
    priceFairValueTTM: number;
    dividendPerShareTTM: number;
  }
  
  export interface CompanyIncomeStatement {
    date: string;
    symbol: string;
    reportedCurrency: string;
    cik: string;
    fillingDate: string;
    acceptedDate: string;
    calendarYear: string;
    period: string;
    revenue: number;
    costOfRevenue: number;
    grossProfit: number;
    grossProfitRatio: number;
    researchAndDevelopmentExpenses: number;
    generalAndAdministrativeExpenses: number;
    sellingAndMarketingExpenses: number;
    sellingGeneralAndAdministrativeExpenses: number;
    otherExpenses: number;
    operatingExpenses: number;
    costAndExpenses: number;
    interestIncome: number;
    interestExpense: number;
    depreciationAndAmortization: number;
    ebitda: number;
    ebitdaratio: number;
    operatingIncome: number;
    operatingIncomeRatio: number;
    totalOtherIncomeExpensesNet: number;
    incomeBeforeTax: number;
    incomeBeforeTaxRatio: number;
    incomeTaxExpense: number;
    netIncome: number;
    netIncomeRatio: number;
    eps: number;
    epsdiluted: number;
    weightedAverageShsOut: number;
    weightedAverageShsOutDil: number;
    link: string;
    finalLink: string;
  }
  
  export interface CompanyBalanceSheet {
    date: string;
    symbol: string;
    reportedCurrency: string;
    cik: string;
    fillingDate: string;
    acceptedDate: string;
    calendarYear: string;
    period: string;
    cashAndCashEquivalents: number;
    shortTermInvestments: number;
    cashAndShortTermInvestments: number;
    netReceivables: number;
    inventory: number;
    otherCurrentAssets: number;
    totalCurrentAssets: number;
    propertyPlantEquipmentNet: number;
    goodwill: number;
    intangibleAssets: number;
    goodwillAndIntangibleAssets: number;
    longTermInvestments: number;
    taxAssets: number;
    otherNonCurrentAssets: number;
    totalNonCurrentAssets: number;
    otherAssets: number;
    totalAssets: number;
    accountPayables: number;
    shortTermDebt: number;
    taxPayables: number;
    deferredRevenue: number;
    otherCurrentLiabilities: number;
    totalCurrentLiabilities: number;
    longTermDebt: number;
    deferredRevenueNonCurrent: number;
    deferredTaxLiabilitiesNonCurrent: number;
    otherNonCurrentLiabilities: number;
    totalNonCurrentLiabilities: number;
    otherLiabilities: number;
    capitalLeaseObligations: number;
    totalLiabilities: number;
    preferredStock: number;
    commonStock: number;
    retainedEarnings: number;
    accumulatedOtherComprehensiveIncomeLoss: number;
    othertotalStockholdersEquity: number;
    totalStockholdersEquity: number;
    totalEquity: number;
    totalLiabilitiesAndStockholdersEquity: number;
    minorityInterest: number;
    totalLiabilitiesAndTotalEquity: number;
    totalInvestments: number;
    totalDebt: number;
    netDebt: number;
    link: string;
    finalLink: string;
  }
  
  export interface CompanyCashFlow {
    date: string;
    symbol: string;
    reportedCurrency: string;
    cik: string;
    fillingDate: string;
    acceptedDate: string;
    calendarYear: string;
    period: string;
    netIncome: number;
    depreciationAndAmortization: number;
    deferredIncomeTax: number;
    stockBasedCompensation: number;
    changeInWorkingCapital: number;
    accountsReceivables: number;
    inventory: number;
    accountsPayables: number;
    otherWorkingCapital: number;
    otherNonCashItems: number;
    netCashProvidedByOperatingActivities: number;
    investmentsInPropertyPlantAndEquipment: number;
    acquisitionsNet: number;
    purchasesOfInvestments: number;
    salesMaturitiesOfInvestments: number;
    otherInvestingActivites: number;
    netCashUsedForInvestingActivites: number;
    debtRepayment: number;
    commonStockIssued: number;
    commonStockRepurchased: number;
    dividendsPaid: number;
    otherFinancingActivites: number;
    netCashUsedProvidedByFinancingActivities: number;
    effectOfForexChangesOnCash: number;
    netChangeInCash: number;
    cashAtEndOfPeriod: number;
    cashAtBeginningOfPeriod: number;
    operatingCashFlow: number;
    capitalExpenditure: number;
    freeCashFlow: number;
    link: string;
    finalLink: string;
  }
{% endhighlight %}

### API
- create `api.tsx` file on src folder

* api.tsx
{% highlight tsx %}
import axios from "axios";
import { CompanySearch } from "./company";

interface SearchResponse {
  data: CompanySearch[];
}

export const searchCompanies = async (query: string) => {
  try {
    const data = await axios.get<SearchResponse>(
      `https://financialmodelingprep.com/api/v3/search?query=${query}&apikey=${process.env.REACT_APP_API_KEY}`
    );
    return data;
  } catch (error) {
    if (axios.isAxiosError(error)) {
      console.log("error message: ", error.message);
      return error.message;
    } else {
      console.log("unexpected error: ", error);
      return "Unxpected error has occured."
    }
  }
}
{% endhighlight %}

### index

* index.tsx
{% highlight tsx %}
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import { searchCompanies } from './api';

const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);

console.log(searchCompanies("tsla"));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/15.jpg"><img src="/assets/img/posts/react_finshark/15.jpg"></a>
	<figcaption>Modeling</figcaption>
</figure>

# Data Flow
- [Props](#props)

### App

* App.tsx
{% highlight tsx %}
import { ChangeEvent, SyntheticEvent, useState } from 'react';
import './App.css';
import CardList from './components/CardList/CardList';
import Search from './components/Search/Search';

function App() {
  const [search, setSearch] = useState<string>("");
  const handlechange = (e: ChangeEvent<HTMLInputElement>) => {
    setSearch(e.target.value);
    console.log(e);
  };
  const onClick = (e: SyntheticEvent) => {
    console.log(e);
  };

  return (
    <div className="App">
      <Search onClick={onClick} search={search} handleChange={handlechange}/>
      <CardList/>
    </div>
  );
}

export default App;
{% endhighlight %}

### Search

* Search.tsx
{% highlight tsx %}
import React, { ChangeEvent, FormEvent, SyntheticEvent } from 'react'

interface Props {
  onClick: (e: SyntheticEvent) => void;
  search: string | undefined;
  handleChange: (e: ChangeEvent<HTMLInputElement>) => void;
}

const Search : React.FC<Props> = ({ onClick, search, handleChange }: Props): JSX.Element => {
  return (
    <div>
      <input value={search} onChange={(e) => handleChange(e)}/>
      <button onClick={(e) => onClick(e)}/>
    </div>
  )
}

export default Search
{% endhighlight %}

# Type Narrowing
- for type checking with `if-else`.
- when type is different with the expectation, it occurs error.

### App

* App.tsx
{% highlight tsx %}
import { ChangeEvent, SyntheticEvent, useState } from 'react';
import './App.css';
import CardList from './components/CardList/CardList';
import Search from './components/Search/Search';
import { CompanySearch } from './company';
import { searchCompanies } from './api';

function App() {
  const [search, setSearch] = useState<string>("");
  const [searchResult, setSearchResult] = useState<CompanySearch[]>([]);
  const [serverError, setServerError] = useState<string>("");

  const handlechange = (e: ChangeEvent<HTMLInputElement>) => {
    setSearch(e.target.value);
    console.log(e);
  };
  const onClick = async (e: SyntheticEvent) => {
    const result = await searchCompanies(search);
    if (typeof result === "string") {
      setServerError(result);
    } else if (Array.isArray(result.data)){
      setSearchResult(result.data);
    }
    console.log(searchResult);
  };

  return (
    <div className="App">
      <Search onClick={onClick} search={search} handleChange={handlechange}/>
      <CardList/>
    </div>
  );
}

export default App;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/16.jpg"><img src="/assets/img/posts/react_finshark/16.jpg"></a>
	<figcaption>Type Narrowing</figcaption>
</figure>

# Conditional Rendering
- `A === B ? C : D` renders C when it is true, D when it is false.
- `A === B ?? C` renders C when it is true

### App

* App.tsx
{% highlight tsx %}
...
  return (
    <div className="App">
      <Search onClick={onClick} search={search} handleChange={handlechange}/>
      {serverError && <h1>{serverError}</h1>}
      <CardList/>
    </div>
  );
}

export default App;
{% endhighlight %}

# map()
- does not modify the original array.
- creates a new array based on the transformation applied to each element.
- is often used for iterating over arrays to transform or process each item.

## uuid(Universally Unique Identifier)
- is typically represented as a 32-character hexadecimal string, separated by hyphens into five groups.(8-4-4-4-12)

### Required
- type `npm install uuid` in terminal
- type `npm install --save-dev @types/uuid` in terminal

### App

* App.tsx
{% highlight tsx %}
...
return (
    <div className="App">
      <Search onClick={onClick} search={search} handleChange={handlechange}/>
      <CardList searchResults={searchResult}/>
      {serverError && <h1>{serverError}</h1>}
    </div>
  );
{% endhighlight %}

### CardList
{% highlight tsx %}
import { CompanySearch } from '../../company'
import Card from '../Card/Card'
import { v4 as uuidv4 } from "uuid";

interface Props {
  searchResults: CompanySearch[];
}

const CardList: React.FC<Props> = ( { searchResults }: Props): JSX.Element => {
  return (
    <>
      {searchResults.length > 0 ? (
        searchResults.map((result) => {
          return <Card id={result.symbol} key={uuidv4()} searchResult={result}/>
        })
      ) : (<h1>No results</h1>)}
    </>
  )
}

export default CardList
{% endhighlight %}

### Card
{% highlight tsx %}
import { CompanySearch } from '../../company';

interface Props {
  id: string;
  searchResult: CompanySearch;
}

const Card: React.FC<Props> = ({ id, searchResult }: Props): JSX.Element => {
  return (
    <div className='card'>
      <img alt='company logo'/>
      <div className='details'>
        <h2>{searchResult.name} ({searchResult.symbol})</h2>
        <p>{searchResult.currency}</p>
      </div>
      <p className='info'>{searchResult.exchangeShortName} - {searchResult.stockExchange}</p>
    </div>
  )
}

export default Card
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/12.jpg"><img src="/assets/img/posts/react_finshark/12.jpg"></a>
	<figcaption>Lists</figcaption>
</figure>

# Forms

### Portfolio
- create `Portfolio` folder in components folder
- create `AddPortfolio` folder in Portfolio folder
- create `AddPortfolio.tsx` and `AddPortfolio.css` in AddPortfolio folder

* AddPortfolio.tsx
{% highlight tsx %}
import React, { SyntheticEvent } from 'react'

interface Props {
  onPortfolioCreate: (e: SyntheticEvent) => void;
  symbol: string;
}

const AddPortfolio: React.FC<Props> = ({ onPortfolioCreate, symbol }: Props): JSX.Element => {
  return (
    <form onSubmit={onPortfolioCreate}>
      <input readOnly={true} hidden={true} value={symbol}/>
      <button type='submit'>Add</button>
    </form>
  )
}

export default AddPortfolio
{% endhighlight %}

### Card

* Card.tsx
{% highlight tsx %}
import { SyntheticEvent } from 'react';
import { CompanySearch } from '../../company';
import AddPortfolio from '../Portfolio/AddPortfolio/AddPortfolio';

interface Props {
  id: string;
  searchResult: CompanySearch;
  onPortfolioCreate: (e: SyntheticEvent) => void;
}

const Card: React.FC<Props> = ({ id, searchResult, onPortfolioCreate }: Props): JSX.Element => {
  return (
    <div className='card'>
      <img alt='company logo'/>
      <div className='details'>
        <h2>{searchResult.name} ({searchResult.symbol})</h2>
        <p>{searchResult.currency}</p>
      </div>
      <p className='info'>{searchResult.exchangeShortName} - {searchResult.stockExchange}</p>
      <AddPortfolio onPortfolioCreate={onPortfolioCreate} symbol={searchResult.symbol}/>
    </div>
  )
}

export default Card
{% endhighlight %}

### CardList

* CardList.tsx
{% highlight tsx %}
import { SyntheticEvent } from 'react';
import { CompanySearch } from '../../company'
import Card from '../Card/Card'
import { v4 as uuidv4 } from "uuid";

interface Props {
  searchResults: CompanySearch[];
  onPortfolioCreate: (e: SyntheticEvent) => void;
}

const CardList: React.FC<Props> = ( { searchResults, onPortfolioCreate }: Props): JSX.Element => {
  return (
    <>
      {searchResults.length > 0 ? (
        searchResults.map((result) => {
          return <Card id={result.symbol} key={uuidv4()} searchResult={result} onPortfolioCreate={onPortfolioCreate}/>
        })
      ) : (<h1>No results</h1>)}
    </>
  )
}

export default CardList
{% endhighlight %}

### App

* App.tsx
{% highlight tsx %}
import { ChangeEvent, SyntheticEvent, useState } from 'react';
import './App.css';
import CardList from './components/CardList/CardList';
import Search from './components/Search/Search';
import { CompanySearch } from './company';
import { searchCompanies } from './api';

function App() {
  const [search, setSearch] = useState<string>("");
  const [searchResult, setSearchResult] = useState<CompanySearch[]>([]);
  const [serverError, setServerError] = useState<string>("");

  const handleSearchChange = (e: ChangeEvent<HTMLInputElement>) => {
    setSearch(e.target.value);
    console.log(e);
  };
  const onSearchClick = async (e: SyntheticEvent) => {
    e.preventDefault();

    const result = await searchCompanies(search);
    if (typeof result === "string") {
      setServerError(result);
    } else if (Array.isArray(result.data)){
      setSearchResult(result.data);
    }
    console.log(searchResult);
  };
  const onPortfolioCreate = (e: SyntheticEvent) => {
    e.preventDefault();
    console.log(e);
  }

  return (
    <div className="App">
      <Search onSearchClick={onSearchClick} search={search} handleSearchChange={handleSearchChange}/>
      <CardList searchResults={searchResult} onPortfolioCreate={onPortfolioCreate}/>
      {serverError && <h1>{serverError}</h1>}
    </div>
  );
}

export default App;
{% endhighlight %}

### Search

* Search.tsx
{% highlight tsx %}
import React, { ChangeEvent, FormEvent, SyntheticEvent } from 'react'

interface Props {
  onSearchClick: (e: SyntheticEvent) => void;
  search: string | undefined;
  handleSearchChange: (e: ChangeEvent<HTMLInputElement>) => void;
}

const Search : React.FC<Props> = ({ onSearchClick, search, handleSearchChange }: Props): JSX.Element => {
  return (
    <>
      <form onSubmit={onSearchClick}>
        <input value={search} onChange={handleSearchChange}/>
        <button type='submit'/>
      </form>
    </>
  )
}

export default Search
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/13.jpg"><img src="/assets/img/posts/react_finshark/13.jpg"></a>
	<figcaption>Forms</figcaption>
</figure>

# Arrays

## preventDefault()
- stops form submission to handle it with JavaScript(e.g. forms, links and etc)
- is a method used to prevent the default behavior of an event

## ...
- creates a copy without modifying the original
- combines multiple arrays into a single array
- adds new elements to an array at the beginning, middle, or end
- passes array elements as individual arguments to functions
- captures the first elements or the rest of an array using destructuring.

### CardPortfolio
- create `CardPortfolio` in Portfoilo folder
- create `CardPortfolio.tsx` and `CardPortfolio.css` in CardPortfolio folder

* CardPortfolio.tsx
{% highlight tsx %}
import React from 'react'

interface Props {
  portfolioValues: string;
}

const CardPortfolio: React.FC<Props> = ({ portfolioValues }: Props): JSX.Element => {
  return (
    <div>
      <h4>{portfolioValues}</h4>
      <button>X</button>
    </div>
  )
}

export default CardPortfolio
{% endhighlight %}

### ListPortfolio

* ListPortfolio.tsx
{% highlight tsx %}
import React from 'react'
import CardPortfolio from '../CardPortfolio/CardPortfolio';

interface Props {
  portfolioValues: string[];
}

const ListPortfolio: React.FC<Props> = ({ portfolioValues }: Props): JSX.Element => {
  return (
    <>
      <h3>My Portfolio</h3>
      <ul>
        {portfolioValues && 
          portfolioValues.map((portfolioValues) => {return <CardPortfolio portfolioValues={portfolioValues}/>;})
        }
      </ul>
    </>
  )
}

export default ListPortfolio
{% endhighlight %}

### App

* App.tsx
{% highlight tsx %}
...
  const onPortfolioCreate = (e: any) => {
    e.preventDefault();
    
    const exists = portfolioValues.find((value) => value === e.target[0].value);
    if (exists) return;
    
    const updatedPortfolio = [...portfolioValues, e.target[0].value]
    setPortfolioValues(updatedPortfolio);
  }

  return (
    <div className="App">
      <Search onSearchClick={onSearchClick} search={search} handleSearchChange={handleSearchChange}/>
      <ListPortfolio portfolioValues={portfolioValues}/>
      <CardList searchResults={searchResult} onPortfolioCreate={onPortfolioCreate}/>
      {serverError && <h1>{serverError}</h1>}
    </div>
  );
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/27.jpg"><img src="/assets/img/posts/react_finshark/27.jpg"></a>
	<figcaption>Arrays</figcaption>
</figure>

# Delete

### App

* App.tsx
{% highlight tsx %}
...
  const onPortfolioDelete = (e: any) => {
    e.preventDefault();
    
    const removed = portfolioValues.filter((value) => {
      return value !== e.target[0].value;
    });
    setPortfolioValues(removed);
  }

  return (
    <div className="App">
      <Search onSearchClick={onSearchClick} search={search} handleSearchChange={handleSearchChange}/>
      <ListPortfolio portfolioValues={portfolioValues} onPortfolioDelete={onPortfolioDelete}/>
      <CardList searchResults={searchResult} onPortfolioCreate={onPortfolioCreate}/>
      {serverError && <h1>{serverError}</h1>}
    </div>
  );
{% endhighlight %}

### ListPortfolio

* ListPortfolio.tsx
{% highlight tsx %}
import React, { SyntheticEvent } from 'react'
import CardPortfolio from '../CardPortfolio/CardPortfolio';

interface Props {
  portfolioValues: string[];
  onPortfolioDelete: (e: SyntheticEvent) => void;
}

const ListPortfolio: React.FC<Props> = ({ portfolioValues, onPortfolioDelete }: Props): JSX.Element => {
  return (
    <>
      <h3>My Portfolio</h3>
      <ul>
        {portfolioValues && 
          portfolioValues.map((portfolioValue) => {return <CardPortfolio portfolioValue={portfolioValue} onPortfolioDelete={onPortfolioDelete}/>;})
        }
      </ul>
    </>
  )
}

export default ListPortfolio
{% endhighlight %}

### CardPortfolio

* CardPortfolio.tsx
{% highlight tsx %}
import React, { SyntheticEvent } from 'react'
import DeletePortfolio from '../DeletePortfolio/DeletePortfolio';

interface Props {
  portfolioValue: string;
  onPortfolioDelete: (e: SyntheticEvent) => void;
}

const CardPortfolio: React.FC<Props> = ({ portfolioValue, onPortfolioDelete }: Props): JSX.Element => {
  return (
    <div>
      <h4>{portfolioValue}</h4>
      <DeletePortfolio portfolioValue={portfolioValue} onPortfolioDelete={onPortfolioDelete} />
    </div>
  )
}

export default CardPortfolio
{% endhighlight %}

### DeletePortfolio
- create `DeletePortfolio` folder in Portfoilo folder
- create `DeletePortfolio.tsx` and `DeletePortfolio.css` in DeletePortfolio folder

* DeletePortfolio.tsx
{% highlight tsx %}
import React, { SyntheticEvent } from 'react'

interface Props {
  portfolioValue: string;
  onPortfolioDelete: (e: SyntheticEvent) => void;
}

const DeletePortfolio: React.FC<Props> = ({ portfolioValue, onPortfolioDelete }: Props): JSX.Element => {
  return (
    <div>
      <form onSubmit={onPortfolioDelete}>
        <input hidden={true} value={portfolioValue} />
        <button>X</button>
      </form>
      
    </div>
  )
}

export default DeletePortfolio
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/28.jpg"><img src="/assets/img/posts/react_finshark/28.jpg"></a>
	<figcaption>Delete</figcaption>
</figure>

# Tailwind
- type `npm install -D tailwindcss` in terminal
- type `npx tailwindcss init` in terminal
- check document in <a href="https://tailwindcss.com/docs/installation">tailwindcss</a> 

* tailwind.config.js
{% highlight js %}
module.exports = {
  content: ["./src/**/*.{js,jsx,ts,tsx}"],
  theme: {
    screens: {
      sm: "480px",
      md: "768px",
      lg: "1020px",
      xl: "1440px",
    },
    extend: {
      colors: {
        lightBlue: "hsl(215.02, 98.39%, 51.18%)",
        darkBlue: "hsl(213.86, 58.82%, 46.67%)",
        lightGreen: "hsl(156.62, 73.33%, 58.82%)",
      },
      fontFamily: {
        sans: ["Poppins", "sans-serif"],
      },
      spacing: {
        180: "32rem",
      },
    },
  },
  plugins: [],
};
{% endhighlight %}

* index.css
{% highlight css %}
@tailwind base;
@tailwind components;
@tailwind utilities;
@import url("https://fonts.googleapis.com/css2?family=Poppins&family=Rubik&display=swap");
{% endhighlight %}

### Title
- open `public` folder in frontend folder
- open `index.html` and change `<title>React App</title>` to  `<title>FinShark</title>`

### images
- add `hero.png` and `logo.png` in images folder

### App

* App.tsx
{% highlight tsx %}
...
  return (
    <>
      <Navbar />
      <Search
        onSearchSubmit={onSearchSubmit}
        search={search}
        handleSearchChange={handleSearchChange}
      />
      <ListPortfolio
        portfolioValues={portfolioValues}
        onPortfolioDelete={onPortfolioDelete}
      />
      <CardList
        searchResults={searchResult}
        onPortfolioCreate={onPortfolioCreate}
      />

      {serverError && <div>Unable to connect to API</div>}
    </>
  );
{% endhighlight %}

### Search

* Search.tsx
{% highlight tsx %}
...
const Search : React.FC<Props> = ({ onSearchSubmit, search, handleSearchChange }: Props): JSX.Element => {
  return (
    <section className="relative bg-gray-100">
      <div className="max-w-4xl mx-auto p-6 space-y-6">
        <form
          className="form relative flex flex-col w-full p-10 space-y-4 bg-darkBlue rounded-lg md:flex-row md:space-y-0 md:space-x-3"
          onSubmit={onSearchSubmit}
        >
          <input
            className="flex-1 p-3 border-2 rounded-lg placeholder-black focus:outline-none"
            id="search-input"
            placeholder="Search companies"
            value={search}
            onChange={handleSearchChange}
          ></input>
        </form>
      </div>
    </section>
  )
}
{% endhighlight %}

### ListPortfolio

* ListPortfolio.tsx
{% highlight tsx %}
...
const ListPortfolio: React.FC<Props> = ({ portfolioValues, onPortfolioDelete }: Props): JSX.Element => {
  return (
    <section id="portfolio">
      <h2 className="mb-3 mt-3 text-3xl font-semibold text-center md:text-4xl">
        My Portfolio
      </h2>
      <div className="relative flex flex-col items-center max-w-5xl mx-auto space-y-10 px-10 mb-5 md:px-6 md:space-y-0 md:space-x-7 md:flex-row">
        <>
          {portfolioValues.length > 0 ? (
            portfolioValues.map((portfolioValue) => {
              return (
                <CardPortfolio
                  portfolioValue={portfolioValue}
                  onPortfolioDelete={onPortfolioDelete}
                />
              );
            })
          ) : (
            <h3 className="mb-3 mt-3 text-xl font-semibold text-center md:text-xl">
              Your portfolio is empty.
            </h3>
          )}
        </>
      </div>
    </section>
  )
}
{% endhighlight %}

### DeletePortfolio

* DeletePortfolio.tsx
{% highlight tsx %}
...
const DeletePortfolio: React.FC<Props> = ({ portfolioValue, onPortfolioDelete }: Props): JSX.Element => {
  return (
    <button className="block w-full py-3 text-white duration-200 border-2 rounded-lg bg-red-500 hover:text-red-500 hover:bg-white border-red-500">
      X
    </button>
  )
}
{% endhighlight %}

### CardPortfolio

* CardPortfolio.tsx
{% highlight tsx %}
...
const CardPortfolio: React.FC<Props> = ({ portfolioValue, onPortfolioDelete }: Props): JSX.Element => {
  return (
    <div className="flex flex-col w-full p-8 space-y-4 text-center rounded-lg shadow-lg md:w-1/3">
      <p className="pt-6 text-xl font-bold">{portfolioValue}</p>
      <DeletePortfolio
        portfolioValue={portfolioValue}
        onPortfolioDelete={onPortfolioDelete}
      />
    </div>
  )
}
{% endhighlight %}

### AddPortfolio

* AddPortfolio.tsx
{% highlight tsx %}
...
const AddPortfolio: React.FC<Props> = ({ onPortfolioCreate, symbol }: Props): JSX.Element => {
  return (
    <div className="flex flex-col items-center justify-end flex-1 space-x-4 space-y-2 md:flex-row md:space-y-0">
      <form onSubmit={onPortfolioCreate}>
        <input readOnly={true} hidden={true} value={symbol} />
        <button
          type="submit"
          className="p-2 px-8 text-white bg-darkBlue rounded-lg hover:opacity-70 focus:outline-none"
        >
          Add
        </button>
      </form>
    </div>
  )
}
{% endhighlight %}

### Navbar

* Navbar.tsx
{% highlight tsx %}
...
const Navbar: React.FC<Props> = (props: Props): JSX.Element => {
  return (
    <nav className="relative container mx-auto p-6">
      <div className="flex items-center justify-between">
        <div className="flex items-center space-x-20">
          <img src={logo} alt="" />
          <div className="hidden font-bold lg:flex">
            <a href="" className="text-black hover:text-darkBlue">
              Dashboard
            </a>
          </div>
        </div>
        <div className="hidden lg:flex items-center space-x-6 text-back">
          <div className="hover:text-darkBlue">Login</div>
          <a
            href=""
            className="px-8 py-3 font-bold rounded text-white bg-lightGreen hover:opacity-70"
          >
            Signup
          </a>
        </div>
      </div>
    </nav>
  )
}
{% endhighlight %}

### Hero
- create `Hero` folder in components folder
- create `Hero.tsx` and `Hero.css` in Hero folder

* Hero.tsx
{% highlight tsx %}
import React from 'react'
import hero from '../../assets/images/hero.png'

interface Props {}

const Hero: React.FC<Props> = (props: Props): JSX.Element => {
  return (
    <section id="hero">
    <div className="container flex flex-col-reverse mx-auto p-8 lg:flex-row">
      <div className="flex flex-col space-y-10 mb-44 m-10 lg:m-10 xl:m-20 lg:mt:16 lg:w-1/2 xl:mb-52">
        <h1 className="text-5xl font-bold text-center lg:text-6xl lg:max-w-md lg:text-left">
          Financial data with no news.
        </h1>
        <p className="text-2xl text-center text-gray-400 lg:max-w-md lg:text-left">
          Search relevant financial documents without fear mongering and fake
          news.
        </p>
        <div className="mx-auto lg:mx-0">
          <a
            href=""
            className="py-5 px-10 text-2xl font-bold text-white bg-lightGreen rounded lg:py-4 hover:opacity-70"
          >
            Get Started
          </a>
        </div>
      </div>
      <div className="mb-24 mx-auto md:w-180 md:px-10 lg:mb-0 lg:w-1/2">
        <img src={hero} alt="" />
      </div>
    </div>
  </section>
  )
}

export default Hero
{% endhighlight %}

### CardList

* CardList.tsx
{% highlight tsx %}
...
const CardList: React.FC<Props> = ( { searchResults, onPortfolioCreate }: Props): JSX.Element => {
  return (
    <>
      {searchResults.length > 0 ? (
        searchResults.map((result) => {
          return <Card id={result.symbol} key={uuidv4()} searchResult={result} onPortfolioCreate={onPortfolioCreate}/>
        })
      ) : (
        <p className="mb-3 mt-3 text-xl font-semibold text-center md:text-xl">
          No results!
        </p>)}
    </>
  )
}
{% endhighlight %}

### Card

* Card.tsx
{% highlight tsx %}
...
const Card: React.FC<Props> = ({ id, searchResult, onPortfolioCreate }: Props): JSX.Element => {
  return (
    <div
      className="flex flex-col items-center justify-between w-full p-6 bg-slate-100 rounded-lg md:flex-row"
      key={id}
      id={id}
    >
      <h2 className="font-bold text-center text-veryDarkViolet md:text-left">
        {searchResult.name} ({searchResult.symbol})
      </h2>
      <p className="text-veryDarkBlue">{searchResult.currency}</p>
      <p className="font-bold text-veryDarkBlue">
        {searchResult.exchangeShortName} - {searchResult.stockExchange}
      </p>
      <AddPortfolio onPortfolioCreate={onPortfolioCreate} symbol={searchResult.symbol}/>
    </div>
  )
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/29.jpg"><img src="/assets/img/posts/react_finshark/29.jpg"></a>
	<figcaption>Tailwind</figcaption>
</figure>

# React Router
- type `npm install -save react-router` in terminal
- type `npm install -save react-router-dom` in terminal
- type `npm install -save @types/react-router` in terminal
- type `npm install -save @types/react-router-dom` in terminal

## Outlet
- is a component in React Router used to render child within a parent route.
- acts as a placeholder whrer the content of nested routes will be displayed.

### Router
- create `routes` folder in src folder
- create `Routes.tsx`

* Routes.tsx
{% highlight tsx %}
import { createBrowserRouter } from "react-router-dom";
import App from "../App";
import HomePage from "../pages/HomePage/HomePage";
import SearchPage from "../pages/SearchPage/SearchPage";
import CompanyPage from "../pages/CompanyPage/CompanyPage";

export const router = createBrowserRouter([
  {
    path: "/",
    element: <App/>,
    children: [
      { path: "", element: <HomePage/> },
      { path: "search", element: <SearchPage/> }
    ]
  }
])
{% endhighlight %}

* index.tsx
{% highlight tsx %}
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import reportWebVitals from './reportWebVitals';
import { RouterProvider } from 'react-router-dom';
import { router } from './routes/Routes';

const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);

root.render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);

reportWebVitals();
{% endhighlight %}

### HomePage
- create `pages` folder in src folder
- create `HomePage` folder in pages folder
- create `HomePage.tsx` and `HomePage.css` in HomePage folder

* HomePage.tsx
{% highlight tsx %}
import React from 'react'
import Hero from '../../components/Hero/Hero'

type Props = {}

const HomePage: React.FC<Props> = (props: Props): JSX.Element => {
  return (
    <div>
      <Hero/>
    </div>
  )
}

export default HomePage
{% endhighlight %}

### SearchPage
- create `SearchPage` folder in pages folder
- create `SearchPage.tsx` and `SearchPage.css` in SearchPage folder

* SearchPage.tsx
{% highlight tsx %}
import React, { ChangeEvent, SyntheticEvent, useState } from 'react'
import Navbar from '../../components/Navbar/Navbar'
import Search from '../../components/Search/Search'
import ListPortfolio from '../../components/Portfolio/ListPortfolio/ListPortfolio'
import CardList from '../../components/CardList/CardList'
import { CompanySearch } from '../../company'
import { searchCompanies } from '../../api'

type Props = {}

const SearchPage = (props: Props) => {
  const [search, setSearch] = useState<string>("");
  const [portfolioValues, setPortfolioValues] = useState<string[]>([]);
  const [searchResult, setSearchResult] = useState<CompanySearch[]>([]);
  const [serverError, setServerError] = useState<string>("");

  const handleSearchChange = (e: ChangeEvent<HTMLInputElement>) => {
    setSearch(e.target.value);
    console.log(e);
  };
  const onSearchSubmit = async (e: SyntheticEvent) => {
    e.preventDefault();

    const result = await searchCompanies(search);
    if (typeof result === "string") {
      setServerError(result);
    } else if (Array.isArray(result.data)){
      setSearchResult(result.data);
    }
    console.log(searchResult);
  };
  const onPortfolioCreate = (e: any) => {
    e.preventDefault();
    
    const exists = portfolioValues.find((value) => value === e.target[0].value);
    if (exists) return;
    
    const updatedPortfolio = [...portfolioValues, e.target[0].value]
    setPortfolioValues(updatedPortfolio);
  }
  const onPortfolioDelete = (e: any) => {
    e.preventDefault();
    
    const removed = portfolioValues.filter((value) => {
      return value !== e.target[0].value;
    });
    setPortfolioValues(removed);
  }

  return (
    <div>
      <Search
        onSearchSubmit={onSearchSubmit}
        search={search}
        handleSearchChange={handleSearchChange}
      />
      <ListPortfolio
        portfolioValues={portfolioValues}
        onPortfolioDelete={onPortfolioDelete}
      />
      <CardList
        searchResults={searchResult}
        onPortfolioCreate={onPortfolioCreate}
      />

      {serverError && <div>Unable to connect to API</div>}
    </div>
  )
}

export default SearchPage
{% endhighlight %}

### Hero
- create `Hero` folder in components folder
- create `Hero.tsx` and `Hero.css` in Hero folder

* Hero.tsx
{% highlight tsx %}
import React from 'react'
import hero from '../../assets/images/hero.png'
import { Link } from 'react-router-dom'

interface Props {}

const Hero: React.FC<Props> = (props: Props): JSX.Element => {
  return (
    <section id="hero">
    <div className="container flex flex-col-reverse mx-auto p-8 lg:flex-row">
      <div className="flex flex-col space-y-10 mb-44 m-10 lg:m-10 xl:m-20 lg:mt:16 lg:w-1/2 xl:mb-52">
        <h1 className="text-5xl font-bold text-center lg:text-6xl lg:max-w-md lg:text-left">
          Financial data with no news.
        </h1>
        <p className="text-2xl text-center text-gray-400 lg:max-w-md lg:text-left">
          Search relevant financial documents without fear mongering and fake
          news.
        </p>
        <div className="mx-auto lg:mx-0">
          <Link
            to="/search"
            className="py-5 px-10 text-2xl font-bold text-white bg-lightGreen rounded lg:py-4 hover:opacity-70"
          >
            Get Started
          </Link>
        </div>
      </div>
      <div className="mb-24 mx-auto md:w-180 md:px-10 lg:mb-0 lg:w-1/2">
        <img src={hero} alt="" />
      </div>
    </div>
  </section>
  )
}

export default Hero
{% endhighlight %}

### Navbar

* Navbar.tsx
{% highlight tsx %}
...
const Navbar: React.FC<Props> = (props: Props): JSX.Element => {
  return (
    <nav className="relative container mx-auto p-6">
      <div className="flex items-center justify-between">
        <div className="flex items-center space-x-20">
          <Link to="/">
            <img src={logo} alt="" />
          </Link>
          <div className="hidden font-bold lg:flex">
            <Link to="/search" className="text-black hover:text-darkBlue">
              Search
            </Link>
          </div>
        </div>
        <div className="hidden lg:flex items-center space-x-6 text-back">
          <div className="hover:text-darkBlue">Login</div>
          <a
            href=""
            className="px-8 py-3 font-bold rounded text-white bg-lightGreen hover:opacity-70"
          >
            Signup
          </a>
        </div>
      </div>
    </nav>
  )
}
{% endhighlight %}

### CardPortfolio

* CardPortfolio.tsx
{% highlight tsx %}
...
const CardPortfolio: React.FC<Props> = ({ portfolioValue, onPortfolioDelete }: Props): JSX.Element => {
  return (
    <div className="flex flex-col w-full p-8 space-y-4 text-center rounded-lg shadow-lg md:w-1/3">
      <Link to={`/company/${portfolioValue}`} className="pt-6 text-xl font-bold">
        {portfolioValue}
      </Link>
      <DeletePortfolio
        portfolioValue={portfolioValue}
        onPortfolioDelete={onPortfolioDelete}
      />
    </div>
  )
}
{% endhighlight %}

### CardList

* CardList.tsx
{% highlight tsx %}
...
const CardList: React.FC<Props> = ( { searchResults, onPortfolioCreate }: Props): JSX.Element => {
  return (
    <>
      {searchResults.length > 0 ? (
        searchResults.map((result) => {
          return <Card id={result.symbol} key={uuidv4()} searchResult={result} onPortfolioCreate={onPortfolioCreate}/>
        })
      ) : (
        <p className="mb-3 mt-3 text-xl font-semibold text-center md:text-xl">
          No results!
        </p>)}
    </>
  )
}
{% endhighlight %}

### Card

* Card.tsx
{% highlight tsx %}
...
const Card: React.FC<Props> = ({ id, searchResult, onPortfolioCreate }: Props): JSX.Element => {
  return (
    <div
      className="flex flex-col items-center justify-between w-full p-6 bg-slate-100 rounded-lg md:flex-row"
      key={id}
      id={id}
    >
      <p className="text-veryDarkBlue">{searchResult.currency}</p>
      <p className="font-bold text-veryDarkBlue">
        {searchResult.exchangeShortName} - {searchResult.stockExchange}
      </p>
      <AddPortfolio onPortfolioCreate={onPortfolioCreate} symbol={searchResult.symbol}/>
    </div>
  )
}
{% endhighlight %}

### App

* App.tsx
{% highlight tsx %}
import { Outlet } from 'react-router';
import './App.css';
import Navbar from './components/Navbar/Navbar';

function App() {
  

  return (
    <>
      <Navbar/>
      <Outlet/>
    </>
  );
}

export default App;
{% endhighlight %}

# useEffect
- is a React hook used for side effects.
- runs after the first render by default and can re-run based on changes to dependencies.

## useParams
- is a React Router hook used to access the dynamic parameters in the current URL.
- returns an object with key-value pairs corresponding to the named parameters in the route path.
- works with nested routes or multiple parameters and makes it easy to build dynamic, URL-driven applications.

### Api

* api.tsx

{% highlight tsx %}
...
export const getCompanyProfile = async (query: string) => {
  try {
    const data = await axios.get<CompanyProfile[]>(
      `https://financialmodelingprep.com/api/v3/profile/${query}?apikey=${process.env.REACT_APP_API_KEY}`
    );
    return data;
  } catch (error: any) {
    console.log("error message from API: ", error.message);

  }
}
{% endhighlight %}

### Card

* Card.tsx

{% highlight tsx %}
...
const Card: React.FC<Props> = ({ id, searchResult, onPortfolioCreate }: Props): JSX.Element => {
  return (
    <div
      className="flex flex-col items-center justify-between w-full p-6 bg-slate-100 rounded-lg md:flex-row"
      key={id}
      id={id}
    >
      <Link to={`/company/${searchResult.symbol}`} className="font-bold text-center text-veryDarkViolet md:text-left">
        {searchResult.name} ({searchResult.symbol})
      </Link>
      <p className="text-veryDarkBlue">{searchResult.currency}</p>
      <p className="font-bold text-veryDarkBlue">
        {searchResult.exchangeShortName} - {searchResult.stockExchange}
      </p>
      <AddPortfolio onPortfolioCreate={onPortfolioCreate} symbol={searchResult.symbol}/>
    </div>
  )
}
{% endhighlight %}

### CompanyPage
- create `CompanyPage` folder in page folder
- create `CompanyPage.tsx` and `CompanyPage.css` in CompanyPage folder

* CompanyPage.tsx
{% highlight tsx %}
import React, { useEffect, useState } from 'react'
import { useParams } from 'react-router'
import { CompanyProfile } from '../../company';
import { getCompanyProfile } from '../../api';

type Props = {}

const CompanyPage: React.FC<Props> = (props: Props): JSX.Element => {
  let { ticker } = useParams();
  const [company, setCompany] = useState<CompanyProfile>();

  useEffect(() => {
    const getCompanyInit = async () => {
      const result = await getCompanyProfile(ticker!);
      setCompany(result?.data[0]);
    }
    getCompanyInit();
  }, []);

  return (
    <>
      {company ? (
        <div>{company.companyName}</div>
      ) : (
        <p>Company not found!</p>
      )}
    </>
  )
}

export default CompanyPage
{% endhighlight %}

### Router

* Routes.tsx
{% highlight tsx %}
import { createBrowserRouter } from "react-router-dom";
import App from "../App";
import HomePage from "../pages/HomePage/HomePage";
import SearchPage from "../pages/SearchPage/SearchPage";
import CompanyPage from "../pages/CompanyPage/CompanyPage";

export const router = createBrowserRouter([
  {
    path: "/",
    element: <App/>,
    children: [
      { path: "", element: <HomePage/> },
      { path: "search", element: <SearchPage/> },
      { path: "company/:ticker", element: <CompanyPage/> }
    ]
  }
])
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/30.jpg"><img src="/assets/img/posts/react_finshark/30.jpg"></a>
	<figcaption>useEffect</figcaption>
</figure>

# Dashboard

## useParams

## react-icons
- type `npm install react-icons --save` in terminal
- check icons in <a href="https://react-icons.github.io/react-icons/">react-icons</a>

### CompanyProfile
- create `CompanyProfile` folder in components folder
- create `CompanyProfile.tsx` and `CompanyProfile.css` in CompanyProfile folder

* CompanyProfile.tsx

{% highlight tsx %}
import React from 'react'

type Props = {}

const CompanyProfile = (props: Props) => {
  return (
    <div>CompanyProfile</div>
  )
}

export default CompanyProfile
{% endhighlight %}

### Router

* Routes.tsx

{% highlight tsx %}
...
export const router = createBrowserRouter([
  {
    path: "/",
    element: <App/>,
    children: [
      { path: "", element: <HomePage/> },
      { path: "search", element: <SearchPage/> },
      { path: "company/:ticker", element: <CompanyPage/>,
        children: [
          { path: "company-profile", element: <CompanyProfile/> }
        ]
      }
    ]
  }
])
{% endhighlight %}

### CompanyDashboard
- create `CompanyDashboard` folder in components folder
- create `CompanyDashboard.tsx` and `CompanyDashboard.css` in CompanyDashboard folder

* CompanyDashboard.tsx
{% highlight tsx %}
import React from 'react'
import { Outlet } from 'react-router'

interface Props {
  children: React.ReactNode;
}

const CompanyDashboard = ({ children }: Props) => {
  return (
    <div className="relative md:ml-64 bg-blueGray-100 w-full">
      <div className="relative pt-20 pb-32 bg-lightBlue-500">
        <div className="px-4 md:px-6 mx-auto w-full">
          <div>
            <div className="flex flex-wrap">
              {children}
            </div>
            <div className="flex flex-wrap">
              {<Outlet />}
            </div>
          </div>
        </div>
      </div>
    </div>
  )
}

export default CompanyDashboard
{% endhighlight %}

### CompanyPage

* CompanyPage.tsx

{% highlight tsx %}
import React, { useEffect, useState } from 'react'
import { useParams } from 'react-router'
import { CompanyProfile } from '../../company';
import { getCompanyProfile } from '../../api';
import Sidebar from '../../components/Sidebar/Sidebar';
import CompanyDashboard from '../../components/CompanyDashboard/CompanyDashboard';
import Tile from '../../components/Tile/Tile';

type Props = {}

const CompanyPage: React.FC<Props> = (props: Props): JSX.Element => {
  let { ticker } = useParams();
  const [company, setCompany] = useState<CompanyProfile>();

  useEffect(() => {
    const getCompanyInit = async () => {
      const result = await getCompanyProfile(ticker!);
      setCompany(result?.data[0]);
    }
    getCompanyInit();
  }, []);

  return (
    <>
      {company ? (
        <div className="w-full relative flex ct-docs-disable-sidebar-content overflow-x-hidden">
          <Sidebar />
          <CompanyDashboard>
            <Tile title="Company Name" subTitle={company.companyName}/>
          </CompanyDashboard>
        </div>
      ) : (
        <p>Company not found!</p>
      )}
    </>
  )
}

export default CompanyPage
{% endhighlight %}

### Sidebar

* Sidebar.tsx

{% highlight tsx %}
import React from 'react';
import { Link } from 'react-router-dom';
import { FaHome } from 'react-icons/fa';

interface Props {}

const Sidebar: React.FC<Props> = (props: Props): JSX.Element => (
  <nav className="block py-4 px-6 top-0 bottom-0 w-64 bg-white shadow-xl left-0 absolute flex-row flex-nowrap md:z-10 z-9999 transition-all duration-300 ease-in-out transform md:translate-x-0 -translate-x-full">
    <button className="md:hidden flex items-center justify-center cursor-pointer text-blueGray-700 w-6 h-10 border-l-0 border-r border-t border-b border-solid border-blueGray-100 text-xl leading-none bg-white rounded-r border border-solid border-transparent absolute top-1/2 -right-24-px focus:outline-none z-9998">
      <i className="fas fa-ellipsis-v"></i>
    </button>
    <div className="flex-col min-h-full px-0 flex flex-wrap items-center justify-between w-full mx-auto overflow-y-auto overflow-x-hidden">
      <div className="flex bg-white flex-col items-stretch opacity-100 relative mt-4 overflow-y-auto overflow-x-hidden h-auto z-40 items-center flex-1 rounded w-full">
        <div className="md:flex-col md:min-w-full flex flex-col list-none">
          <Link to="company-profile" className="flex md:min-w-full text-blueGray-500 text-xs uppercase font-bold block pt-1 pb-4 no-underline">
            <FaHome/>
            <h6 className="ml-3">Company Profile</h6>
          </Link>
        </div>
      </div>
    </div>
  </nav>
)

export default Sidebar
{% endhighlight %}

### Tile

* Tile.tsx

{% highlight tsx %}
import React from 'react'

type Props = {
  title: string;
  subTitle: string;
}

const Tile = ({ title, subTitle }: Props) => {
  return (
    <div className="w-full lg:w-6/12 xl:w-3/12 px-4">
      <div className="relative flex flex-col min-w-0 break-words bg-white rounded-lg mb-6 xl:mb-0 shadow-lg">
        <div className="flex-auto p-4">
          <div className="flex flex-wrap">
            <div className="relative w-full pr-4 max-w-full flex-grow flex-1">
              <h5 className="text-blueGray-400 uppercase font-bold text-xs">
                {title}
              </h5>
              <span className="font-bold text-xl">{subTitle}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  )
}

export default Tile
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/31.jpg"><img src="/assets/img/posts/react_finshark/31.jpg"></a>
	<figcaption>Dashboard</figcaption>
</figure>

# Table
- create `Table` folder in components folder
- create `testData.tsx`, `Table.tsx` and `Table.css` in Table folder

* testData.tsx
{% highlight tsx %}
export const TestDataCompany = [
  {
    symbol: "AAPL",
    price: 145.85,
    beta: 1.201965,
    volAvg: 79766736,
    mktCap: 2410929717248,
    lastDiv: 0.85,
    range: "105.0-157.26",
    changes: 2.4200134,
    companyName: "Apple Inc.",
    currency: "USD",
    cik: "0000320193",
    isin: "US0378331005",
    cusip: "037833100",
    exchange: "Nasdaq Global Select",
    exchangeShortName: "NASDAQ",
    industry: "Consumer Electronics",
    website: "http://www.apple.com",
    description:
      "Apple Inc. designs, manufactures, and markets smartphones, personal computers, tablets, wearables, and accessories worldwide. It also sells various related services. The company offers iPhone, a line of smartphones; Mac, a line of personal computers; iPad, a line of multi-purpose tablets; and wearables, home, and accessories comprising AirPods, Apple TV, Apple Watch, Beats products, HomePod, iPod touch, and other Apple-branded and third-party accessories. It also provides AppleCare support services; cloud services store services; and operates various platforms, including the App Store, that allow customers to discover and download applications and digital content, such as books, music, video, games, and podcasts. In addition, the company offers various services, such as Apple Arcade, a game subscription service; Apple Music, which offers users a curated listening experience with on-demand radio stations; Apple News+, a subscription news and magazine service; Apple TV+, which offers exclusive original content; Apple Card, a co-branded credit card; and Apple Pay, a cashless payment service, as well as licenses its intellectual property. The company serves consumers, and small and mid-sized businesses; and the education, enterprise, and government markets. It sells and delivers third-party applications for its products through the App Store. The company also sells its products through its retail and online stores, and direct sales force; and third-party cellular network carriers, wholesalers, retailers, and resellers. Apple Inc. was founded in 1977 and is headquartered in Cupertino, California.",
    ceo: "Mr. Timothy Cook",
    sector: "Technology",
    country: "US",
    fullTimeEmployees: "147000",
    phone: "14089961010",
    address: "1 Apple Park Way",
    city: "Cupertino",
    state: "CALIFORNIA",
    zip: "95014",
    dcfDiff: 89.92,
    dcf: 148.019,
    image: "https://financialmodelingprep.com/image-stock/AAPL.png",
    ipoDate: "1980-12-12",
    defaultImage: false,
    isEtf: false,
    isActivelyTrading: true,
    isAdr: false,
    isFund: false,
  },
];

export const testIncomeStatementData = [
  {
    date: "2022-09-24",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2022-10-28",
    acceptedDate: "2022-10-27 18:01:14",
    calendarYear: "2022",
    period: "FY",
    revenue: 394328000000,
    costOfRevenue: 223546000000,
    grossProfit: 170782000000,
    grossProfitRatio: 0.4330963056,
    researchAndDevelopmentExpenses: 26251000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 25094000000,
    otherExpenses: -334000000,
    operatingExpenses: 51345000000,
    costAndExpenses: 274891000000,
    interestIncome: 2825000000,
    interestExpense: 2931000000,
    depreciationAndAmortization: 11104000000,
    ebitda: 130541000000,
    ebitdaratio: 0.3310467428,
    operatingIncome: 119437000000,
    operatingIncomeRatio: 0.302887444,
    totalOtherIncomeExpensesNet: -334000000,
    incomeBeforeTax: 119103000000,
    incomeBeforeTaxRatio: 0.3020404333,
    incomeTaxExpense: 19300000000,
    netIncome: 99803000000,
    netIncomeRatio: 0.2530964071,
    eps: 6.15,
    epsdiluted: 6.11,
    weightedAverageShsOut: 16215963000,
    weightedAverageShsOutDil: 16325819000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000032019322000108/0000320193-22-000108-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000032019322000108/aapl-20220924.htm",
  },
  {
    date: "2021-09-25",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2021-10-29",
    acceptedDate: "2021-10-28 18:04:28",
    calendarYear: "2021",
    period: "FY",
    revenue: 365817000000,
    costOfRevenue: 212981000000,
    grossProfit: 152836000000,
    grossProfitRatio: 0.4177935963,
    researchAndDevelopmentExpenses: 21914000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 21973000000,
    otherExpenses: 258000000,
    operatingExpenses: 43887000000,
    costAndExpenses: 256868000000,
    interestIncome: 2843000000,
    interestExpense: 2645000000,
    depreciationAndAmortization: 11284000000,
    ebitda: 120233000000,
    ebitdaratio: 0.3286697994,
    operatingIncome: 108949000000,
    operatingIncomeRatio: 0.2978237753,
    totalOtherIncomeExpensesNet: 258000000,
    incomeBeforeTax: 109207000000,
    incomeBeforeTaxRatio: 0.2985290459,
    incomeTaxExpense: 14527000000,
    netIncome: 94680000000,
    netIncomeRatio: 0.2588179336,
    eps: 5.67,
    epsdiluted: 5.61,
    weightedAverageShsOut: 16701272000,
    weightedAverageShsOutDil: 16864919000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000032019321000105/0000320193-21-000105-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000032019321000105/aapl-20210925.htm",
  },
  {
    date: "2020-09-26",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2020-10-30",
    acceptedDate: "2020-10-29 18:06:25",
    calendarYear: "2020",
    period: "FY",
    revenue: 274515000000,
    costOfRevenue: 169559000000,
    grossProfit: 104956000000,
    grossProfitRatio: 0.3823324773,
    researchAndDevelopmentExpenses: 18752000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 19916000000,
    otherExpenses: 803000000,
    operatingExpenses: 38668000000,
    costAndExpenses: 208227000000,
    interestIncome: 3763000000,
    interestExpense: 2873000000,
    depreciationAndAmortization: 11056000000,
    ebitda: 77344000000,
    ebitdaratio: 0.2817478098,
    operatingIncome: 66288000000,
    operatingIncomeRatio: 0.2414731435,
    totalOtherIncomeExpensesNet: 803000000,
    incomeBeforeTax: 67091000000,
    incomeBeforeTaxRatio: 0.2443983025,
    incomeTaxExpense: 9680000000,
    netIncome: 57411000000,
    netIncomeRatio: 0.2091361128,
    eps: 3.31,
    epsdiluted: 3.28,
    weightedAverageShsOut: 17352119000,
    weightedAverageShsOutDil: 17528214000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000032019320000096/0000320193-20-000096-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000032019320000096/aapl-20200926.htm",
  },
  {
    date: "2019-09-28",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2019-10-31",
    acceptedDate: "2019-10-30 18:12:36",
    calendarYear: "2019",
    period: "FY",
    revenue: 260174000000,
    costOfRevenue: 161782000000,
    grossProfit: 98392000000,
    grossProfitRatio: 0.3781776811,
    researchAndDevelopmentExpenses: 16217000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 18245000000,
    otherExpenses: 1807000000,
    operatingExpenses: 34462000000,
    costAndExpenses: 196244000000,
    interestIncome: 4961000000,
    interestExpense: 3576000000,
    depreciationAndAmortization: 12547000000,
    ebitda: 76477000000,
    ebitdaratio: 0.2939455903,
    operatingIncome: 63930000000,
    operatingIncomeRatio: 0.2457201719,
    totalOtherIncomeExpensesNet: 1807000000,
    incomeBeforeTax: 65737000000,
    incomeBeforeTaxRatio: 0.2526655238,
    incomeTaxExpense: 10481000000,
    netIncome: 55256000000,
    netIncomeRatio: 0.2123809451,
    eps: 2.99,
    epsdiluted: 2.97,
    weightedAverageShsOut: 18471336000,
    weightedAverageShsOutDil: 18595652000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000032019319000119/0000320193-19-000119-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000032019319000119/a10-k20199282019.htm",
  },
  {
    date: "2018-09-29",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2018-11-05",
    acceptedDate: "2018-11-05 08:01:40",
    calendarYear: "2018",
    period: "FY",
    revenue: 265595000000,
    costOfRevenue: 163756000000,
    grossProfit: 101839000000,
    grossProfitRatio: 0.3834371882,
    researchAndDevelopmentExpenses: 14236000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 16705000000,
    otherExpenses: 2005000000,
    operatingExpenses: 30941000000,
    costAndExpenses: 194697000000,
    interestIncome: 5686000000,
    interestExpense: 3240000000,
    depreciationAndAmortization: 10903000000,
    ebitda: 81801000000,
    ebitdaratio: 0.3079914908,
    operatingIncome: 70898000000,
    operatingIncomeRatio: 0.2669402662,
    totalOtherIncomeExpensesNet: 2005000000,
    incomeBeforeTax: 72903000000,
    incomeBeforeTaxRatio: 0.2744893541,
    incomeTaxExpense: 13372000000,
    netIncome: 59531000000,
    netIncomeRatio: 0.2241420207,
    eps: 3,
    epsdiluted: 2.98,
    weightedAverageShsOut: 19821508000,
    weightedAverageShsOutDil: 20000436000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000032019318000145/0000320193-18-000145-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000032019318000145/a10-k20189292018.htm",
  },
  {
    date: "2017-09-30",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2017-11-03",
    acceptedDate: "2017-11-03 08:01:37",
    calendarYear: "2017",
    period: "FY",
    revenue: 229234000000,
    costOfRevenue: 141048000000,
    grossProfit: 88186000000,
    grossProfitRatio: 0.3846986049,
    researchAndDevelopmentExpenses: 11581000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 15261000000,
    otherExpenses: 2745000000,
    operatingExpenses: 26842000000,
    costAndExpenses: 167890000000,
    interestIncome: 5201000000,
    interestExpense: 2323000000,
    depreciationAndAmortization: 10157000000,
    ebitda: 71501000000,
    ebitdaratio: 0.311912718,
    operatingIncome: 61344000000,
    operatingIncomeRatio: 0.2676042821,
    totalOtherIncomeExpensesNet: 2745000000,
    incomeBeforeTax: 64089000000,
    incomeBeforeTaxRatio: 0.2795789455,
    incomeTaxExpense: 15738000000,
    netIncome: 48351000000,
    netIncomeRatio: 0.2109242085,
    eps: 2.32,
    epsdiluted: 2.3,
    weightedAverageShsOut: 20868968000,
    weightedAverageShsOutDil: 21006768000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000032019317000070/0000320193-17-000070-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000032019317000070/a10-k20179302017.htm",
  },
  {
    date: "2016-09-24",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2016-10-26",
    acceptedDate: "2016-10-26 16:42:16",
    calendarYear: "2016",
    period: "FY",
    revenue: 215639000000,
    costOfRevenue: 131376000000,
    grossProfit: 84263000000,
    grossProfitRatio: 0.3907595565,
    researchAndDevelopmentExpenses: 10045000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 14194000000,
    otherExpenses: 1348000000,
    operatingExpenses: 24239000000,
    costAndExpenses: 155615000000,
    interestIncome: 3999000000,
    interestExpense: 1456000000,
    depreciationAndAmortization: 10505000000,
    ebitda: 70529000000,
    ebitdaratio: 0.3270697787,
    operatingIncome: 62567000000,
    operatingIncomeRatio: 0.2901469586,
    totalOtherIncomeExpensesNet: 1348000000,
    incomeBeforeTax: 61372000000,
    incomeBeforeTaxRatio: 0.2846052894,
    incomeTaxExpense: 15685000000,
    netIncome: 45687000000,
    netIncomeRatio: 0.2118679831,
    eps: 2.09,
    epsdiluted: 2.08,
    weightedAverageShsOut: 21883280000,
    weightedAverageShsOutDil: 22001124000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000162828016020309/0001628280-16-020309-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000162828016020309/a201610-k9242016.htm",
  },
  {
    date: "2015-09-26",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2015-10-28",
    acceptedDate: "2015-10-28 16:31:09",
    calendarYear: "2015",
    period: "FY",
    revenue: 233715000000,
    costOfRevenue: 140089000000,
    grossProfit: 93626000000,
    grossProfitRatio: 0.4005990202,
    researchAndDevelopmentExpenses: 8067000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 14329000000,
    otherExpenses: 1285000000,
    operatingExpenses: 22396000000,
    costAndExpenses: 162485000000,
    interestIncome: 2921000000,
    interestExpense: 733000000,
    depreciationAndAmortization: 11257000000,
    ebitda: 82487000000,
    ebitdaratio: 0.3529384079,
    operatingIncome: 71230000000,
    operatingIncomeRatio: 0.3047729072,
    totalOtherIncomeExpensesNet: 1285000000,
    incomeBeforeTax: 72515000000,
    incomeBeforeTaxRatio: 0.3102710566,
    incomeTaxExpense: 19121000000,
    netIncome: 53394000000,
    netIncomeRatio: 0.228457737,
    eps: 2.32,
    epsdiluted: 2.31,
    weightedAverageShsOut: 23013684000,
    weightedAverageShsOutDil: 23172276000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000119312515356351/0001193125-15-356351-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000119312515356351/d17062d10k.htm",
  },
  {
    date: "2014-09-27",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2014-10-27",
    acceptedDate: "2014-10-27 17:11:55",
    calendarYear: "2014",
    period: "FY",
    revenue: 182795000000,
    costOfRevenue: 112258000000,
    grossProfit: 70537000000,
    grossProfitRatio: 0.3858803578,
    researchAndDevelopmentExpenses: 6041000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 11993000000,
    otherExpenses: -431000000,
    operatingExpenses: 18034000000,
    costAndExpenses: 130292000000,
    interestIncome: 1795000000,
    interestExpense: 384000000,
    depreciationAndAmortization: 7946000000,
    ebitda: 61813000000,
    ebitdaratio: 0.3381547635,
    operatingIncome: 52503000000,
    operatingIncomeRatio: 0.2872233923,
    totalOtherIncomeExpensesNet: 980000000,
    incomeBeforeTax: 53483000000,
    incomeBeforeTaxRatio: 0.2925845893,
    incomeTaxExpense: 13973000000,
    netIncome: 39510000000,
    netIncomeRatio: 0.2161437676,
    eps: 1.62,
    epsdiluted: 1.61,
    weightedAverageShsOut: 24342288000,
    weightedAverageShsOutDil: 24490652000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000119312514383437/0001193125-14-383437-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000119312514383437/d783162d10k.htm",
  },
  {
    date: "2013-09-28",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2013-10-30",
    acceptedDate: "2013-10-29 20:38:28",
    calendarYear: "2013",
    period: "FY",
    revenue: 170910000000,
    costOfRevenue: 106606000000,
    grossProfit: 64304000000,
    grossProfitRatio: 0.3762448072,
    researchAndDevelopmentExpenses: 4475000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 10830000000,
    otherExpenses: 1156000000,
    operatingExpenses: 15305000000,
    costAndExpenses: 121911000000,
    interestIncome: 1616000000,
    interestExpense: 136000000,
    depreciationAndAmortization: 6757000000,
    ebitda: 55756000000,
    ebitdaratio: 0.3262301796,
    operatingIncome: 50479000000,
    operatingIncomeRatio: 0.29535428,
    totalOtherIncomeExpensesNet: 1156000000,
    incomeBeforeTax: 50155000000,
    incomeBeforeTaxRatio: 0.2934585454,
    incomeTaxExpense: 13118000000,
    netIncome: 37037000000,
    netIncomeRatio: 0.2167046984,
    eps: 1.43,
    epsdiluted: 1.42,
    weightedAverageShsOut: 25909268000,
    weightedAverageShsOutDil: 26086536000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000119312513416534/0001193125-13-416534-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000119312513416534/d590790d10k.htm",
  },
  {
    date: "2012-09-29",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2012-10-31",
    acceptedDate: "2012-10-31 17:07:19",
    calendarYear: "2012",
    period: "FY",
    revenue: 156508000000,
    costOfRevenue: 87846000000,
    grossProfit: 68662000000,
    grossProfitRatio: 0.4387123981,
    researchAndDevelopmentExpenses: 3381000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 10040000000,
    otherExpenses: 522000000,
    operatingExpenses: 13421000000,
    costAndExpenses: 101267000000,
    interestIncome: 1088000000,
    interestExpense: -522000000,
    depreciationAndAmortization: 3277000000,
    ebitda: 58518000000,
    ebitdaratio: 0.3738978199,
    operatingIncome: 55241000000,
    operatingIncomeRatio: 0.3529595931,
    totalOtherIncomeExpensesNet: 522000000,
    incomeBeforeTax: 55763000000,
    incomeBeforeTaxRatio: 0.3562948859,
    incomeTaxExpense: 14030000000,
    netIncome: 41733000000,
    netIncomeRatio: 0.266650906,
    eps: 1.59,
    epsdiluted: 1.58,
    weightedAverageShsOut: 26174904000,
    weightedAverageShsOutDil: 26469940000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000119312512444068/0001193125-12-444068-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000119312512444068/d411355d10k.htm",
  },
  {
    date: "2011-09-24",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2011-10-26",
    acceptedDate: "2011-10-26 16:35:25",
    calendarYear: "2011",
    period: "FY",
    revenue: 108249000000,
    costOfRevenue: 64431000000,
    grossProfit: 43818000000,
    grossProfitRatio: 0.4047889588,
    researchAndDevelopmentExpenses: 2429000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 7599000000,
    otherExpenses: 415000000,
    operatingExpenses: 10028000000,
    costAndExpenses: 74459000000,
    interestIncome: 519000000,
    interestExpense: -415000000,
    depreciationAndAmortization: 1814000000,
    ebitda: 35604000000,
    ebitdaratio: 0.3289083502,
    operatingIncome: 33790000000,
    operatingIncomeRatio: 0.3121506896,
    totalOtherIncomeExpensesNet: 415000000,
    incomeBeforeTax: 34205000000,
    incomeBeforeTaxRatio: 0.3159844433,
    incomeTaxExpense: 8283000000,
    netIncome: 25922000000,
    netIncomeRatio: 0.2394664154,
    eps: 1,
    epsdiluted: 0.99,
    weightedAverageShsOut: 25879224000,
    weightedAverageShsOutDil: 26226060000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000119312511282113/0001193125-11-282113-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000119312511282113/d220209d10k.htm",
  },
  {
    date: "2010-09-25",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2010-10-27",
    acceptedDate: "2010-10-27 16:36:21",
    calendarYear: "2010",
    period: "FY",
    revenue: 65225000000,
    costOfRevenue: 39541000000,
    grossProfit: 25684000000,
    grossProfitRatio: 0.3937753929,
    researchAndDevelopmentExpenses: 1782000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 5517000000,
    otherExpenses: 155000000,
    operatingExpenses: 7299000000,
    costAndExpenses: 46840000000,
    interestIncome: 311000000,
    interestExpense: -155000000,
    depreciationAndAmortization: 1027000000,
    ebitda: 19412000000,
    ebitdaratio: 0.2976159448,
    operatingIncome: 18385000000,
    operatingIncomeRatio: 0.2818704484,
    totalOtherIncomeExpensesNet: 155000000,
    incomeBeforeTax: 18540000000,
    incomeBeforeTaxRatio: 0.2842468379,
    incomeTaxExpense: 4527000000,
    netIncome: 14013000000,
    netIncomeRatio: 0.2148409352,
    eps: 0.55,
    epsdiluted: 0.54,
    weightedAverageShsOut: 25464908000,
    weightedAverageShsOutDil: 25891936000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000119312510238044/0001193125-10-238044-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000119312510238044/d10k.htm",
  },
  {
    date: "2009-09-26",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2009-10-27",
    acceptedDate: "2009-10-27 16:18:29",
    calendarYear: "2009",
    period: "FY",
    revenue: 36537000000,
    costOfRevenue: 23397000000,
    grossProfit: 13140000000,
    grossProfitRatio: 0.359635438,
    researchAndDevelopmentExpenses: 1333000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 4149000000,
    otherExpenses: 326000000,
    operatingExpenses: 5482000000,
    costAndExpenses: 28879000000,
    interestIncome: 407000000,
    interestExpense: -326000000,
    depreciationAndAmortization: 703000000,
    ebitda: 8361000000,
    ebitdaratio: 0.2288365219,
    operatingIncome: 7658000000,
    operatingIncomeRatio: 0.2095957523,
    totalOtherIncomeExpensesNet: 326000000,
    incomeBeforeTax: 7984000000,
    incomeBeforeTaxRatio: 0.2185182144,
    incomeTaxExpense: 2280000000,
    netIncome: 5704000000,
    netIncomeRatio: 0.1561157183,
    eps: 0.23,
    epsdiluted: 0.22,
    weightedAverageShsOut: 25004448000,
    weightedAverageShsOutDil: 25396140000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000119312509214859/0001193125-09-214859-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000119312509214859/d10k.htm",
  },
  {
    date: "2008-09-27",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2008-11-05",
    acceptedDate: "2008-11-05 06:16:23",
    calendarYear: "2008",
    period: "FY",
    revenue: 32479000000,
    costOfRevenue: 21334000000,
    grossProfit: 11145000000,
    grossProfitRatio: 0.3431448013,
    researchAndDevelopmentExpenses: 1109000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 3761000000,
    otherExpenses: 620000000,
    operatingExpenses: 4870000000,
    costAndExpenses: 26204000000,
    interestIncome: 653000000,
    interestExpense: -620000000,
    depreciationAndAmortization: 473000000,
    ebitda: 6748000000,
    ebitdaratio: 0.2077650174,
    operatingIncome: 6275000000,
    operatingIncomeRatio: 0.1932017611,
    totalOtherIncomeExpensesNet: 620000000,
    incomeBeforeTax: 6895000000,
    incomeBeforeTaxRatio: 0.2122910188,
    incomeTaxExpense: 2061000000,
    netIncome: 4834000000,
    netIncomeRatio: 0.1488346316,
    eps: 0.2,
    epsdiluted: 0.19,
    weightedAverageShsOut: 24684576000,
    weightedAverageShsOutDil: 25259892000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000119312508224958/0001193125-08-224958-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000119312508224958/d10k.htm",
  },
  {
    date: "2007-09-29",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2007-11-15",
    acceptedDate: "2007-11-15 16:49:37",
    calendarYear: "2007",
    period: "FY",
    revenue: 24006000000,
    costOfRevenue: 15852000000,
    grossProfit: 8154000000,
    grossProfitRatio: 0.3396650837,
    researchAndDevelopmentExpenses: 782000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 2963000000,
    otherExpenses: -48000000,
    operatingExpenses: 3745000000,
    costAndExpenses: 19597000000,
    interestIncome: 647000000,
    interestExpense: -599000000,
    depreciationAndAmortization: 317000000,
    ebitda: 4726000000,
    ebitdaratio: 0.1968674498,
    operatingIncome: 4409000000,
    operatingIncomeRatio: 0.1836624177,
    totalOtherIncomeExpensesNet: 599000000,
    incomeBeforeTax: 5008000000,
    incomeBeforeTaxRatio: 0.208614513,
    incomeTaxExpense: 1512000000,
    netIncome: 3496000000,
    netIncomeRatio: 0.1456302591,
    eps: 0.14,
    epsdiluted: 0.14,
    weightedAverageShsOut: 24208660000,
    weightedAverageShsOutDil: 24900176000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000104746907009340/0001047469-07-009340-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000104746907009340/a2181030z10-k.htm",
  },
  {
    date: "2006-09-30",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2006-12-29",
    acceptedDate: "2006-12-29 06:05:58",
    calendarYear: "2006",
    period: "FY",
    revenue: 19315000000,
    costOfRevenue: 13717000000,
    grossProfit: 5598000000,
    grossProfitRatio: 0.2898265597,
    researchAndDevelopmentExpenses: 712000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 2433000000,
    otherExpenses: -29000000,
    operatingExpenses: 3145000000,
    costAndExpenses: 16862000000,
    interestIncome: 394000000,
    interestExpense: -365000000,
    depreciationAndAmortization: 225000000,
    ebitda: 3043000000,
    ebitdaratio: 0.1575459487,
    operatingIncome: 2453000000,
    operatingIncomeRatio: 0.1269997411,
    totalOtherIncomeExpensesNet: 365000000,
    incomeBeforeTax: 2818000000,
    incomeBeforeTaxRatio: 0.1458969713,
    incomeTaxExpense: 829000000,
    netIncome: 1989000000,
    netIncomeRatio: 0.1029769609,
    eps: 0.0843,
    epsdiluted: 0.0811,
    weightedAverageShsOut: 23633624000,
    weightedAverageShsOutDil: 24570728000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000110465906084288/0001104659-06-084288-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000110465906084288/a06-25759_210k.htm",
  },
  {
    date: "2005-09-24",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2005-12-01",
    acceptedDate: "2005-11-30 21:22:48",
    calendarYear: "2005",
    period: "FY",
    revenue: 13931000000,
    costOfRevenue: 9888000000,
    grossProfit: 4043000000,
    grossProfitRatio: 0.2902160649,
    researchAndDevelopmentExpenses: 534000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 1859000000,
    otherExpenses: 0,
    operatingExpenses: 2393000000,
    costAndExpenses: 12281000000,
    interestIncome: 0,
    interestExpense: -165000000,
    depreciationAndAmortization: 179000000,
    ebitda: 1829000000,
    ebitdaratio: 0.1312899289,
    operatingIncome: 1650000000,
    operatingIncomeRatio: 0.1184408872,
    totalOtherIncomeExpensesNet: 165000000,
    incomeBeforeTax: 1815000000,
    incomeBeforeTaxRatio: 0.130284976,
    incomeTaxExpense: 480000000,
    netIncome: 1335000000,
    netIncomeRatio: 0.0958294451,
    eps: 0.0586,
    epsdiluted: 0.0554,
    weightedAverageShsOut: 22636292000,
    weightedAverageShsOutDil: 23992584000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000110465905058421/0001104659-05-058421-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000110465905058421/a05-20674_110k.htm",
  },
  {
    date: "2004-09-25",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2004-12-03",
    acceptedDate: "2004-12-02 18:05:49",
    calendarYear: "2004",
    period: "FY",
    revenue: 8279000000,
    costOfRevenue: 6020000000,
    grossProfit: 2259000000,
    grossProfitRatio: 0.2728590409,
    researchAndDevelopmentExpenses: 489000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 1421000000,
    otherExpenses: -9000000,
    operatingExpenses: 1910000000,
    costAndExpenses: 7930000000,
    interestIncome: 64000000,
    interestExpense: 3000000,
    depreciationAndAmortization: 150000000,
    ebitda: 554000000,
    ebitdaratio: 0.0669162942,
    operatingIncome: 326000000,
    operatingIncomeRatio: 0.0393767363,
    totalOtherIncomeExpensesNet: 57000000,
    incomeBeforeTax: 383000000,
    incomeBeforeTaxRatio: 0.0462616258,
    incomeTaxExpense: 107000000,
    netIncome: 276000000,
    netIncomeRatio: 0.0333373596,
    eps: 0.0129,
    epsdiluted: 0.0121,
    weightedAverageShsOut: 20809040000,
    weightedAverageShsOutDil: 21693728000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000104746904035975/0001047469-04-035975-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000104746904035975/a2147337z10-k.htm",
  },
  {
    date: "2003-09-27",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2003-12-19",
    acceptedDate: "2003-12-19 17:25:45",
    calendarYear: "2003",
    period: "FY",
    revenue: 6207000000,
    costOfRevenue: 4499000000,
    grossProfit: 1708000000,
    grossProfitRatio: 0.2751731916,
    researchAndDevelopmentExpenses: 471000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 1212000000,
    otherExpenses: -5000000,
    operatingExpenses: 1683000000,
    costAndExpenses: 6182000000,
    interestIncome: 69000000,
    interestExpense: 8000000,
    depreciationAndAmortization: 113000000,
    ebitda: 202000000,
    ebitdaratio: 0.032543902,
    operatingIncome: 89000000,
    operatingIncomeRatio: 0.0143386499,
    totalOtherIncomeExpensesNet: 3000000,
    incomeBeforeTax: 92000000,
    incomeBeforeTaxRatio: 0.0148219752,
    incomeTaxExpense: 24000000,
    netIncome: 69000000,
    netIncomeRatio: 0.0111164814,
    eps: 0.0036,
    epsdiluted: 0.0032,
    weightedAverageShsOut: 20195336000,
    weightedAverageShsOutDil: 20354096000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000104746903041604/0001047469-03-041604-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000104746903041604/a2124888z10-k.htm",
  },
  {
    date: "2002-09-28",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2002-12-19",
    acceptedDate: "2002-12-19 17:20:21",
    calendarYear: "2002",
    period: "FY",
    revenue: 5742000000,
    costOfRevenue: 4139000000,
    grossProfit: 1603000000,
    grossProfitRatio: 0.2791710206,
    researchAndDevelopmentExpenses: 447000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 1111000000,
    otherExpenses: 5000000,
    operatingExpenses: 1558000000,
    costAndExpenses: 5697000000,
    interestIncome: 118000000,
    interestExpense: 11000000,
    depreciationAndAmortization: 118000000,
    ebitda: 286000000,
    ebitdaratio: 0.0498084291,
    operatingIncome: 168000000,
    operatingIncomeRatio: 0.0292580982,
    totalOtherIncomeExpensesNet: -81000000,
    incomeBeforeTax: 87000000,
    incomeBeforeTaxRatio: 0.0151515152,
    incomeTaxExpense: 22000000,
    netIncome: 65000000,
    netIncomeRatio: 0.0113200975,
    eps: 0.0032,
    epsdiluted: 0.0032,
    weightedAverageShsOut: 19881232000,
    weightedAverageShsOutDil: 20259960000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000104746902007674/0001047469-02-007674-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000104746902007674/a2096490z10-k.htm",
  },
  {
    date: "2001-09-29",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2001-12-21",
    acceptedDate: "2001-12-21 00:00:00",
    calendarYear: "2001",
    period: "FY",
    revenue: 5363000000,
    costOfRevenue: 4128000000,
    grossProfit: 1235000000,
    grossProfitRatio: 0.2302815588,
    researchAndDevelopmentExpenses: 441000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 1138000000,
    otherExpenses: 8000000,
    operatingExpenses: 1579000000,
    costAndExpenses: 5707000000,
    interestIncome: 218000000,
    interestExpense: 16000000,
    depreciationAndAmortization: 102000000,
    ebitda: -16000000,
    ebitdaratio: -0.0029834048,
    operatingIncome: -344000000,
    operatingIncomeRatio: -0.0641432034,
    totalOtherIncomeExpensesNet: 292000000,
    incomeBeforeTax: -52000000,
    incomeBeforeTaxRatio: -0.0096960656,
    incomeTaxExpense: -15000000,
    netIncome: -37000000,
    netIncomeRatio: -0.0068991236,
    eps: -0.0019,
    epsdiluted: -0.0019,
    weightedAverageShsOut: 19354328000,
    weightedAverageShsOutDil: 19354328000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000091205701544436/0000912057-01-544436-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000091205701544436/a2066171z10-k405.htm",
  },
  {
    date: "2000-09-30",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "2000-12-14",
    acceptedDate: "2000-12-14 00:00:00",
    calendarYear: "2000",
    period: "FY",
    revenue: 7983000000,
    costOfRevenue: 5817000000,
    grossProfit: 2166000000,
    grossProfitRatio: 0.271326569,
    researchAndDevelopmentExpenses: 380000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 1166000000,
    otherExpenses: 6000000,
    operatingExpenses: 1546000000,
    costAndExpenses: 7363000000,
    interestIncome: 210000000,
    interestExpense: 21000000,
    depreciationAndAmortization: 84000000,
    ebitda: 920000000,
    ebitdaratio: 0.1152448954,
    operatingIncome: 836000000,
    operatingIncomeRatio: 0.1047225354,
    totalOtherIncomeExpensesNet: 256000000,
    incomeBeforeTax: 1092000000,
    incomeBeforeTaxRatio: 0.1367906802,
    incomeTaxExpense: 306000000,
    netIncome: 786000000,
    netIncomeRatio: 0.0984592259,
    eps: 0.0432,
    epsdiluted: 0.0389,
    weightedAverageShsOut: 18175808000,
    weightedAverageShsOutDil: 20178144000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000091205700053623/0000912057-00-053623-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000091205700053623/a2032880z10-k.txt",
  },
  {
    date: "1999-09-25",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1999-12-22",
    acceptedDate: "1999-12-22 00:00:00",
    calendarYear: "1999",
    period: "FY",
    revenue: 6134000000,
    costOfRevenue: 4438000000,
    grossProfit: 1696000000,
    grossProfitRatio: 0.2764916857,
    researchAndDevelopmentExpenses: 314000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 996000000,
    otherExpenses: -2000000,
    operatingExpenses: 1310000000,
    costAndExpenses: 5748000000,
    interestIncome: 144000000,
    interestExpense: 47000000,
    depreciationAndAmortization: 85000000,
    ebitda: 613000000,
    ebitdaratio: 0.0999347897,
    operatingIncome: 528000000,
    operatingIncomeRatio: 0.0860776003,
    totalOtherIncomeExpensesNet: 148000000,
    incomeBeforeTax: 676000000,
    incomeBeforeTaxRatio: 0.1102054125,
    incomeTaxExpense: 75000000,
    netIncome: 601000000,
    netIncomeRatio: 0.0979784806,
    eps: 0.0375,
    epsdiluted: 0.0323,
    weightedAverageShsOut: 16033584000,
    weightedAverageShsOutDil: 19506368000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000091205799010244/0000912057-99-010244-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/000091205799010244/0000912057-99-010244.txt",
  },
  {
    date: "1998-09-25",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1998-12-23",
    acceptedDate: "1998-12-23 00:00:00",
    calendarYear: "1998",
    period: "FY",
    revenue: 5941000000,
    costOfRevenue: 4462000000,
    grossProfit: 1479000000,
    grossProfitRatio: 0.2489479886,
    researchAndDevelopmentExpenses: 310000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 908000000,
    otherExpenses: -8000000,
    operatingExpenses: 1218000000,
    costAndExpenses: 5680000000,
    interestIncome: 100000000,
    interestExpense: 62000000,
    depreciationAndAmortization: 111000000,
    ebitda: 504000000,
    ebitdaratio: 0.084834203,
    operatingIncome: 299000000,
    operatingIncomeRatio: 0.0503282276,
    totalOtherIncomeExpensesNet: -64000000,
    incomeBeforeTax: 329000000,
    incomeBeforeTaxRatio: 0.0553778825,
    incomeTaxExpense: 20000000,
    netIncome: 309000000,
    netIncomeRatio: 0.0520114459,
    eps: 0.0209,
    epsdiluted: 0.0188,
    weightedAverageShsOut: 14781088000,
    weightedAverageShsOutDil: 18806704000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000104746998044981/0001047469-98-044981-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/0001047469-98-044981.txt",
  },
  {
    date: "1997-09-26",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1997-12-05",
    acceptedDate: "1997-12-05 00:00:00",
    calendarYear: "1997",
    period: "FY",
    revenue: 7081000000,
    costOfRevenue: 5713000000,
    grossProfit: 1368000000,
    grossProfitRatio: 0.1931930518,
    researchAndDevelopmentExpenses: 860000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 1286000000,
    otherExpenses: 3000000,
    operatingExpenses: 2146000000,
    costAndExpenses: 7859000000,
    interestIncome: 82000000,
    interestExpense: 71000000,
    depreciationAndAmortization: 118000000,
    ebitda: -575000000,
    ebitdaratio: -0.0812032199,
    operatingIncome: -778000000,
    operatingIncomeRatio: -0.1098714871,
    totalOtherIncomeExpensesNet: -267000000,
    incomeBeforeTax: -1045000000,
    incomeBeforeTaxRatio: -0.1475780257,
    incomeTaxExpense: 281000000,
    netIncome: -1326000000,
    netIncomeRatio: -0.1872616862,
    eps: -0.0939,
    epsdiluted: -0.0939,
    weightedAverageShsOut: 14118944000,
    weightedAverageShsOutDil: 14118944000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000104746997006960/0001047469-97-006960-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/0001047469-97-006960.txt",
  },
  {
    date: "1996-09-27",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1996-12-19",
    acceptedDate: "1996-12-19 00:00:00",
    calendarYear: "1996",
    period: "FY",
    revenue: 9833000000,
    costOfRevenue: 8865000000,
    grossProfit: 968000000,
    grossProfitRatio: 0.0984440151,
    researchAndDevelopmentExpenses: 604000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 1568000000,
    otherExpenses: -3000000,
    operatingExpenses: 2172000000,
    costAndExpenses: 11037000000,
    interestIncome: 60000000,
    interestExpense: 60000000,
    depreciationAndAmortization: 156000000,
    ebitda: -991000000,
    ebitdaratio: -0.1007830774,
    operatingIncome: -1204000000,
    operatingIncomeRatio: -0.1224448286,
    totalOtherIncomeExpensesNet: -91000000,
    incomeBeforeTax: -1295000000,
    incomeBeforeTaxRatio: -0.1316993796,
    incomeTaxExpense: -479000000,
    netIncome: -816000000,
    netIncomeRatio: -0.0829858639,
    eps: -0.0589,
    epsdiluted: -0.0589,
    weightedAverageShsOut: 13858208000,
    weightedAverageShsOutDil: 13858208000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000032019396000023/0000320193-96-000023-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/0000320193-96-000023.txt",
  },
  {
    date: "1995-09-29",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1995-12-19",
    acceptedDate: "1995-12-19 00:00:00",
    calendarYear: "1995",
    period: "FY",
    revenue: 11062000000,
    costOfRevenue: 8204000000,
    grossProfit: 2858000000,
    grossProfitRatio: 0.2583619599,
    researchAndDevelopmentExpenses: 614000000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 1583000000,
    otherExpenses: -1000000,
    operatingExpenses: 2197000000,
    costAndExpenses: 10401000000,
    interestIncome: 100000000,
    interestExpense: 48000000,
    depreciationAndAmortization: 127000000,
    ebitda: 887000000,
    ebitdaratio: 0.0801844151,
    operatingIncome: 684000000,
    operatingIncomeRatio: 0.0618333032,
    totalOtherIncomeExpensesNet: -10000000,
    incomeBeforeTax: 674000000,
    incomeBeforeTaxRatio: 0.0609293075,
    incomeTaxExpense: 250000000,
    netIncome: 424000000,
    netIncomeRatio: 0.038329416,
    eps: 0.0308,
    epsdiluted: 0.0308,
    weightedAverageShsOut: 13781264000,
    weightedAverageShsOutDil: 13781264000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000032019395000016/0000320193-95-000016-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/0000320193-95-000016.txt",
  },
  {
    date: "1994-09-30",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1994-12-13",
    acceptedDate: "1994-12-13 00:00:00",
    calendarYear: "1994",
    period: "FY",
    revenue: 9188748000,
    costOfRevenue: 6844915000,
    grossProfit: 2343833000,
    grossProfitRatio: 0.2550764261,
    researchAndDevelopmentExpenses: 564303000,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 1384111000,
    otherExpenses: 8685000,
    operatingExpenses: 1948414000,
    costAndExpenses: 8793329000,
    interestIncome: 43284000,
    interestExpense: 39653000,
    depreciationAndAmortization: 167958000,
    ebitda: 615346000,
    ebitdaratio: 0.0669673387,
    operatingIncome: 522274000,
    operatingIncomeRatio: 0.0568384289,
    totalOtherIncomeExpensesNet: -21988000,
    incomeBeforeTax: 500286000,
    incomeBeforeTaxRatio: 0.0544455023,
    incomeTaxExpense: 190108000,
    netIncome: 310178000,
    netIncomeRatio: 0.0337562854,
    eps: 0.0233,
    epsdiluted: 0.0233,
    weightedAverageShsOut: 13298320000,
    weightedAverageShsOutDil: 13298320000,
    link: "https://www.sec.gov/Archives/edgar/data/320193/000032019394000016/0000320193-94-000016-index.htm",
    finalLink:
      "https://www.sec.gov/Archives/edgar/data/320193/0000320193-94-000016.txt",
  },
  {
    date: "1993-09-30",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1993-09-30",
    acceptedDate: "1993-09-30 00:00:00",
    calendarYear: "1993",
    period: "FY",
    revenue: 7977000000,
    costOfRevenue: 5082700000,
    grossProfit: 2894300000,
    grossProfitRatio: 0.3628306381,
    researchAndDevelopmentExpenses: 0,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 2617800000,
    otherExpenses: 166100000,
    operatingExpenses: 2783900000,
    costAndExpenses: 7866600000,
    interestIncome: 0,
    interestExpense: -58600000,
    depreciationAndAmortization: 166100000,
    ebitda: 247200000,
    ebitdaratio: 0.0309890936,
    operatingIncome: 81100000,
    operatingIncomeRatio: 0.0101667293,
    totalOtherIncomeExpensesNet: 58600000,
    incomeBeforeTax: 139700000,
    incomeBeforeTaxRatio: 0.0175128494,
    incomeTaxExpense: 53100000,
    netIncome: 86600000,
    netIncomeRatio: 0.0108562116,
    eps: 0.0065,
    epsdiluted: 0.0065,
    weightedAverageShsOut: 13342000000,
    weightedAverageShsOutDil: 13424000000,
    link: null,
    finalLink: null,
  },
  {
    date: "1992-09-30",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1992-09-30",
    acceptedDate: "1992-09-30 00:00:00",
    calendarYear: "1992",
    period: "FY",
    revenue: 7086500000,
    costOfRevenue: 3774200000,
    grossProfit: 3312300000,
    grossProfitRatio: 0.4674098638,
    researchAndDevelopmentExpenses: 0,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 2289400000,
    otherExpenses: 217200000,
    operatingExpenses: 2506600000,
    costAndExpenses: 6280800000,
    interestIncome: 0,
    interestExpense: -99400000,
    depreciationAndAmortization: 217200000,
    ebitda: 973300000,
    ebitdaratio: 0.1373456572,
    operatingIncome: 805700000,
    operatingIncomeRatio: 0.113695054,
    totalOtherIncomeExpensesNet: 49800000,
    incomeBeforeTax: 855500000,
    incomeBeforeTaxRatio: 0.1207225005,
    incomeTaxExpense: 325100000,
    netIncome: 530400000,
    netIncomeRatio: 0.0748465392,
    eps: 0.0387,
    epsdiluted: 0.0387,
    weightedAverageShsOut: 13718880000,
    weightedAverageShsOutDil: 13729632000,
    link: null,
    finalLink: null,
  },
  {
    date: "1991-09-30",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1991-09-30",
    acceptedDate: "1991-09-30 00:00:00",
    calendarYear: "1991",
    period: "FY",
    revenue: 6308800000,
    costOfRevenue: 3109700000,
    grossProfit: 3199100000,
    grossProfitRatio: 0.5070853411,
    researchAndDevelopmentExpenses: 0,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 2547400000,
    otherExpenses: 204400000,
    operatingExpenses: 2751800000,
    costAndExpenses: 5861500000,
    interestIncome: 0,
    interestExpense: -104800000,
    depreciationAndAmortization: 204400000,
    ebitda: 599300000,
    ebitdaratio: 0.0949942937,
    operatingIncome: 447300000,
    operatingIncomeRatio: 0.0709009637,
    totalOtherIncomeExpensesNet: 52400000,
    incomeBeforeTax: 499700000,
    incomeBeforeTaxRatio: 0.0792068222,
    incomeTaxExpense: 189900000,
    netIncome: 309800000,
    netIncomeRatio: 0.0491060107,
    eps: 0.023,
    epsdiluted: 0.023,
    weightedAverageShsOut: 13448682171,
    weightedAverageShsOutDil: 13448682171,
    link: null,
    finalLink: null,
  },
  {
    date: "1990-09-30",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1990-09-30",
    acceptedDate: "1990-09-30 00:00:00",
    calendarYear: "1990",
    period: "FY",
    revenue: 5558400000,
    costOfRevenue: 2403500000,
    grossProfit: 3154900000,
    grossProfitRatio: 0.5675913932,
    researchAndDevelopmentExpenses: 0,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 2240200000,
    otherExpenses: 202700000,
    operatingExpenses: 2442900000,
    costAndExpenses: 4846400000,
    interestIncome: 0,
    interestExpense: -133000000,
    depreciationAndAmortization: 202700000,
    ebitda: 848200000,
    ebitdaratio: 0.1525978699,
    operatingIncome: 712000000,
    operatingIncomeRatio: 0.1280944157,
    totalOtherIncomeExpensesNet: 66500000,
    incomeBeforeTax: 778500000,
    incomeBeforeTaxRatio: 0.1400582902,
    incomeTaxExpense: 303600000,
    netIncome: 474900000,
    netIncomeRatio: 0.0854382556,
    eps: 0.0338,
    epsdiluted: 0.0338,
    weightedAverageShsOut: 14071111111,
    weightedAverageShsOutDil: 14071111111,
    link: null,
    finalLink: null,
  },
  {
    date: "1989-09-30",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1989-09-30",
    acceptedDate: "1989-09-30 00:00:00",
    calendarYear: "1989",
    period: "FY",
    revenue: 5284000000,
    costOfRevenue: 2570000000,
    grossProfit: 2714000000,
    grossProfitRatio: 0.5136260409,
    researchAndDevelopmentExpenses: 0,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 1954900000,
    otherExpenses: 124800000,
    operatingExpenses: 2079700000,
    costAndExpenses: 4649700000,
    interestIncome: 0,
    interestExpense: -220000000,
    depreciationAndAmortization: 124800000,
    ebitda: 649100000,
    ebitdaratio: 0.1228425435,
    operatingIncome: 634300000,
    operatingIncomeRatio: 0.1200416351,
    totalOtherIncomeExpensesNet: 110000000,
    incomeBeforeTax: 744300000,
    incomeBeforeTaxRatio: 0.1408591976,
    incomeTaxExpense: 290300000,
    netIncome: 454000000,
    netIncomeRatio: 0.0859197578,
    eps: 0.0316,
    epsdiluted: 0.0316,
    weightedAverageShsOut: 14363841808,
    weightedAverageShsOutDil: 14363841808,
    link: null,
    finalLink: null,
  },
  {
    date: "1988-09-30",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1988-09-30",
    acceptedDate: "1988-09-30 00:00:00",
    calendarYear: "1988",
    period: "FY",
    revenue: 4071400000,
    costOfRevenue: 1913200000,
    grossProfit: 2158200000,
    grossProfitRatio: 0.5300879304,
    researchAndDevelopmentExpenses: 0,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 1460200000,
    otherExpenses: 77700000,
    operatingExpenses: 1537900000,
    costAndExpenses: 3451100000,
    interestIncome: 0,
    interestExpense: -71700000,
    depreciationAndAmortization: 77700000,
    ebitda: 662200000,
    ebitdaratio: 0.1626467554,
    operatingIncome: 620300000,
    operatingIncomeRatio: 0.1523554551,
    totalOtherIncomeExpensesNet: 35900000,
    incomeBeforeTax: 656200000,
    incomeBeforeTaxRatio: 0.1611730609,
    incomeTaxExpense: 255900000,
    netIncome: 400300000,
    netIncomeRatio: 0.0983199882,
    eps: 0.0275,
    epsdiluted: 0.0275,
    weightedAverageShsOut: 14556363636,
    weightedAverageShsOutDil: 14556363636,
    link: null,
    finalLink: null,
  },
  {
    date: "1987-09-30",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1987-09-30",
    acceptedDate: "1987-09-30 00:00:00",
    calendarYear: "1987",
    period: "FY",
    revenue: 2661100000,
    costOfRevenue: 1225700000,
    grossProfit: 1435400000,
    grossProfitRatio: 0.5394009996,
    researchAndDevelopmentExpenses: 0,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 993400000,
    otherExpenses: 70500000,
    operatingExpenses: 1063900000,
    costAndExpenses: 2289600000,
    interestIncome: 0,
    interestExpense: -77800000,
    depreciationAndAmortization: 70500000,
    ebitda: 403100000,
    ebitdaratio: 0.1514787118,
    operatingIncome: 371500000,
    operatingIncomeRatio: 0.1396039232,
    totalOtherIncomeExpensesNet: 38900000,
    incomeBeforeTax: 410400000,
    incomeBeforeTaxRatio: 0.1542219383,
    incomeTaxExpense: 192900000,
    netIncome: 217500000,
    netIncomeRatio: 0.0817331179,
    eps: 0.0148,
    epsdiluted: 0.0148,
    weightedAverageShsOut: 14674698795,
    weightedAverageShsOutDil: 14674698795,
    link: null,
    finalLink: null,
  },
  {
    date: "1986-09-30",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1986-09-30",
    acceptedDate: "1986-09-30 00:00:00",
    calendarYear: "1986",
    period: "FY",
    revenue: 1901900000,
    costOfRevenue: 840000000,
    grossProfit: 1061900000,
    grossProfitRatio: 0.5583364004,
    researchAndDevelopmentExpenses: 0,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 737300000,
    otherExpenses: 51100000,
    operatingExpenses: 788400000,
    costAndExpenses: 1628400000,
    interestIncome: 0,
    interestExpense: -72500000,
    depreciationAndAmortization: 51100000,
    ebitda: 288400000,
    ebitdaratio: 0.1516378358,
    operatingIncome: 273500000,
    operatingIncomeRatio: 0.1438035649,
    totalOtherIncomeExpensesNet: 36300000,
    incomeBeforeTax: 309800000,
    incomeBeforeTaxRatio: 0.1628897418,
    incomeTaxExpense: 155800000,
    netIncome: 154000000,
    netIncomeRatio: 0.0809716599,
    eps: 0.0107,
    epsdiluted: 0.0107,
    weightedAverageShsOut: 14373333333,
    weightedAverageShsOutDil: 14373333333,
    link: null,
    finalLink: null,
  },
  {
    date: "1985-09-30",
    symbol: "AAPL",
    reportedCurrency: "USD",
    cik: "0000320193",
    fillingDate: "1985-09-30",
    acceptedDate: "1985-09-30 00:00:00",
    calendarYear: "1985",
    period: "FY",
    revenue: 1918300000,
    costOfRevenue: 1076000000,
    grossProfit: 842300000,
    grossProfitRatio: 0.4390866913,
    researchAndDevelopmentExpenses: 0,
    generalAndAdministrativeExpenses: 0,
    sellingAndMarketingExpenses: 0,
    sellingGeneralAndAdministrativeExpenses: 653200000,
    otherExpenses: 41800000,
    operatingExpenses: 695000000,
    costAndExpenses: 1771000000,
    interestIncome: 0,
    interestExpense: 54500000,
    depreciationAndAmortization: 41800000,
    ebitda: 216300000,
    ebitdaratio: 0.1127560861,
    operatingIncome: 147300000,
    operatingIncomeRatio: 0.0767867383,
    totalOtherIncomeExpensesNet: -27300000,
    incomeBeforeTax: 120000000,
    incomeBeforeTaxRatio: 0.0625553876,
    incomeTaxExpense: 58800000,
    netIncome: 61200000,
    netIncomeRatio: 0.0319032477,
    eps: 0.0045,
    epsdiluted: 0.0045,
    weightedAverageShsOut: 13708800000,
    weightedAverageShsOutDil: 13708800000,
    link: null,
    finalLink: null,
  },
];
{% endhighlight %}

* Table.tsx
{% highlight tsx %}
import React from 'react'
import { testIncomeStatementData } from './testData'

const data = testIncomeStatementData;

type Props = {}

type Company = (typeof data)[0];

const configs = [
  {
    label: "Year",
    render: (company: Company) => company.acceptedDate
  },
  {
    label: "Cost of Revenue",
    render: (company: Company) => company.costOfRevenue
  }
]

const Table = (props: Props) => {
  const renderedRows = data.map((company) => {
    return (
      <tr key={company.cik}>
        {configs.map((val: any) => {
          return (
            <td className="p-4 whitespace-nowrap text-sm font-normal text-gray-900">
              {val.render(company)}
            </td>
        )})}
      </tr>
    )
  })
  const renderedHeaders = configs.map((config) => {
    return (
      <th className="p-4 text-left text-xs font-medium text-gray-500 uppercase tracking-wider" key={config.label}>
        {config.label}
      </th>
    )
  })

  return (
    <div className="bg-white shadow rounded-lg p-4 sm:p-6 xl:p-8">
      <table>
        <thead className="min-w-full divide-y divide-gray-200 m-5">
          {renderedHeaders}
        </thead>
        <tbody>
          {renderedRows}
        </tbody>
      </table>
    </div>
  )
}

export default Table
{% endhighlight %}

### DesignPage
- create `DesginPage` folder in pages folder
- create `DesignPage.tsx` and `DesignPage.css` in DesignPage folder

* DesignPage.tsx
{% highlight tsx %}
import React from 'react'
import Table from '../../components/Table/Table'

type Props = {}

const DesignPage = (props: Props) => {
  return (
    <>
      <h1>FinShark Design Page</h1>
      <h2>This is Finshark's design page. This is where we well house various design aspects of the app</h2>
      <Table />
    </>
  )
}

export default DesignPage
{% endhighlight %}

### Router

* Routes.tsx
{% highlight tsx %}
import { createBrowserRouter } from "react-router-dom";
import App from "../App";
import HomePage from "../pages/HomePage/HomePage";
import SearchPage from "../pages/SearchPage/SearchPage";
import CompanyPage from "../pages/CompanyPage/CompanyPage";
import CompanyProfile from "../components/CompanyProfile/CompanyProfile";
import DesignPage from "../pages/DesignPage/DesignPage";

export const router = createBrowserRouter([
  {
    path: "/",
    element: <App/>,
    children: [
      { path: "", element: <HomePage/> },
      { path: "search", element: <SearchPage/> },
      { path: "design-guide", element: <DesignPage/> },
      { path: "company/:ticker", element: <CompanyPage/>,
        children: [
          { path: "company-profile", element: <CompanyProfile/> }
        ]
      }
    ]
  }
])
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/32.jpg"><img src="/assets/img/posts/react_finshark/32.jpg"></a>
	<figcaption>Table</figcaption>
</figure>

# RatioList
- create `RatioList` folder in components folder
- create `RatioList.tsx` and `RatioList.css` in RatioList folder

* RatioList.tsx
{% highlight tsx %}
import React from 'react'
import { TestDataCompany } from '../Table/testData'

type Props = {}

const data = TestDataCompany[0];

type Company = typeof data;

const config = [
  {
    label: "Company Name",
    render: (company: Company) => company.companyName,
    subTitle: "This is the company name"
  },
  {
    label: "Company Name",
    render: (company: Company) => company.companyName,
    subTitle: "This is the company name"
  }
]

const RatioList = (props: Props) => {
  const renderedRows = config.map(row => {
    return (
      <li className="py-3 sm:py-4">
        <div className="flex items-center space-x-4">
          <div className="flex-1 min-w-0">
            <p className="text-sm font-medium text-gray-900 truncate">
              {row.label}
            </p>
            <p className="text-sm text-gray-500 truncate">
              {row.subTitle && row.subTitle}
            </p>
          </div>
          <div className="inline-flex items-center text-base font-semibold text-gray-900">
            {row.render(data)}
          </div>
        </div>
      </li>
    )
  })
  return (
    <div className="bg-white shadow rounded-lg mb-4 p-4 sm:p-6 h-full">
      <ul className="divide-y divided-gray-200">
        {renderedRows}
      </ul>
    </div>
  )
}

export default RatioList
{% endhighlight %}

### DesignPage

* DesignPage.tsx
{% highlight tsx %}
import React from 'react'
import Table from '../../components/Table/Table'
import RatioList from '../../components/RatioList/RatioList'

type Props = {}

const DesignPage = (props: Props) => {
  return (
    <>
      <h1>FinShark Design Page</h1>
      <h2>This is Finshark's design page. This is where we well house various design aspects of the app</h2>
      <RatioList />
      <Table />
    </>
  )
}

export default DesignPage
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/33.jpg"><img src="/assets/img/posts/react_finshark/33.jpg"></a>
	<figcaption>RatioList</figcaption>
</figure>

# Company Profile

## useOutletContext
-  is a hook provided by React Router to access context data passed through `Outlet`.

* CompanyProfile.tsx
{% highlight tsx %}
import React, { useEffect, useState } from 'react'
import { CompanyKeyMetrics } from '../../company';
import { useOutletContext } from 'react-router';
import { getKeyMetrics } from '../../api';
import RatioList from '../RatioList/RatioList';

type Props = {}

const tableConfig = [
  {
    label: "Market Cap",
    render: (company: CompanyKeyMetrics) => company.marketCapTTM,
    subTitle: "Total value of all a company's shares of stock",
  },
  {
    label: "Current Ratio",
    render: (company: CompanyKeyMetrics) => company.currentRatioTTM,
    subTitle:
      "Measures the companies ability to pay short term debt obligations",
  },
  {
    label: "Return On Equity",
    render: (company: CompanyKeyMetrics) => company.roeTTM,
    subTitle:
      "Return on equity is the measure of a company's net income divided by its shareholder's equity",
  },
  {
    label: "Return On Assets",
    render: (company: CompanyKeyMetrics) => company.returnOnTangibleAssetsTTM,
    subTitle:
      "Return on assets is the measure of how effective a company is using its assets",
  },
  {
    label: "Free Cashflow Per Share",
    render: (company: CompanyKeyMetrics) => company.freeCashFlowPerShareTTM,
    subTitle:
      "Return on assets is the measure of how effective a company is using its assets",
  },
  {
    label: "Book Value Per Share TTM",
    render: (company: CompanyKeyMetrics) => company.bookValuePerShareTTM,
    subTitle:
      "Book value per share indicates a firm's net asset value (total assets - total liabilities) on per share basis",
  },
  {
    label: "Divdend Yield TTM",
    render: (company: CompanyKeyMetrics) => company.dividendYieldTTM,
    subTitle: "Shows how much a company pays each year relative to stock price",
  },
  {
    label: "Capex Per Share TTM",
    render: (company: CompanyKeyMetrics) => company.capexPerShareTTM,
    subTitle:
      "Capex is used by a company to aquire, upgrade, and maintain physical assets",
  },
  {
    label: "Graham Number",
    render: (company: CompanyKeyMetrics) => company.grahamNumberTTM,
    subTitle:
      "This is the upperbouind of the price range that a defensive investor should pay for a stock",
  },
  {
    label: "PE Ratio",
    render: (company: CompanyKeyMetrics) => company.peRatioTTM,
    subTitle:
      "This is the upperbouind of the price range that a defensive investor should pay for a stock",
  },
];

const CompanyProfile = (props: Props) => {
  const ticker = useOutletContext<string>();
  const[companyData, setCompanyData] = useState<CompanyKeyMetrics>();

  useEffect(() => {
    const getCompanyKeyMetrics = async () => {
      const value = await getKeyMetrics(ticker);
      setCompanyData(value?.data[0]);
    };
    getCompanyKeyMetrics();
  }, []);

  return (
    <>
      { companyData ? (
        <RatioList data={companyData} config={tableConfig}/>
      ):(
        <>Loading...</>
      )}
    </>
  )
}

export default CompanyProfile
{% endhighlight %}

### CompanyDashboard

* CompanyDashboard.tsx
{% highlight tsx %}
import React from 'react'
import { Outlet } from 'react-router'

interface Props {
  children: React.ReactNode;
  ticker: string;
}

const CompanyDashboard = ({ children, ticker }: Props) => {
  return (
    <div className="relative md:ml-64 bg-blueGray-100 w-full">
      <div className="relative pt-20 pb-32 bg-lightBlue-500">
        <div className="px-4 md:px-6 mx-auto w-full">
          <div>
            <div className="flex flex-wrap">
              {children}
            </div>
            <div className="flex flex-wrap">
              {<Outlet context={ticker}/>}
            </div>
          </div>
        </div>
      </div>
    </div>
  )
}

export default CompanyDashboard
{% endhighlight %}

### RatioList

* RatioList.tsx
{% highlight tsx %}
type Props = {
  config: any;
  data: any;
}

const RatioList = ({ config, data }: Props) => {
  const renderedRows = config.map((row: any) => {
    return (
      <li className="py-3 sm:py-4">
        <div className="flex items-center space-x-4">
          <div className="flex-1 min-w-0">
            <p className="text-sm font-medium text-gray-900 truncate">
              {row.label}
            </p>
            <p className="text-sm text-gray-500 truncate">
              {row.subTitle && row.subTitle}
            </p>
          </div>
          <div className="inline-flex items-center text-base font-semibold text-gray-900">
            {row.render(data)}
          </div>
        </div>
      </li>
    )
  })
  return (
    <div className="bg-white shadow rounded-lg ml-4 mt-4 mb-4 p-4 sm:p-6 h-full">
      <ul className="divide-y divided-gray-200">
        {renderedRows}
      </ul>
    </div>
  )
}

export default RatioList
{% endhighlight %}

### Company Model

* company.d.ts
{% highlight ts %}
...
  export interface CompanyKeyMetrics {
    revenuePerShareTTM: number;
    netIncomePerShareTTM: number;
    operatingCashFlowPerShareTTM: number;
    freeCashFlowPerShareTTM: number;
    cashPerShareTTM: number;
    bookValuePerShareTTM: number;
    tangibleBookValuePerShareTTM: number;
    shareholdersEquityPerShareTTM: number;
    interestDebtPerShareTTM: number;
    marketCapTTM: number;
    enterpriseValueTTM: number;
    peRatioTTM: number;
    priceToSalesRatioTTM: number;
    pocfratioTTM: number;
    pfcfRatioTTM: number;
    pbRatioTTM: number;
    ptbRatioTTM: number;
    evToSalesTTM: number;
    enterpriseValueOverEBITDATTM: number;
    evToOperatingCashFlowTTM: number;
    evToFreeCashFlowTTM: number;
    earningsYieldTTM: number;
    freeCashFlowYieldTTM: number;
    debtToEquityTTM: number;
    debtToAssetsTTM: number;
    netDebtToEBITDATTM: number;
    currentRatioTTM: number;
    interestCoverageTTM: number;
    incomeQualityTTM: number;
    dividendYieldTTM: number;
    dividendYieldPercentageTTM: number;
    payoutRatioTTM: number;
    salesGeneralAndAdministrativeToRevenueTTM: number;
    researchAndDevelopementToRevenueTTM: number;
    intangiblesToTotalAssetsTTM: number;
    capexToOperatingCashFlowTTM: number;
    capexToRevenueTTM: number;
    capexToDepreciationTTM: number;
    stockBasedCompensationToRevenueTTM: number;
    grahamNumberTTM: number;
    roicTTM: number;
    returnOnTangibleAssetsTTM: number;
    grahamNetNetTTM: number;
    workingCapitalTTM: number;
    tangibleAssetValueTTM: number;
    netCurrentAssetValueTTM: number;
    investedCapitalTTM: number;
    averageReceivablesTTM: number;
    averagePayablesTTM: number;
    averageInventoryTTM: number;
    daysSalesOutstandingTTM: number;
    daysPayablesOutstandingTTM: number;
    daysOfInventoryOnHandTTM: number;
    receivablesTurnoverTTM: number;
    payablesTurnoverTTM: number;
    inventoryTurnoverTTM: number;
    roeTTM: number;
    capexPerShareTTM: number;
    dividendPerShareTTM: number;
    debtToMarketCapTTM: number;
  }
{% endhighlight %}

### Api

* api.tsx
{% highlight tsx %}
...
export const getKeyMetrics = async (query: string) => {
  try {
    const data = await axios.get<CompanyKeyMetrics[]>(
      `https://financialmodelingprep.com/api/v3/key-metrics-ttm/${query}?apikey=${process.env.REACT_APP_API_KEY}`
    );
    return data;
  } catch (error: any) {
    console.log("error message from API: ", error.message);
  }
}
{% endhighlight %}

### CompanyPage

* CompanyPage.tsx
{% highlight tsx %}
...
return (
    <>
      {company ? (
        <div className="w-full relative flex ct-docs-disable-sidebar-content overflow-x-hidden">
          <Sidebar />
          <CompanyDashboard ticker={ticker!}>
            <Tile title="Company Name" subTitle={company.companyName}/>
          </CompanyDashboard>
        </div>
      ) : (
        <p>Company not found!</p>
      )}
    </>
  )
{% endhighlight %}

### DesignPage

* DesignPage.tsx
{% highlight tsx %}
import React from 'react'
import Table from '../../components/Table/Table'
import RatioList from '../../components/RatioList/RatioList'
import { testIncomeStatementData } from '../../components/Table/testData';

type Props = {}

const tableConfig = [
  {
    label: "Market Cap",
    render: (company: any) => company.marketCapTTM,
    subTitle: "Total value of all a company's shares of stock",
  }
];

const DesignPage = (props: Props) => {
  return (
    <>
      <h1>FinShark Design Page</h1>
      <h2>This is Finshark's design page. This is where we well house various design aspects of the app</h2>
      <RatioList data={testIncomeStatementData} config={tableConfig}/>
      <Table />
    </>
  )
}

export default DesignPage
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/34.jpg"><img src="/assets/img/posts/react_finshark/34.jpg"></a>
	<figcaption>CompanyProfile</figcaption>
</figure>

# Income Statement
- create `IncomeStatement` folder in components folder
- create `IncomeStatement.tsx` and `IncomeStatement.css` in IncomeStatement folder

* IncomeStatement.tsx
{% highlight tsx %}
import React, { useEffect, useState } from 'react'
import { CompanyIncomeStatement } from '../../company';
import { useOutletContext } from 'react-router';
import { getIncomeStatement } from '../../api';
import Table from '../Table/Table';

type Props = {}

const configs = [
  {
    label: "Date",
    render: (company: CompanyIncomeStatement) => company.date,
  },
  {
    label: "Revenue",
    render: (company: CompanyIncomeStatement) => company.revenue,
  },
  {
    label: "Cost Of Revenue",
    render: (company: CompanyIncomeStatement) => company.costOfRevenue,
  },
  {
    label: "Depreciation",
    render: (company: CompanyIncomeStatement) =>
      company.depreciationAndAmortization,
  },
  {
    label: "Operating Income",
    render: (company: CompanyIncomeStatement) => company.operatingIncome,
  },
  {
    label: "Income Before Taxes",
    render: (company: CompanyIncomeStatement) => company.incomeBeforeTax,
  },
  {
    label: "Net Income",
    render: (company: CompanyIncomeStatement) => company.netIncome,
  },
  {
    label: "Net Income Ratio",
    render: (company: CompanyIncomeStatement) => company.netIncomeRatio,
  },
  {
    label: "Earnings Per Share",
    render: (company: CompanyIncomeStatement) => company.eps,
  },
  {
    label: "Earnings Per Diluted",
    render: (company: CompanyIncomeStatement) => company.epsdiluted,
  },
  {
    label: "Gross Profit Ratio",
    render: (company: CompanyIncomeStatement) => company.grossProfitRatio,
  },
  {
    label: "Opearting Income Ratio",
    render: (company: CompanyIncomeStatement) => company.operatingIncomeRatio,
  },
  {
    label: "Income Before Taxes Ratio",
    render: (company: CompanyIncomeStatement) => company.incomeBeforeTaxRatio,
  },
];

const IncomeStatement = (props: Props) => {
  const ticker = useOutletContext<string>();
  const [incomeStatement, setIncomeStatement] = useState<CompanyIncomeStatement[]>();
  
  useEffect(() => {
    const getCompanyIncomeStatement = async () => {
      const result = await getIncomeStatement(ticker!);
      setIncomeStatement(result!.data);
    }
    getCompanyIncomeStatement();
  }, []);

  return (
    <>
      { incomeStatement ? (
        <Table configs={configs} data={incomeStatement}/>
      ):(
        <>Loading...</>
      )}
    </>
  )
}

export default IncomeStatement
{% endhighlight %}

### Sidebar

* Sidebar.tsx
{% highlight tsx %}
...
<Link to="company-profile" className="flex md:min-w-full text-blueGray-500 text-xs uppercase font-bold block pt-1 pb-4 no-underline">
            <FaHome/>
            <h6 className="ml-3">Company Profile</h6>
          </Link>
          <Link to="income-statement" className="flex md:min-w-full text-blueGray-500 text-xs uppercase font-bold block pt-1 pb-4 no-underline">
            <FaHome/>
            <h6 className="ml-3">Income Statement</h6>
          </Link>
...
{% endhighlight %}

### Table

* Table.tsx
{% highlight tsx %}
type Props = {
  configs: any;
  data: any;
}

const Table = ({ configs, data }: Props) => {
  const renderedRows = data.map((company: any) => {
    return (
      <tr key={company.cik}>
        {configs.map((val: any) => {
          return (
            <td className="p-3">
              {val.render(company)}
            </td>
        )})}
      </tr>
    )
  })
  const renderedHeaders = configs.map((configs: any) => {
    return (
      <th className="p-3" key={configs.label}>
        {configs.label}
      </th>
    )
  })

  return (
    <div className="bg-white shadow rounded-lg p-4 sm:p-6 xl:p-8">
      <table>
        <thead className="min-w-full divide-y divide-gray-200 m-5">
          {renderedHeaders}
        </thead>
        <tbody>
          {renderedRows}
        </tbody>
      </table>
    </div>
  )
}

export default Table
{% endhighlight %}

### DesignPage

* DesignPage.tsx
{% highlight tsx %}
...
const DesignPage = (props: Props) => {
  return (
    <>
      <h1>FinShark Design Page</h1>
      <h2>This is Finshark's design page. This is where we well house various design aspects of the app</h2>
      <RatioList data={testIncomeStatementData} config={tableConfig}/>
      <Table data={testIncomeStatementData} configs={tableConfig} />
    </>
  )
...
{% endhighlight %}

### Routes

* Routes.tsx
{% highlight tsx %}
...
{ path: "company/:ticker", element: <CompanyPage/>,
        children: [
          { path: "company-profile", element: <CompanyProfile/> },
          { path: "income-statement", element: <IncomeStatement/> }
        ]
      }
...
{% endhighlight %}

### Api

* api.tsx
{% highlight tsx %}
...
export const getIncomeStatement = async (query: string) => {
  try {
    const data = await axios.get<CompanyIncomeStatement[]>(
      `https://financialmodelingprep.com/api/v3/income-statement/${query}?apikey=${process.env.REACT_APP_API_KEY}`
    );
    return data;
  } catch (error: any) {
    console.log("error message from API: ", error.message);
  }
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/35.jpg"><img src="/assets/img/posts/react_finshark/35.jpg"></a>
	<figcaption>Income Statement</figcaption>
</figure>

# Balance Sheet
- create `BalanceSheet` folder in components folder
- create `BalanceSheet.tsx` and `BalanceSheet.css` in BalanceSheet folder

* BalanceSheet.tsx
{% highlight tsx %}
import React, { useEffect, useState } from 'react'
import { CompanyBalanceSheet, CompanyCashFlow } from '../../company';
import { useOutletContext } from 'react-router';
import { getBalanceSheet } from '../../api';
import RatioList from '../RatioList/RatioList';

type Props = {}

const config = [
  {
    label: "Cash",
    render: (company: CompanyBalanceSheet) => company.cashAndCashEquivalents,
  },
  {
    label: "Inventory",
    render: (company: CompanyBalanceSheet) => company.inventory,
  },
  {
    label: "Other Current Assets",
    render: (company: CompanyBalanceSheet) => company.otherCurrentAssets,
  },
  {
    label: "Minority Interest",
    render: (company: CompanyBalanceSheet) => company.minorityInterest,
  },
  {
    label: "Other Non-Current Assets",
    render: (company: CompanyBalanceSheet) => company.otherNonCurrentAssets,
  },
  {
    label: "Long Term Debt",
    render: (company: CompanyBalanceSheet) => company.longTermDebt,
  },
  {
    label: "Total Debt",
    render: (company: CompanyBalanceSheet) => company.otherCurrentLiabilities,
  },
];

const BalanceSheet = (props: Props) => {
  const ticker = useOutletContext<string>();
  const [balanceSheet, setBalanceSheet] = useState<CompanyBalanceSheet>();

  useEffect(() => {
    const getCompanyBalanceSheet = async() => {
      const result = await getBalanceSheet(ticker!)
      setBalanceSheet(result?.data[0]);
    }
    getCompanyBalanceSheet();
  }, [])

  return (
    <>
      { balanceSheet ? (
        <RatioList config={config} data={balanceSheet}/>
      ) : (
        <>Loading...</>
      )}
    </>
  )
}

export default BalanceSheet
{% endhighlight %}

### Api

* api.tsx
{% highlight tsx %}
...
export const getBalanceSheet = async (query: string) => {
  try {
    const data = await axios.get<CompanyBalanceSheet[]>(
      `https://financialmodelingprep.com/api/v3/balance-sheet-statement/${query}?apikey=${process.env.REACT_APP_API_KEY}`
    );
    return data;
  } catch (error: any) {
    console.log("error message from API: ", error.message);
  }
}
{% endhighlight %}

### Routes

* Routes.tsx
{% highlight tsx %}
...
{ path: "company/:ticker", element: <CompanyPage/>,
        children: [
          { path: "company-profile", element: <CompanyProfile/> },
          { path: "income-statement", element: <IncomeStatement/> },
          { path: "balance-sheet", element: <BalanceSheet/> }
        ]
      }
...
{% endhighlight %}

### Sidebar

* Sidebar.tsx
{% highlight tsx %}
...
<Link to="balance-sheet" className="flex md:min-w-full text-blueGray-500 text-xs uppercase font-bold block pt-1 pb-4 no-underline">
            <FaHome/>
            <h6 className="ml-3">Balance Sheet</h6>
          </Link>
...
{% endhighlight %}

* RatioList.tsx
{% highlight tsx %}
...
 return (
    <div className="bg-white shadow rounded-lg ml-4 mt-4 mb-4 p-4 sm:p-6 w-full">
      <ul className="divide-y divided-gray-200">
        {renderedRows}
      </ul>
    </div>
  )
{% endhighlight %}

* RatioList.tsx
{% highlight tsx %}
...
 return (
    <div className="bg-white shadow rounded-lg ml-4 mt-4 mb-4 p-4 sm:p-6 w-full">
      <ul className="divide-y divided-gray-200">
        {renderedRows}
      </ul>
    </div>
  )
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/36.jpg"><img src="/assets/img/posts/react_finshark/36.jpg"></a>
	<figcaption>Balance Sheet</figcaption>
</figure>

# Cash Flow Statement
- create `CashFlowStatement` folder in components folder
- create `CashFlowStatement.tsx` and `CashFlowStatement.css` in CashFlowStatement folder

* CashFlowStatement.tsx
{% highlight tsx %}
import React, { useEffect, useState } from 'react'
import { CompanyCashFlow } from '../../company';
import { getCashFlowStatement } from '../../api';
import { useOutletContext } from 'react-router';
import RatioList from '../RatioList/RatioList';

type Props = {}

const config = [
  {
    label: "Date",
    render: (company: CompanyCashFlow) => company.date,
  },
  {
    label: "Operating Cashflow",
    render: (company: CompanyCashFlow) => company.operatingCashFlow,
  },
  {
    label: "Property/Machinery Cashflow",
    render: (company: CompanyCashFlow) =>
      company.investmentsInPropertyPlantAndEquipment,
  },
  {
    label: "Other Investing Cashflow",
    render: (company: CompanyCashFlow) => company.otherInvestingActivites,
  },
  {
    label: "Debt Cashflow",
    render: (company: CompanyCashFlow) =>
      company.netCashUsedProvidedByFinancingActivities,
  },
  {
    label: "CapEX",
    render: (company: CompanyCashFlow) => company.capitalExpenditure,
  },
  {
    label: "Free Cash Flow",
    render: (company: CompanyCashFlow) => company.freeCashFlow,
  },
];

const CashFlowStatement = (props: Props) => {
  const ticker = useOutletContext<string>();
  const [cashFlowStatement, setCashFlowStatement] = useState<CompanyCashFlow>();
  useEffect(() => {
    const getCompanyCashFlowStatement = async () => {
      const result = await getCashFlowStatement(ticker);
      setCashFlowStatement(result?.data[0]);
    }
    getCompanyCashFlowStatement();
  }, []);

  return (
    <>
      { cashFlowStatement ? (
        <RatioList config={config} data={cashFlowStatement}/>
      ) : (
        <>Loading...</>
      )}
    </>
  )
}

export default CashFlowStatement
{% endhighlight %}

### Api

* api.tsx
{% highlight tsx %}
...
export const getCashFlowStatement = async (query: string) => {
  try {
    const data = await axios.get<CompanyCashFlow[]>(
      `https://financialmodelingprep.com/api/v3/cash-flow-statement/${query}?apikey=${process.env.REACT_APP_API_KEY}`
    );
    return data;
  } catch (error: any) {
    console.log("error message from API: ", error.message);
  }
}
...
{% endhighlight %}

### Routes

* Routes.tsx
{% highlight tsx %}
...
{ path: "company/:ticker", element: <CompanyPage/>,
        children: [
          { path: "company-profile", element: <CompanyProfile/> },
          { path: "income-statement", element: <IncomeStatement/> },
          { path: "balance-sheet", element: <BalanceSheet/> },
          { path: "cashflow-statement", element: <CashFlowStatement/> }
        ]
      }
...
{% endhighlight %}

### Routes

* Routes.tsx
{% highlight tsx %}
...
{ path: "company/:ticker", element: <CompanyPage/>,
        children: [
          { path: "company-profile", element: <CompanyProfile/> },
          { path: "income-statement", element: <IncomeStatement/> },
          { path: "balance-sheet", element: <BalanceSheet/> },
          { path: "cashflow-statement", element: <CashFlowStatement/> }
        ]
      }
...
{% endhighlight %}

### Routes

* Sidebar.tsx
{% highlight tsx %}
...
<Link to="cashflow-statement" className="flex md:min-w-full text-blueGray-500 text-xs uppercase font-bold block pt-1 pb-4 no-underline">
            <FaHome/>
            <h6 className="ml-3">Cash Flow Statement</h6>
          </Link>
...
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/37.jpg"><img src="/assets/img/posts/react_finshark/37.jpg"></a>
	<figcaption>Cash Flow Statement</figcaption>
</figure>

# Spinner
- type `npm install react-spinners`
- create `Spinner` folder in components folder
- create `Spinner.tsx` and `Spinner.css` in Spinner folder

* Spinner.tsx
{% highlight tsx %}
import { ClipLoader } from 'react-spinners';
import "./Spinner.css"

type Props = {
  isLoading? : boolean;
}

const Spinner = ({ isLoading = true }: Props) => {
  return (
    <>
      <div id="loading-spinner">
        <ClipLoader color="#36d7b7" loading={isLoading} size={35} aria-label="Loading Spinner" data-testid="loader"/>
      </div>
    </>
  )
}

export default Spinner
{% endhighlight %}

### BalanceSheet

* BalanceSheet.tsx
{% highlight tsx %}
...
return (
    <>
      { balanceSheet ? (
        <RatioList config={config} data={balanceSheet}/>
      ) : (
        <Spinner/>
      )}
    </>
  )
{% endhighlight %}

### CashFlowStatement

* CashFlowStatement.tsx
{% highlight tsx %}
...
return (
    <>
      { cashFlowStatement ? (
        <RatioList config={config} data={cashFlowStatement}/>
      ) : (
        <Spinner/>
      )}
    </>
  )
{% endhighlight %}

### CashFlowStatement

* CompanyProfile.tsx
{% highlight tsx %}
...
return (
    <>
      { companyData ? (
        <RatioList data={companyData} config={tableConfig}/>
      ):(
        <Spinner/>
      )}
    </>
  )
{% endhighlight %}

### IncomeStatement

* IncomeStatement.tsx
{% highlight tsx %}
...
return (
    <>
      { incomeStatement ? (
        <Table configs={configs} data={incomeStatement}/>
      ):(
        <Spinner/>
      )}
    </>
  )
{% endhighlight %}

### CompanyPage

* CompanyPage.tsx
{% highlight tsx %}
...
return (
    <>
      {company ? (
        <div className="w-full relative flex ct-docs-disable-sidebar-content overflow-x-hidden">
          <Sidebar />
          <CompanyDashboard ticker={ticker!}>
            <Tile title="Company Name" subTitle={company.companyName}/>
          </CompanyDashboard>
        </div>
      ) : (
        <Spinner/>
      )}
    </>
  )
{% endhighlight %}

# Formatting

## Number Formatting
- create `Helpers` folder in components folder
- create `NumberFormatting.tsx` in Helpers folder

* NumberFormatting.tsx
{% highlight tsx %}
export const formatLargeMonetaryNumber: any = (number: number) => {
  if (number < 0) {
    return "-" + formatLargeMonetaryNumber(-1 * number);
  }
  if (number < 1000) {
    return "$" + number;
  } else if (number >= 1000 && number < 1_000_000) {
    return "$" + (number / 1000).toFixed(1) + "K";
  } else if (number >= 1_000_000 && number < 1_000_000_000) {
    return "$" + (number / 1_000_000).toFixed(1) + "M";
  } else if (number >= 1_000_000_000 && number < 1_000_000_000_000) {
    return "$" + (number / 1_000_000_000).toFixed(1) + "B";
  } else if (number >= 1_000_000_000_000 && number < 1_000_000_000_000_000) {
    return "$" + (number / 1_000_000_000_000).toFixed(1) + "T";
  }
};

export const formatLargeNonMonetaryNumber: any = (number: number) => {
  if (number < 0) {
    return "-" + formatLargeMonetaryNumber(-1 * number);
  }
  if (number < 1000) {
    return number;
  } else if (number >= 1000 && number < 1_000_000) {
    return (number / 1000).toFixed(1) + "K";
  } else if (number >= 1_000_000 && number < 1_000_000_000) {
    return (number / 1_000_000).toFixed(1) + "M";
  } else if (number >= 1_000_000_000 && number < 1_000_000_000_000) {
    return (number / 1_000_000_000).toFixed(1) + "B";
  } else if (number >= 1_000_000_000_000 && number < 1_000_000_000_000_000) {
    return (number / 1_000_000_000_000).toFixed(1) + "T";
  }
};

export const formatRatio = (ratio: number) => {
  return (Math.round(ratio * 100) / 100).toFixed(2);
};
{% endhighlight %}

### BalanceSheet

* BalanceSheet.tsx
{% highlight tsx %}
const config = [
  {
    label: <div className="font-bold">Total Assets</div>,
    render: (company: CompanyBalanceSheet) => formatLargeMonetaryNumber(company.totalAssets)
  },
  {
    label: "Current Assets",
    render: (company: CompanyBalanceSheet) => formatLargeMonetaryNumber(company.totalCurrentAssets)
  },
  {
    label: "Total Cash",
    render: (company: CompanyBalanceSheet) => formatLargeMonetaryNumber(company.cashAndCashEquivalents)
  },
  {
    label: "Property & equipment",
    render: (company: CompanyBalanceSheet) => formatLargeMonetaryNumber(company.propertyPlantEquipmentNet)
  },
  {
    label: "Intangible Assets",
    render: (company: CompanyBalanceSheet) => formatLargeMonetaryNumber(company.intangibleAssets)
  },
  {
    label: "Long Term Debt",
    render: (company: CompanyBalanceSheet) => formatLargeMonetaryNumber(company.longTermDebt)
  },
  {
    label: "Total Debt",
    render: (company: CompanyBalanceSheet) => formatLargeMonetaryNumber(company.otherCurrentLiabilities)
  },
  {
    label: <div className="font-bold">Total Liabilites</div>,
    render: (company: CompanyBalanceSheet) => formatLargeMonetaryNumber(company.totalLiabilities)
  },
  {
    label: "Current Liabilities",
    render: (company: CompanyBalanceSheet) => formatLargeMonetaryNumber(company.totalCurrentLiabilities)
  },
  {
    label: "Long-Term Debt",
    render: (company: CompanyBalanceSheet) => formatLargeMonetaryNumber(company.longTermDebt)
  },
  {
    label: "Long-Term Income Taxes",
    render: (company: CompanyBalanceSheet) => formatLargeMonetaryNumber(company.otherLiabilities)
  },
  {
    label: "Stakeholder's Equity",
    render: (company: CompanyBalanceSheet) => formatLargeMonetaryNumber(company.totalStockholdersEquity)
  },
  {
    label: "Retained Earnings",
    render: (company: CompanyBalanceSheet) => formatLargeMonetaryNumber(company.retainedEarnings)
  },
];
...
{% endhighlight %}

### CashFlowStatement

* CashFlowStatement.tsx
{% highlight tsx %}
const config = [
  {
    label: "Date",
    render: (company: CompanyCashFlow) => company.date
  },
  {
    label: "Operating Cashflow",
    render: (company: CompanyCashFlow) => formatLargeMonetaryNumber(company.operatingCashFlow)
  },
  {
    label: "Investing Cashflow",
    render: (company: CompanyCashFlow) => formatLargeMonetaryNumber(company.netCashUsedForInvestingActivites)
  },
  {
    label: "Financing Cashflow",
    render: (company: CompanyCashFlow) => formatLargeMonetaryNumber(company.netCashUsedProvidedByFinancingActivities)
  },
  {
    label: "Cash At End of Period",
    render: (company: CompanyCashFlow) => formatLargeMonetaryNumber(company.cashAtEndOfPeriod)
  },
  {
    label: "CapEX",
    render: (company: CompanyCashFlow) => formatLargeMonetaryNumber(company.capitalExpenditure)
  },
  {
    label: "Issuance Of Stock",
    render: (company: CompanyCashFlow) => formatLargeMonetaryNumber(company.commonStockIssued)
  },
  {
    label: "Free Cash Flow",
    render: (company: CompanyCashFlow) => formatLargeMonetaryNumber(company.freeCashFlow)
  },
];
...
{% endhighlight %}

### CompanyProfile

* CompanyProfile.tsx
{% highlight tsx %}
const tableConfig = [
  {
    label: "Market Cap",
    render: (company: CompanyKeyMetrics) => formatLargeMonetaryNumber(company.marketCapTTM),
    subTitle: "Total value of all a company's shares of stock",
  },
  {
    label: "Current Ratio",
    render: (company: CompanyKeyMetrics) => formatRatio(company.currentRatioTTM),
    subTitle:
      "Measures the companies ability to pay short term debt obligations",
  },
  {
    label: "Return On Equity",
    render: (company: CompanyKeyMetrics) => formatRatio(company.roeTTM),
    subTitle:
      "Return on equity is the measure of a company's net income divided by its shareholder's equity",
  },
  {
    label: "Return On Assets",
    render: (company: CompanyKeyMetrics) => formatRatio(company.returnOnTangibleAssetsTTM),
    subTitle:
      "Return on assets is the measure of how effective a company is using its assets",
  },
  {
    label: "Free Cashflow Per Share",
    render: (company: CompanyKeyMetrics) => formatRatio(company.freeCashFlowPerShareTTM),
    subTitle:
      "Return on assets is the measure of how effective a company is using its assets",
  },
  {
    label: "Book Value Per Share TTM",
    render: (company: CompanyKeyMetrics) => formatRatio(company.bookValuePerShareTTM),
    subTitle:
      "Book value per share indicates a firm's net asset value (total assets - total liabilities) on per share basis",
  },
  {
    label: "Divdend Yield TTM",
    render: (company: CompanyKeyMetrics) => formatRatio(company.dividendYieldTTM),
    subTitle: "Shows how much a company pays each year relative to stock price",
  },
  {
    label: "Capex Per Share TTM",
    render: (company: CompanyKeyMetrics) => formatRatio(company.capexPerShareTTM),
    subTitle:
      "Capex is used by a company to aquire, upgrade, and maintain physical assets",
  },
  {
    label: "Graham Number",
    render: (company: CompanyKeyMetrics) => formatRatio(company.grahamNumberTTM),
    subTitle:
      "This is the upperbouind of the price range that a defensive investor should pay for a stock",
  },
  {
    label: "PE Ratio",
    render: (company: CompanyKeyMetrics) => formatRatio(company.peRatioTTM),
    subTitle:
      "This is the upperbouind of the price range that a defensive investor should pay for a stock",
  },
];
...
{% endhighlight %}

### IncomeStatement

* IncomeStatement.tsx
{% highlight tsx %}
const configs = [
  {
    label: "Date",
    render: (company: CompanyIncomeStatement) => company.date,
  },
  {
    label: "Revenue",
    render: (company: CompanyIncomeStatement) => formatLargeMonetaryNumber(company.revenue)
  },
  {
    label: "Cost Of Revenue",
    render: (company: CompanyIncomeStatement) => formatLargeMonetaryNumber(company.costOfRevenue)
  },
  {
    label: "Depreciation",
    render: (company: CompanyIncomeStatement) => formatLargeMonetaryNumber(company.depreciationAndAmortization)
  },
  {
    label: "Operating Income",
    render: (company: CompanyIncomeStatement) => formatLargeMonetaryNumber(company.operatingIncome)
  },
  {
    label: "Income Before Taxes",
    render: (company: CompanyIncomeStatement) => formatLargeMonetaryNumber(company.incomeBeforeTax)
  },
  {
    label: "Net Income",
    render: (company: CompanyIncomeStatement) => formatLargeMonetaryNumber(company.netIncome)
  },
  {
    label: "Net Income Ratio",
    render: (company: CompanyIncomeStatement) => formatRatio(company.netIncomeRatio)
  },
  {
    label: "Earnings Per Share",
    render: (company: CompanyIncomeStatement) => formatRatio(company.eps)
  },
  {
    label: "Earnings Per Diluted",
    render: (company: CompanyIncomeStatement) => formatRatio(company.epsdiluted)
  },
  {
    label: "Gross Profit Ratio",
    render: (company: CompanyIncomeStatement) => formatRatio(company.grossProfitRatio)
  },
  {
    label: "Opearting Income Ratio",
    render: (company: CompanyIncomeStatement) => formatRatio(company.operatingIncomeRatio)
  },
  {
    label: "Income Before Taxes Ratio",
    render: (company: CompanyIncomeStatement) => formatRatio(company.incomeBeforeTaxRatio)
  },
];
...
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/38.jpg"><img src="/assets/img/posts/react_finshark/38.jpg"></a>
	<figcaption>Formatting</figcaption>
</figure>

# Api
- create `Api` application as `.net Asp Core Web Api` with visual studio

<figure>
  <a href="/assets/img/posts/react_finshark/18.jpg"><img src="/assets/img/posts/react_finshark/18.jpg"></a>
	<figcaption>Api</figcaption>
</figure>

# SQL Server Management Studio
- download <a href="https://www.microsoft.com/en-us/sql-server/sql-server-downloads">SQL Server</a>
- download <a href="https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver16">SQL Server Management Studio</a>

<figure class="half">
  <a href="/assets/img/posts/react_finshark/14.jpg"><img src="/assets/img/posts/react_finshark/14.jpg"></a>
  <a href="/assets/img/posts/react_finshark/17.jpg"><img src="/assets/img/posts/react_finshark/17.jpg"></a>
	<figcaption>SQL Server Management studio</figcaption>
</figure>

# Models
- create `Model` folder in Api project

<figure>
  <a href="/assets/img/posts/react_finshark/19.jpg"><img src="/assets/img/posts/react_finshark/19.jpg"></a>
	<figcaption>Models</figcaption>
</figure>

### Stock.cs
{% highlight cs %}
using System.ComponentModel.DataAnnotations.Schema;

namespace Api.Models
{
    public class Stock
    {
        public int Id { get; set; }
        public string Symbol { get; set; } = string.Empty;
        public string CompanyName { get; set; } = string.Empty;
        [Column(TypeName="decimal(18, 2)")]
        public decimal Purchase { get; set; }
        [Column(TypeName="decimal(18, 2)")]
        public decimal LastDiv { get; set; }
        public string Industry { get; set; } = string.Empty;
        public long MarketCap { get; set; }
        public List<Comment> Comments { get; set; } = new List<Comment>();
    }
}
{% endhighlight %}

### Comment.cs
{% highlight cs %}
namespace Api.Models
{
    public class Comment
    {
        public int Id { get; set; }
        public string Title { get; set; } = string.Empty;
        public string Content { get; set; } = string.Empty;
        public DateTime CreatedOn { get; set; } = DateTime.Now;
        public int? StockId { get; set; }
        public Stock? Stock { get; set; }
    }
}
{% endhighlight %}

# Entity Framework
- open `Nuget Package Manager` in Api project
- download `Microsoft.EntityFrameworkCore.Design`
- download `Microsoft.EntityFrameworkCore.Tools`
- download `Microsoft.EntityFrameworkCore.SqlServer`

<figure>
  <a href="/assets/img/posts/react_finshark/20.jpg"><img src="/assets/img/posts/react_finshark/20.jpg"></a>
	<figcaption>Entity Framework</figcaption>
</figure>

### SQL Server Management Studio
- create new `finshark` database 

<figure>
  <a href="/assets/img/posts/react_finshark/21.jpg"><img src="/assets/img/posts/react_finshark/21.jpg"></a>
	<figcaption>Entity Framework</figcaption>
</figure>

### DBContext
- create `Data` folder in Api project
- create `ApplicationDBContext.cs` in Data folder

* ApplicationDBContext.cs
{% highlight cs %}
using Api.Models;
using Microsoft.EntityFrameworkCore;

namespace Api.Data
{
    public class ApplicationDBContext : DbContext
    {
        public ApplicationDBContext(DbContextOptions dbContextOptions): base(dbContextOptions)
        {
        }

        public DbSet<Stock> Stocks { get; set; }
        public DbSet<Comment> Comments { get; set; }
    }
}
{% endhighlight %}

### Program

* Program.cs
{% highlight cs %}
...
builder.Services.AddDbContext<ApplicationDBContext>(options =>
{
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"));
});

var app = builder.Build();
...
{% endhighlight %}

* appsettigs.json
{% highlight json %}
{
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=YOUR_DESKTOP;Initial Catalog=finshark;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False"
  },
  ...
}
{% endhighlight %}

### Database
- open `Developer Power Shell` in visual studio
- type `doenet ef migrations add init`
- type `doenet ef database update`

<figure class="third">
  <a href="/assets/img/posts/react_finshark/22.jpg"><img src="/assets/img/posts/react_finshark/22.jpg"></a>
  <a href="/assets/img/posts/react_finshark/23.jpg"><img src="/assets/img/posts/react_finshark/23.jpg"></a>
  <a href="/assets/img/posts/react_finshark/24.jpg"><img src="/assets/img/posts/react_finshark/24.jpg"></a>
	<figcaption>Entity Framework</figcaption>
</figure>

### SQL Server Management Studio
- right click `dbo.Stocks` table in finshark database
- click `Edit Top 200 Rows`
- insert some values

<figure>
  <a href="/assets/img/posts/react_finshark/25.jpg"><img src="/assets/img/posts/react_finshark/25.jpg"></a>
	<figcaption>Controllers</figcaption>
</figure>

# Stock

### Controller
- create `StockController.cs` in Controllers folder

* StockController.cs
{% highlight cs %}
using Api.Dtos.Stock;
using Api.Interfaces;
using Api.Mappers;
using Microsoft.AspNetCore.Mvc;

namespace Api.Controllers
{
    [Route("api/stock")]
    [ApiController]
    public class StockController : ControllerBase
    {
        private readonly IStockRepository _stockRepository;

        public StockController(IStockRepository stockRepository)
        {
            _stockRepository = stockRepository;
        }

        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            var stock = await _stockRepository.GetAllAsync();
            var stockDto = stock.Select(s => s.ToStockDto());

            return Ok(stock);
        }

        [HttpGet]
        [Route("{id}")]
        public async Task<IActionResult> GetById([FromRoute] int id)
        {
            var stock = await _stockRepository.GetByIdAsync(id);

            if (stock == null)
            {
                return NotFound();
            }

            return Ok(stock.ToStockDto());
        }

        [HttpPost]
        public async Task<IActionResult> Create([FromBody] CreateStockRequestDto stockDto)
        {
            var stockModel = stockDto.ToStockFromCreateDto();
            await _stockRepository.CreateAsync(stockModel);

            return CreatedAtAction(nameof(GetById), new { id = stockModel.Id}, stockModel.ToStockDto());
        }

        [HttpPut]
        [Route("{id}")]
        public async Task<IActionResult> Update([FromRoute] int id, [FromBody] UpdateStockRequestDto updateDto)
        {
            var stockModel = await _stockRepository.UpdateAsync(id, updateDto);

            if (stockModel == null)
            {
                return NotFound();
            }

            return Ok(stockModel.ToStockDto());
        }

        [HttpDelete]
        [Route("{id}")]
        public async Task<IActionResult> Delete([FromRoute] int id)
        {
            var stockModel = await _stockRepository.DeleteAsync(id);

            if (stockModel == null)
            {
                return NotFound();
            }

            return NoContent();
        }
    }
}
{% endhighlight %}

### DTOs
- create `Dtos` folder in Api folder
- create `Stock` folder in Dtos folder
- create `StockDto.cs` in Stock folder

* StockDto.cs
{% highlight cs %}
using Api.Dtos.Comment;

namespace Api.Dtos.Stock
{
    public class StockDto
    {
        public int Id { get; set; }
        public string Symbol { get; set; } = string.Empty;
        public string CompanyName { get; set; } = string.Empty;
        public decimal Purchase { get; set; }
        public decimal LastDiv { get; set; }
        public string Industry { get; set; } = string.Empty;
        public long MarketCap { get; set; }
        public List<CommentDto>? Comments { get; set; }
    }
}
{% endhighlight %}

- create `CreateStockRequestDto.cs` in Stock folder

* CreateStockRequestDto.cs
{% highlight cs %}
namespace Api.Dtos.Stock
{
    public class CreateStockRequestDto
    {
        public string Symbol { get; set; } = string.Empty;
        public string CompanyName { get; set; } = string.Empty;
        public decimal Purchase { get; set; }
        public decimal LastDiv { get; set; }
        public string Industry { get; set; } = string.Empty;
        public long MarketCap { get; set; }
    }
}
{% endhighlight %}

- create `UpdateStockRequestDto.cs` in Stock folder

* UpdateStockRequestDto.cs
{% highlight cs %}
namespace Api.Dtos.Stock
{
    public class UpdateStockRequestDto
    {
        public string Symbol { get; set; } = string.Empty;
        public string CompanyName { get; set; } = string.Empty;
        public decimal Purchase { get; set; }
        public decimal LastDiv { get; set; }
        public string Industry { get; set; } = string.Empty;
        public long MarketCap { get; set; }
    }
}
{% endhighlight %}

### Mapper
- create `Mappers` folder in Api folder
- create `StockMappers.cs` in Mappers folder

* StockMappers.cs
{% highlight cs %}
using Api.Dtos.Stock;
using Api.Models;

namespace Api.Mappers
{
    public static class StockMappers
    {
        public static StockDto ToStockDto(this Stock stockModel)
        {
            return new StockDto
            {
                Id = stockModel.Id,
                Symbol = stockModel.Symbol,
                CompanyName = stockModel.CompanyName,
                Purchase = stockModel.Purchase,
                LastDiv = stockModel.LastDiv,
                Industry = stockModel.Industry,
                MarketCap = stockModel.MarketCap,
                Comments = stockModel.Comments.Select(c => c.ToCommentDto()).ToList(),
            };
        }

        public static Stock ToStockFromCreateDto(this CreateStockRequestDto stockDto)
        {
            return new Stock
            {
                Symbol = stockDto.Symbol,
                CompanyName = stockDto.CompanyName,
                Purchase = stockDto.Purchase,
                LastDiv = stockDto.LastDiv,
                Industry = stockDto.Industry,
                MarketCap = stockDto.MarketCap
            };
        }
    }
}
{% endhighlight %}

### Interfaces
- create `Interfaces` folder in Api folder
- create `IStockRepository.cs` in Interfaces folder

* IStockRepository.cs
{% highlight cs %}
using Api.Dtos.Stock;
using Api.Models;

namespace Api.Interfaces
{
    public interface IStockRepository
    {
        Task<List<Stock>> GetAllAsync();
        Task<Stock?> GetByIdAsync(int id);
        Task<Stock?> CreateAsync(Stock stockModel);
        Task<Stock?> UpdateAsync(int id, UpdateStockRequestDto stockModel);
        Task<Stock?> DeleteAsync(int id);
        Task<bool> StockExists(int id);
    }
}
{% endhighlight %}

### Repository
- create `Repository` folder in Api folder
- create `StockRepository.cs` in Repository folder

* StockRepository.cs
{% highlight cs %}
using Api.Data;
using Api.Dtos.Stock;
using Api.Interfaces;
using Api.Models;
using Microsoft.EntityFrameworkCore;

namespace Api.Repository
{
    public class StockRepository : IStockRepository
    {
        private readonly ApplicationDBContext _context;

        public StockRepository(ApplicationDBContext context)
        {
            _context = context;
        }

        public async Task<List<Stock>> GetAllAsync()
        {
            return await _context.Stocks.Include(c => c.Comments).ToListAsync();
        }

        public async Task<Stock?> GetByIdAsync(int id)
        {
            return await _context.Stocks.FirstOrDefaultAsync(x => x.Id == id);
        }

        public async Task<bool> StockExists(int id)
        {
            return await _context.Stocks.AnyAsync(s => s.Id == id);
        }

        public async Task<Stock?> CreateAsync(Stock stockModel)
        {
            await _context.Stocks.AddAsync(stockModel);
            await _context.SaveChangesAsync();
            return stockModel;
        }

        public async Task<Stock?> UpdateAsync(int id, UpdateStockRequestDto stockModel)
        {
            var existingStock = await _context.Stocks.FirstOrDefaultAsync(x => x.Id == id);

            if (existingStock == null)
            {
                return null;
            }

            existingStock.Symbol = stockModel.Symbol;
            existingStock.CompanyName = stockModel.CompanyName;
            existingStock.Purchase = stockModel.Purchase;
            existingStock.LastDiv = stockModel.LastDiv;
            existingStock.MarketCap = stockModel.MarketCap;

            await _context.SaveChangesAsync();
            return existingStock;
        }

        public async Task<Stock?> DeleteAsync(int id)
        {
            var stockModel = await _context.Stocks.FirstOrDefaultAsync(x => x.Id == id);

            if (stockModel == null)
            {
                return null;
            }

            _context.Stocks.Remove(stockModel);
            await _context.SaveChangesAsync();
            return stockModel;
        }
    }
}
{% endhighlight %}

### Program

* Program.cs
{% highlight cs %}
...
builder.Services.AddScoped<IStockRepository, StockRepository>();
...
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/react_finshark/39.jpg"><img src="/assets/img/posts/react_finshark/39.jpg"></a>
  <a href="/assets/img/posts/react_finshark/40.jpg"><img src="/assets/img/posts/react_finshark/40.jpg"></a>
  <a href="/assets/img/posts/react_finshark/41.jpg"><img src="/assets/img/posts/react_finshark/41.jpg"></a>
	<figcaption>Stock</figcaption>
</figure>

# Comment


### Controller
- create `CommentController.cs` in Controllers folder

* CommentController.cs
{% highlight cs %}
using Api.Dtos.Comment;
using Api.Interfaces;
using Api.Mappers;
using Microsoft.AspNetCore.Mvc;

namespace Api.Controllers
{
    [Route("api/comment")]
    [ApiController]
    public class CommentController : ControllerBase
    {
        private readonly ICommentRepository _commentRepository;
        private readonly IStockRepository _stockRepository;

        public CommentController(ICommentRepository commentRepository, IStockRepository stockRepository)
        {
            _commentRepository = commentRepository;
            _stockRepository = stockRepository;
        }

        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            var comments = await _commentRepository.GetAllAsync();

            var commentDto = comments.Select(s => s.ToCommentDto());

            return Ok(commentDto);
        }

        [HttpGet]
        [Route("{id}")]
        public async Task<IActionResult> GetById([FromRoute] int id)
        {
            var comment = await _commentRepository.GetByIdAsync(id);

            if (comment == null)
            {
                return NotFound();
            }

            return Ok(comment.ToCommentDto());
        }

        [HttpPost]
        [Route("{stockId}")]
        public async Task<IActionResult> Create([FromRoute] int stockId, [FromBody] CreateCommentDto commentDto)
        {
            if (!await _stockRepository.StockExists(stockId))
            {
                return BadRequest("Stock does not exist");
            }

            var commentModel = commentDto.ToCommentFromCreate(stockId);
            await _commentRepository.CreateAsync(commentModel);

            return CreatedAtAction(nameof(GetById), new { id = commentModel.Id }, commentModel.ToCommentDto());
        }

        [HttpPut]
        [Route("{id}")]
        public async Task<IActionResult> Update([FromRoute] int id, [FromBody] UpdateCommentRequestDto updateDto)
        {
            var comment = await _commentRepository.UpdateAsync(id, updateDto.ToCommentFromUpdate());

            if (comment == null)
            {
                return NotFound("Comment not found");
            }

            return Ok(comment.ToCommentDto());
        }

        [HttpDelete]
        [Route("{id}")]
        public async Task<IActionResult> Delete([FromRoute] int id)
        {
            var commentModel = await _commentRepository.DeleteAsync(id);

            if (commentModel == null)
            {
                return NotFound("Comment does not exist");
            }

            return Ok(commentModel);
        }
    }
}

{% endhighlight %}

### DTOs
- create `Stock` folder in Dtos folder
- create `CommentDto.cs` in Comment folder

* StockDto.cs
{% highlight cs %}
namespace Api.Dtos.Comment
{
    public class CommentDto
    {
        public int Id { get; set; }
        public string Title { get; set; } = string.Empty;
        public string Content { get; set; } = string.Empty;
        public DateTime CreatedOn { get; set; } = DateTime.Now;
        public int? StockId { get; set; }
    }
}
{% endhighlight %}

- create `CreateCommentRequestDto.cs` in Comment folder

* CreateCommentRequestDto.cs
{% highlight cs %}
namespace Api.Dtos.Comment
{
    public class CreateCommentRequestDto
    {
        public string Title { get; set; } = string.Empty;
        public string Content { get; set; } = string.Empty;
    }
}

{% endhighlight %}

- create `UpdateCommentRequestDto.cs` in Comment folder

* UpdateCommentRequestDto.cs
{% highlight cs %}
namespace Api.Dtos.Comment
{
    public class UpdateCommentRequestDto
    {
        public string Title { get; set; } = string.Empty;
        public string Content { get; set; } = string.Empty;
    }
}
{% endhighlight %}

### Mapper
- create `CommentMapper.cs` in Mappers folder

* CommentMapper.cs
{% highlight cs %}
using Api.Dtos.Comment;
using Api.Models;

namespace Api.Mappers
{
    public static class CommentMapper
    {
        public static CommentDto ToCommentDto(this Comment commentModel)
        {
            return new CommentDto
            {
                Id = commentModel.Id,
                Title = commentModel.Title,
                Content = commentModel.Content,
                CreatedOn = commentModel.CreatedOn,
                StockId = commentModel.StockId
            };
        }

        public static Comment ToCommentFromCreate(this CreateCommentRequestDto commentDto, int stockId)
        {
            return new Comment
            {
                Title = commentDto.Title,
                Content = commentDto.Content,
                StockId = stockId
            };
        }

        public static Comment ToCommentFromUpdate(this UpdateCommentRequestDto commentDto)
        {
            return new Comment
            {
                Title = commentDto.Title,
                Content = commentDto.Content
            };
        }
    }
}
{% endhighlight %}

### Interfaces
- create `ICommentRepository.cs` in Interfaces folder

* ICommentRepository.cs
{% highlight cs %}
using Api.Models;

namespace Api.Interfaces
{
    public interface ICommentRepository
    {
        Task<List<Comment>> GetAllAsync();
        Task<Comment?> GetByIdAsync(int id);
        Task<Comment> CreateAsync(Comment commentModel);
        Task<Comment?> UpdateAsync(int id, Comment commentModel);
        Task<Comment?> DeleteAsync(int id);
    }
}

{% endhighlight %}

### Repository
- create `CommentRepository.cs` in Repository folder

* CommentRepository.cs
{% highlight cs %}
using Api.Data;
using Api.Interfaces;
using Api.Models;
using Microsoft.EntityFrameworkCore;

namespace Api.Repository
{
    public class CommentRepository : ICommentRepository
    {
        private readonly ApplicationDBContext _context;

        public CommentRepository(ApplicationDBContext context)
        {
            _context = context;
        }

        public async Task<List<Comment>> GetAllAsync()
        {
            return await _context.Comments.ToListAsync();
        }

        public async Task<Comment?> GetByIdAsync(int id)
        {
            return await _context.Comments.FirstOrDefaultAsync(x => x.Id == id);
        }

        public async Task<Comment> CreateAsync(Comment commentModel)
        {
            await _context.Comments.AddAsync(commentModel);
            await _context.SaveChangesAsync();

            return commentModel;
        }

        public async Task<Comment?> UpdateAsync(int id, Comment commentModel)
        {
            var existingComment = await _context.Comments.FirstOrDefaultAsync(x => x.Id == id);

            if (existingComment == null)
            {
                return null;
            }

            existingComment.Title = commentModel.Title;
            existingComment.Content = commentModel.Content;

            await _context.SaveChangesAsync();
            return existingComment;
        }

        public async Task<Comment?> DeleteAsync(int id)
        {
            var commentModel = await _context.Comments.FirstOrDefaultAsync(x => x.Id == id);

            if (commentModel == null)
            {
                return null;
            }

            _context.Comments.Remove(commentModel);
            await _context.SaveChangesAsync();

            return commentModel;
        }
    }
}
{% endhighlight %}

### Program

* Program.cs
{% highlight cs %}
...
builder.Services.AddScoped<IStockRepository, StockRepository>();
builder.Services.AddScoped<ICommentRepository, CommentRepository>();
...
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/44.jpg"><img src="/assets/img/posts/react_finshark/44.jpg"></a>
  <a href="/assets/img/posts/react_finshark/45.jpg"><img src="/assets/img/posts/react_finshark/45.jpg"></a>
  <a href="/assets/img/posts/react_finshark/46.jpg"><img src="/assets/img/posts/react_finshark/46.jpg"></a>
	<figcaption>Comment</figcaption>
</figure>

# Data Validation

## Controller Validation
- You can set route's type with `:{type}`
- If you use data annotation in Dtos, you can alert to user with error message by `ModelState`

* StockController.cs
{% highlight cs %}
...
[HttpGet]
public async Task<IActionResult> GetAll()
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    var stock = await _stockRepository.GetAllAsync();
    var stockDto = stock.Select(s => s.ToStockDto());

    return Ok(stock);
}

[HttpGet]
[Route("{id:int}")]
public async Task<IActionResult> GetById([FromRoute] int id)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    var stock = await _stockRepository.GetByIdAsync(id);

    if (stock == null)
    {
        return NotFound();
    }

    return Ok(stock.ToStockDto());
}

[HttpPost]
public async Task<IActionResult> Create([FromBody] CreateStockRequestDto stockDto)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    var stockModel = stockDto.ToStockFromCreateDto();
    await _stockRepository.CreateAsync(stockModel);

    return CreatedAtAction(nameof(GetById), new { id = stockModel.Id}, stockModel.ToStockDto());
}

[HttpPut]
[Route("{id:int}")]
public async Task<IActionResult> Update([FromRoute] int id, [FromBody] UpdateStockRequestDto updateDto)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    var stockModel = await _stockRepository.UpdateAsync(id, updateDto);

    if (stockModel == null)
    {
        return NotFound();
    }

    return Ok(stockModel.ToStockDto());
}

[HttpDelete]
[Route("{id:int}")]
public async Task<IActionResult> Delete([FromRoute] int id)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    var stockModel = await _stockRepository.DeleteAsync(id);

    if (stockModel == null)
    {
        return NotFound();
    }

    return NoContent();
}
{% endhighlight %}

* CommentController.cs
{% highlight cs %}
...
[HttpGet]
public async Task<IActionResult> GetAll()
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    var comments = await _commentRepository.GetAllAsync();
    var commentDto = comments.Select(s => s.ToCommentDto());

    return Ok(commentDto);
}

[HttpGet]
[Route("{id:int}")]
public async Task<IActionResult> GetById([FromRoute] int id)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    var comment = await _commentRepository.GetByIdAsync(id);

    if (comment == null)
    {
        return NotFound();
    }

    return Ok(comment.ToCommentDto());
}

[HttpPost]
[Route("{stockId:int}")]
public async Task<IActionResult> Create([FromRoute] int stockId, [FromBody] CreateCommentRequestDto commentDto)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    if (!await _stockRepository.StockExists(stockId))
    {
        return BadRequest("Stock does not exist");
    }

    var commentModel = commentDto.ToCommentFromCreate(stockId);
    await _commentRepository.CreateAsync(commentModel);

    return CreatedAtAction(nameof(GetById), new { id = commentModel.Id }, commentModel.ToCommentDto());
}

[HttpPut]
[Route("{id:int}")]
public async Task<IActionResult> Update([FromRoute] int id, [FromBody] UpdateCommentRequestDto updateDto)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    var comment = await _commentRepository.UpdateAsync(id, updateDto.ToCommentFromUpdate());

    if (comment == null)
    {
        return NotFound("Comment not found");
    }

    return Ok(comment.ToCommentDto());
}

[HttpDelete]
[Route("{id:int}")]
public async Task<IActionResult> Delete([FromRoute] int id)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    var commentModel = await _commentRepository.DeleteAsync(id);

    if (commentModel == null)
    {
        return NotFound("Comment does not exist");
    }

    return Ok(commentModel);
}
{% endhighlight %}

## Data Annotations

* CreateCommentRequestDto.cs
{% highlight cs %}
public class CreateCommentRequestDto
{
    [Required]
    [MinLength(5,ErrorMessage = "Title must be 5 chaaracters")]
    [MaxLength(280, ErrorMessage = "Title cannot be over 280 characters")]
    public string Title { get; set; } = string.Empty;
    [Required]
    [MinLength(5, ErrorMessage = "Content must be 5 chaaracters")]
    [MaxLength(280, ErrorMessage = "Content cannot be over 280 characters")]
    public string Content { get; set; } = string.Empty;
}
{% endhighlight %}

* UpdateCommentRequestDto.cs
{% highlight cs %}
public class UpdateCommentRequestDto
{
    [Required]
    [MinLength(5, ErrorMessage = "Title must be 5 chaaracters")]
    [MaxLength(280, ErrorMessage = "Title cannot be over 280 characters")]
    public string Title { get; set; } = string.Empty;
    [Required]
    [MinLength(5, ErrorMessage = "Content must be 5 chaaracters")]
    [MaxLength(280, ErrorMessage = "Content cannot be over 280 characters")]
    public string Content { get; set; } = string.Empty;
}
{% endhighlight %}

* CreateStockRequestDto.cs
{% highlight cs %}
public class CreateStockRequestDto
{
    [Required]
    [MaxLength(10, ErrorMessage = "Symbol cannot be over 10 characters")]
    public string Symbol { get; set; } = string.Empty;
    [Required]
    [MaxLength(10, ErrorMessage = "Company cannot be over 10 characters")]
    public string CompanyName { get; set; } = string.Empty;
    [Required]
    [Range(1, 1000000000)]
    public decimal Purchase { get; set; }
    [Required]
    [Range(0.001, 100)]
    public decimal LastDiv { get; set; }
    [Required]
    [MaxLength(10, ErrorMessage = "Industry cannot be over 10 characters")]
    public string Industry { get; set; } = string.Empty;
    [Required]
    [Range(1, 5000000000)]
    public long MarketCap { get; set; }
}
{% endhighlight %}

* UpdateStockRequestDto.cs
{% highlight cs %}
public class UpdateStockRequestDto
{
    [Required]
    [MaxLength(10, ErrorMessage = "Symbol cannot be over 10 characters")]
    public string Symbol { get; set; } = string.Empty;
    [Required]
    [MaxLength(10, ErrorMessage = "Company cannot be over 10 characters")]
    public string CompanyName { get; set; } = string.Empty;
    [Required]
    [Range(1, 1000000000)]
    public decimal Purchase { get; set; }
    [Required]
    [Range(0.001, 100)]
    public decimal LastDiv { get; set; }
    [Required]
    public string Industry { get; set; } = string.Empty;
    [Required]
    [Range(1, 5000000000)]
    public long MarketCap { get; set; }
}
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/react_finshark/26.jpg"><img src="/assets/img/posts/react_finshark/26.jpg"></a>
  <a href="/assets/img/posts/react_finshark/42.jpg"><img src="/assets/img/posts/react_finshark/42.jpg"></a>
  <a href="/assets/img/posts/react_finshark/43.jpg"><img src="/assets/img/posts/react_finshark/43.jpg"></a>
	<figcaption>Data Validation</figcaption>
</figure>

# Filtering

### Program
- Download `Newtonsoft.Json` in Nuget Package Manager
- Download `Microsoft.AspNetCore.Mvc.NewtonsoftJson` in Nuget Package Manager

* Program.cs
{% highlight cs %}
...
builder.Services.AddControllers()
    .AddNewtonsoftJson(options =>
    {
        options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore;
    });
...
{% endhighlight %}

### Program
- Download `Newtonsoft.Json` in Nuget Package Manager
- Download `Microsoft.AspNetCore.Mvc.NewtonsoftJson` in Nuget Package Manager

* Program.cs
{% highlight cs %}
...
builder.Services.AddControllers()
    .AddNewtonsoftJson(options =>
    {
        options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore;
    });
...
{% endhighlight %}

### Helpers
- create `Helpers` folder in Api folder
- create `QueryObject.cs` in Helpers folder

* QueryObject.cs
{% highlight cs %}
namespace Api.Helpers
{
    public class QueryObject
    {
        public string? Symbol { get; set; } = null;
        public string? CompanyName { get; set; } = null;
        public string? SortBy { get; set; } = null;
        public bool IsDecsending { get; set; } = false;
        public int PageNumber { get; set; } = 1;
        public int PageSize { get; set; } = 20;
    }
}
{% endhighlight %}

### Interfaces

* IStockRepository.cs
{% highlight cs %}
Task<List<Stock>> GetAllAsync(QueryObject query);
...
{% endhighlight %}

### Repository

* StockRepository.cs
{% highlight cs %}
public async Task<List<Stock>> GetAllAsync(QueryObject query)
{
    var stocks = _context.Stocks.Include(c => c.Comments).AsQueryable();

    if (!string.IsNullOrWhiteSpace(query.CompanyName))
    {
        stocks = stocks.Where(s => s.CompanyName.Contains(query.CompanyName));
    }

    if (!string.IsNullOrWhiteSpace(query.Symbol)) 
    {
        stocks = stocks.Where(s => s.Symbol.Contains(query.Symbol));
    }

    if (!string.IsNullOrWhiteSpace(query.SortBy))
    {
        if (query.SortBy.Equals("Symbol", StringComparison.OrdinalIgnoreCase))
        {
            stocks = query.IsDecsending ? stocks.OrderByDescending(s => s.Symbol) : stocks.OrderBy(s => s.Symbol);
        }
    }

    var skipNumber = (query.PageNumber - 1) * query.PageSize;

    return await stocks.Skip(skipNumber).Take(query.PageSize).ToListAsync();
}
...
{% endhighlight %}

### Controller

* StockController.cs
{% highlight cs %}
[HttpGet]
public async Task<IActionResult> GetAll([FromQuery] QueryObject query)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    var stock = await _stockRepository.GetAllAsync(query);
    var stockDto = stock.Select(s => s.ToStockDto());

    return Ok(stock);
}
...
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_finshark/47.jpg"><img src="/assets/img/posts/react_finshark/47.jpg"></a>
	<figcaption>Filtering</figcaption>
</figure>

# Identify

## IdentityDbContext
- is a specialized DbContext provided by the Entity Framework (EF) to handle authentication and authorization.
- includes several built-in DbSets (tables) that support the default identity functionality, like users, roles and claims.

## IdentityRole
- is the built-in role model from ASP.NET Core Identity, representing user roles in the application. 
- allows to define roles like "Admin", "User" and etc.

## Authentication
- in an ASP.NET Core application can use JWT (JSON Web Token) Bearer Authentication.
- `JwtBearerDefaults.AuthenticationScheme` uses the JWT Bearer token approach.

## TokenValidationParameters 
- ensures that only valid and authorized tokens are accepted.

## app.useAuthentication()
- is a middleware that enables the authentication system within the request pipeline.
- should be placed before `app.UseAuthorization()`

### Nuget Packages
- Download `Microsoft.AspNetCore.Authentication.JwtBearer` in Nuget Package Manager
- Download `Microsoft.AspNetCore.Identity.EntityFrameworkCore` in Nuget Package Manager
- Download `Microsoft.Extensions.Identity.Core` in Nuget Package Manager

### Data

* ApplicationDBContext.cs
{% highlight cs %}
public class ApplicationDBContext : IdentityDbContext<AppUser>
...
{% endhighlight %}

### Models
- create `AppUser.cs` in Models folder

* AppUser.cs
{% highlight cs %}
using Microsoft.AspNetCore.Identity;

namespace Api.Models
{
    public class AppUser : IdentityUser
    {

    }
}
{% endhighlight %}

### appsettigs

* appsettigs.json
{% highlight json %}
...
"JWT": {
  "Issuer": "http://localhost:5246",
  "Audience": "http://localhost:5246",
  "SigningKey":  "swordrish"
}
{% endhighlight %}

### Program

* Program.cs
{% highlight cs %}
...
builder.Services.AddIdentity<AppUser, IdentityRole>(options =>
{
    options.Password.RequireDigit = true;
    options.Password.RequireLowercase = true;
    options.Password.RequireUppercase = true;
    options.Password.RequireNonAlphanumeric = true;
    options.Password.RequiredLength = 12;
}).AddEntityFrameworkStores<ApplicationDBContext>();

builder.Services.AddAuthentication(options =>
{
    options.DefaultAuthenticateScheme =
    options.DefaultChallengeScheme =
    options.DefaultForbidScheme =
    options.DefaultScheme =
    options.DefaultSignInScheme =
    options.DefaultSignOutScheme = JwtBearerDefaults.AuthenticationScheme;
}).AddJwtBearer(options =>
{
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = true,
        ValidIssuer = builder.Configuration["JWT:Issuer"],
        ValidateAudience = true,
        ValidAudience = builder.Configuration["JWT:Audience"],
        ValidateIssuerSigningKey = true,
        IssuerSigningKey = new SymmetricSecurityKey(
            System.Text.Encoding.UTF8.GetBytes(builder.Configuration["JWT:SigningKey"]))
    };
});
...
app.UseAuthentication();
...
{% endhighlight %}

### Database
- type `dotnet ef migrations add Identity` in developer power shell
- type `dotnet ef database update` in developer power shell

<figure>
  <a href="/assets/img/posts/react_finshark/48.jpg"><img src="/assets/img/posts/react_finshark/48.jpg"></a>
	<figcaption>Identify</figcaption>
</figure>

# Registration

## UserManager
- provides a high-level API for managing user-related operations in applications that use ASP.NET Identity for authentication and authorization.

## OnModelCreating
- provides a place to customize the model created by Entity Framework Core before generateing the database.

### DTOs
- create `RegisterDto.cs` in Dtos folder

* RegisterDto.cs
{% highlight cs %}
using System.ComponentModel.DataAnnotations;

namespace Api.Dtos.Account
{
    public class RegisterDto
    {
        [Required]
        public string? UserName { get; set; }
        [Required]
        [EmailAddress]
        public string? Email { get; set; }
        [Required]
        public string? Password { get; set; }
    }
}
{% endhighlight %}

### Controller
- create `AccountController.cs` in Controllers folder

* AccountController.cs
{% highlight cs %}
using Api.Dtos.Account;
using Api.Models;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Mvc;

namespace Api.Controllers
{
    [Route("api/account")]
    [ApiController]
    public class AccountController : ControllerBase
    {
        private readonly UserManager<AppUser> _userManager;

        public AccountController(UserManager<AppUser> userManager)
        {
            _userManager = userManager;   
        }

        [HttpPost("register")]
        public async Task<IActionResult> Register([FromBody] RegisterDto registerDto)
        {
            try
            {
                if (!ModelState.IsValid) 
                { 
                    return BadRequest(ModelState);
                }

                var appUser = new AppUser
                {
                    UserName = registerDto.UserName,
                    Email = registerDto.Email
                };

                var createUser = await _userManager.CreateAsync(appUser, registerDto.Password);

                if (createUser.Succeeded) 
                {
                    var roleResult = await _userManager.AddToRoleAsync(appUser, "User");
                    if (roleResult.Succeeded)
                    {
                        return Ok("User created");
                    }
                    else
                    {
                        return StatusCode(500, roleResult.Errors);
                    }
                }
                else
                {
                    return StatusCode(500, createUser.Errors);
                }
            }
            catch (Exception e)
            {
                return StatusCode(500, e);
            }
        }
    }
}
{% endhighlight %}

### ApplicationDBContext

* ApplicationDBContext.cs
{% highlight cs %}
...
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);

    List<IdentityRole> roles = new List<IdentityRole>
    {
        new IdentityRole
        {
            Name = "Admin",
            NormalizedName = "ADMIN"
        },
        new IdentityRole
        {
            Name = "User",
            NormalizedName = "USER"
        }
    };

    builder.Entity<IdentityRole>().HasData(roles);
}
{% endhighlight %}

### Database
- type `dotnet ef migrations add SeedRole` in developer power shell
- type `dotnet ef database update` in developer power shell

<figure class="third">
  <a href="/assets/img/posts/react_finshark/49.jpg"><img src="/assets/img/posts/react_finshark/49.jpg"></a>
  <a href="/assets/img/posts/react_finshark/50.jpg"><img src="/assets/img/posts/react_finshark/50.jpg"></a>
  <a href="/assets/img/posts/react_finshark/51.jpg"><img src="/assets/img/posts/react_finshark/51.jpg"></a>
	<figcaption>Registration</figcaption>
</figure>

# Token

## JWT (Json Web Token)
- is used in applications to securely transmit information for stateless, secure and cross-platform authentication.
- allows applications to manage user sessions without relying on centralized server-side session.
- you can decode your JWT in <a href="https://jwt.io/">https://jwt.io/</a>

### DTOs
- create `NewUserDto.cs` in Dtos folder

* NewUserDto.cs
{% highlight cs %}
namespace Api.Dtos.Account
{
    public class NewUserDto
    {
        public string UserName { get; set; }
        public string Email { get; set; }
        public string Token { get; set; }
    }
}
{% endhighlight %}

### Interfaces
- create `ITokenService.cs` in Interfaces folder

* ITokenService.cs
{% highlight cs %}
using Api.Models;

namespace Api.Interfaces
{
    public interface ITokenService
    {
        string CreateToken(AppUser appUser);
    }
}
{% endhighlight %}

### Services
- create `Services` folder
- create `TokenService.cs` in Services folder

* TokenService.cs
{% highlight cs %}
using Api.Interfaces;
using Api.Models;
using Microsoft.IdentityModel.Tokens;
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;

namespace Api.Service
{
    public class TokenService : ITokenService
    {
        private readonly IConfiguration _configuration;
        private readonly SymmetricSecurityKey _symmetricSecurityKey;

        public TokenService(IConfiguration configuration)
        {
            _configuration = configuration;
            _symmetricSecurityKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_configuration["JWT:SigningKey"]));
        }

        public string CreateToken(AppUser appUser)
        {
            var claims = new List<Claim>
            {
                new Claim(JwtRegisteredClaimNames.Email, appUser.Email),
                new Claim(JwtRegisteredClaimNames.GivenName, appUser.UserName)
            };

            var signingCredentials = new SigningCredentials(_symmetricSecurityKey, SecurityAlgorithms.HmacSha512Signature);
            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Subject = new ClaimsIdentity(claims),
                Expires = DateTime.Now.AddDays(7),
                SigningCredentials = signingCredentials,
                Issuer = _configuration["JWT:Issuer"],
                Audience = _configuration["JWT:Audience"]
            };

            var jwtSecutiryTokenHandler = new JwtSecurityTokenHandler();

            var token = jwtSecutiryTokenHandler.CreateToken(securityTokenDescriptor);

            return jwtSecutiryTokenHandler.WriteToken(token);
        }
    }
}

{% endhighlight %}

### Controllers

* AccountController.cs
{% highlight cs %}
private readonly UserManager<AppUser> _userManager;
private readonly ITokenService _tokenService;

public AccountController(UserManager<AppUser> userManager, ITokenService tokenService)
{
    _userManager = userManager;   
    _tokenService = tokenService;
}
...
        if (roleResult.Succeeded)
        {
            return Ok(
                new NewUserDto
                {
                    UserName = appUser.UserName,
                    Email = appUser.Email,
                    Token = _tokenService.CreateToken(appUser)
                });
        }
{% endhighlight %}

### Program

* Program.cs
{% highlight cs %}
...
builder.Services.AddScoped<ITokenService, TokenService>();
...
{% endhighlight %}

### appsettigs

* appsettigs.json
{% highlight json %}
...
"SigningKey":  "iamgoodiamcooliambeautifuliamprettyiamwonderfuliamgeniousiamcleveriamthebest"
...
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/react_finshark/52.jpg"><img src="/assets/img/posts/react_finshark/52.jpg"></a>
  <a href="/assets/img/posts/react_finshark/53.jpg"><img src="/assets/img/posts/react_finshark/53.jpg"></a>
	<figcaption>Token</figcaption>
</figure>

# Login

### DTOs
- create `LoginDto.cs` in Dtos folder

* LoginDto.cs
{% highlight cs %}
using System.ComponentModel.DataAnnotations;

namespace Api.Dtos.Account
{
    public class LoginDto
    {
        [Required]
        public string UserName { get; set; }
        [Required]
        public string Password { get; set; }
    }
}
{% endhighlight %}

### Program

* Program.cs
{% highlight cs %}
...
builder.Services.AddSwaggerGen(option =>
{
    option.SwaggerDoc("v1", new OpenApiInfo { Title = "Demo API", Version = "v1" });
    option.AddSecurityDefinition("Bearer", new OpenApiSecurityScheme
    {
        In = ParameterLocation.Header,
        Description = "Please enter a valid token",
        Name = "Authorization",
        Type = SecuritySchemeType.Http,
        BearerFormat = "JWT",
        Scheme = "Bearer"
    });
    option.AddSecurityRequirement(new OpenApiSecurityRequirement
    {
        {
            new OpenApiSecurityScheme
            {
                Reference = new OpenApiReference
                {
                    Type=ReferenceType.SecurityScheme,
                    Id="Bearer"
                }
            },
            new string[]{}
        }
    });
});
...
{% endhighlight %}

### Controllers

* AccountController.cs
{% highlight cs %}
public class AccountController : ControllerBase
{
    private readonly UserManager<AppUser> _userManager;
    private readonly ITokenService _tokenService;
    private readonly SignInManager<AppUser> _signInManager;

    public AccountController(UserManager<AppUser> userManager, ITokenService tokenService, SignInManager<AppUser> signInManager)
    {
        _userManager = userManager;   
        _tokenService = tokenService;
        _signInManager = signInManager;
    }

    [HttpPost("login")]
    public async Task<IActionResult> Login(LoginDto loginDto)
    {
        if (!ModelState.IsValid)
        {
            return BadRequest(ModelState);
        }

        var user = await _userManager.Users.FirstOrDefaultAsync(u => u.UserName == loginDto.UserName.ToLower());

        if (user == null)
        {
            return Unauthorized("Invalid username!");
        }

        var result = await _signInManager.CheckPasswordSignInAsync(user, loginDto.Password, false);

        if (!result.Succeeded) 
        {
            return Unauthorized("Username not found/or password incorrect");
        }

        return Ok(
            new NewUserDto
            {
                UserName = user.UserName,
                Email = user.Email,
                Token = _tokenService.CreateToken(user)
            });
    }
...
{% endhighlight %}

* CommentController.cs
{% highlight cs %}
public class AccountController : ControllerBase
...
[HttpGet]
[Authorize]
public async Task<IActionResult> GetAll()
...
[HttpGet]
[Authorize]
[Route("{id:int}")]
public async Task<IActionResult> GetById([FromRoute] int id)
...
[HttpPost]
[Authorize]
[Route("{stockId:int}")]
public async Task<IActionResult> Create([FromRoute] int stockId, [FromBody] CreateCommentRequestDto commentDto)
...
[HttpPut]
[Authorize]
[Route("{id:int}")]
public async Task<IActionResult> Update([FromRoute] int id, [FromBody] UpdateCommentRequestDto updateDto)
...
[HttpDelete]
[Authorize]
[Route("{id:int}")]
public async Task<IActionResult> Delete([FromRoute] int id)
...
{% endhighlight %}

* StockController.cs
{% highlight cs %}
public class AccountController : ControllerBase
...
[HttpGet]
[Authorize]
public async Task<IActionResult> GetAll([FromQuery] QueryObject query)
...
[HttpGet]
[Authorize]
[Route("{id:int}")]
public async Task<IActionResult> GetById([FromRoute] int id)
...
[HttpPost]
[Authorize]
public async Task<IActionResult> Create([FromBody] CreateStockRequestDto stockDto)
...
[HttpPut]
[Authorize]
[Route("{id:int}")]
public async Task<IActionResult> Update([FromRoute] int id, [FromBody] UpdateStockRequestDto updateDto)
...
[HttpDelete]
[Authorize]
[Route("{id:int}")]
public async Task<IActionResult> Delete([FromRoute] int id)
...
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/react_finshark/54.jpg"><img src="/assets/img/posts/react_finshark/54.jpg"></a>
  <a href="/assets/img/posts/react_finshark/55.jpg"><img src="/assets/img/posts/react_finshark/55.jpg"></a>
  <a href="/assets/img/posts/react_finshark/56.jpg"><img src="/assets/img/posts/react_finshark/56.jpg"></a>
	<figcaption>Token</figcaption>
</figure>

# Portfolio

## Database Relationship
- means like n:n, 1:n, n:1 and 1:1.
- `HasOne` sets up 1:1 or 1:n relationships between entities.
- `WithMany` sets up n:1 or n:n relationships between entities.
- `HasKey` sets a primary key from a composite key in an entity
- `HasForeignKey` sets a foreign key to refer an entity

### Models
- create `Portfolio.cs` in Models folder

* Portfolio.cs
{% highlight cs %}
using System.ComponentModel.DataAnnotations.Schema;

namespace Api.Models
{
    [Table("Portfolios")]
    public class Portfolio
    {
        public string AppUserId { get; set; }
        public int StockId { get; set; }
        public AppUser AppUser { get; set; }
        public Stock Stock { get; set; }
    }
}
{% endhighlight %}

* AppUser.cs
{% highlight cs %}
public class AppUser : IdentityUser
{
    public List<Portfolio> Portfolios { get; set; } = new List<Portfolio>();
}
{% endhighlight %}

* Comment.cs
{% highlight cs %}
[Table("Comments")]
public class Comment
{
...
{% endhighlight %}

* Stock.cs
{% highlight cs %}
using System.ComponentModel.DataAnnotations.Schema;

namespace Api.Models
{
    [Table("Stocks")]
    public class Stock
    {
        public int Id { get; set; }
        public string Symbol { get; set; } = string.Empty;
        public string CompanyName { get; set; } = string.Empty;
        [Column(TypeName="decimal(18, 2)")]
        public decimal Purchase { get; set; }
        [Column(TypeName="decimal(18, 2)")]
        public decimal LastDiv { get; set; }
        public string Industry { get; set; } = string.Empty;
        public long MarketCap { get; set; }
        public List<Comment> Comments { get; set; } = new List<Comment>();
        public List<Portfolio> Portfolios { get; set; } = new List<Portfolio>();
    }
}
{% endhighlight %}

### Data

* ApplicationDBContext.cs
{% highlight cs %}
...
public DbSet<Portfolio> Portfolios { get; set; }

protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);

    builder.Entity<Portfolio>(x => x.HasKey(p => new { p.AppUserId, p.StockId }));

    builder.Entity<Portfolio>()
      .HasOne(p => p.AppUser)
      .WithMany(u => u.Portfolios)
      .HasForeignKey(p => p.AppUserId);

    builder.Entity<Portfolio>()
      .HasOne(p => p.Stock)
      .WithMany(s => s.Portfolios)
      .HasForeignKey(p => p.StockId);
...
{% endhighlight %}

### Extensions
- create `Extensions` folder in Api folder
- create `ClaimsExtensions.cs` in Extensions folder

* ClaimsExtensions.cs
{% highlight cs %}
using System.Security.Claims;

namespace Api.Extensions
{
    public static class ClaimsExtensions
    {
        public static string GetUsername(this ClaimsPrincipal user)
        {
            return user.Claims.SingleOrDefault(x => x.Type.Equals("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname")).Value;
        }
    }
}
{% endhighlight %}

### Interfaces
- create `IPortfolioRepository.cs` in Interfaces folder

* IPortfolioRepository.cs
{% highlight cs %}
using Api.Models;

namespace Api.Interfaces
{
    public interface IPortfolioRepository
    {
        Task<List<Stock>> GetUserPortfolio(AppUser appUser);
        Task<Portfolio> CreateAsync (Portfolio portfolio);
        Task<Portfolio> DeleteAsync (AppUser appUser, string symbol);
    }
}
{% endhighlight %}

* IStockRepository.cs
{% highlight cs %}
...
Task<Stock?>  GetBySymbolAsync(string symbol);
...
{% endhighlight %}

### Repository
- create `PortfolioRepository.cs` in Repository folder

* IPortfolioRepository.cs
{% highlight cs %}
using Api.Data;
using Api.Interfaces;
using Api.Models;
using Microsoft.EntityFrameworkCore;

namespace Api.Repository
{
    public class PortfolioRepository : IPortfolioRepository
    {
        private readonly ApplicationDBContext _context;

        public PortfolioRepository(ApplicationDBContext context)
        {
            _context = context;
        }

        public async Task<List<Stock>> GetUserPortfolio(AppUser appUser)
        {
            return await _context.Portfolios.Where(u => u.AppUserId == appUser.Id)
                .Select(stock => new Stock
                {
                    Id = stock.StockId,
                    Symbol = stock.Stock.Symbol,
                    CompanyName = stock.Stock.CompanyName,
                    Purchase = stock.Stock.Purchase,
                    LastDiv = stock.Stock.LastDiv,
                    MarketCap = stock.Stock.MarketCap
                }).ToListAsync();
        }

        public async Task<Portfolio> CreateAsync(Portfolio portfolio)
        {
            await _context.Portfolios.AddAsync(portfolio);
            await _context.SaveChangesAsync();
            return portfolio;
        }

        public async Task<Portfolio> DeleteAsync(AppUser appUser, string symbol)
        {
            var portfolioModel = await _context.Portfolios.FirstOrDefaultAsync(x => x.AppUserId == appUser.Id 
                && x.Stock.Symbol.ToLower() == symbol.ToLower());
            
            if (portfolioModel != null)
            {
                return null;
            }

            _context.Portfolios.Remove(portfolioModel);
            await _context.SaveChangesAsync();

            return portfolioModel;
        }
    }
}
{% endhighlight %}

* StockRepository.cs
{% highlight cs %}
...
 public async Task<Stock?> GetBySymbolAsync(string symbol)
 {
     return await _context.Stocks.FirstOrDefaultAsync(x => x.Symbol == symbol);
 }
...
{% endhighlight %}

### Program
* Program.cs
{% highlight cs %}
...
builder.Services.AddScoped<IPortfolioRepository, PortfolioRepository>();
...
{% endhighlight %}

### PortfolioController

* PortfolioController.cs
{% highlight cs %}
using Api.Extensions;
using Api.Interfaces;
using Api.Models;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Mvc;

namespace Api.Controllers
{
    [Route("api/portfolio")]
    [ApiController]
    public class PortfolioController : ControllerBase
    {
        private readonly UserManager<AppUser> _userManager;
        private readonly IStockRepository _stockRepository;
        private readonly IPortfolioRepository _portfolioRepository;

        public PortfolioController(UserManager<AppUser> userManager, IStockRepository stockRepository, IPortfolioRepository portfolioRepository)
        {
            _userManager = userManager;
            _stockRepository = stockRepository;
            _portfolioRepository = portfolioRepository;
        }

        [HttpGet]
        [Authorize]
        public async Task<IActionResult> GetUserPortfolio()
        {
            var username = User.GetUsername();
            var appUser = await _userManager.FindByNameAsync(username);
            var userPortfolio = await _portfolioRepository.GetUserPortfolio(appUser);

            return Ok(userPortfolio);
        }

        [HttpPost]
        [Authorize]
        public async Task<IActionResult> AddPortfolio(string symbol)
        {
            var username = User.GetUsername();
            var appUser = await _userManager.FindByNameAsync(username);
            var stock = await _stockRepository.GetBySymbolAsync(symbol);

            if (stock == null)
            {
                return BadRequest("Stock not found");
            }

            var userPortfolio = await _portfolioRepository.GetUserPortfolio(appUser);

            if (userPortfolio.Any(e => e.Symbol.ToLower() == symbol.ToLower()))
            {
                return BadRequest("Cannot add same stock to portfolio");
            }

            var portfolioModel = new Portfolio
            {
                StockId = stock.Id,
                AppUserId = appUser.Id
            };

            await _portfolioRepository.CreateAsync(portfolioModel);

            if (portfolioModel == null) 
            {
                return StatusCode(500, "Cloud not create");
            }
            else
            {
                return Created();
            }
        }

        [HttpDelete]
        [Authorize]
        public async Task<IActionResult> DeletePortfolio(string symbol)
        {
            var username = User.GetUsername();
            var appUser = await _userManager.FindByNameAsync(username);
            var userPortfolio = await _portfolioRepository.GetUserPortfolio(appUser);
            
            var filteredStock = userPortfolio.Where(u => u.Symbol.ToLower() == symbol.ToLower()).ToList();

            if (filteredStock.Count() == 1) 
            {
                await _portfolioRepository.DeleteAsync(appUser, symbol);
            }
            else
            {
                return BadRequest("Stock not in your portfolio");
            }

            return Ok();
        }
    }
}
{% endhighlight %}

### Database
- type `dotnet ef migrations add ManyToMany`
- type `dotnet ef database update`

<figure class="third">
  <a href="/assets/img/posts/react_finshark/57.jpg"><img src="/assets/img/posts/react_finshark/57.jpg"></a>
  <a href="/assets/img/posts/react_finshark/58.jpg"><img src="/assets/img/posts/react_finshark/58.jpg"></a>
  <a href="/assets/img/posts/react_finshark/59.jpg"><img src="/assets/img/posts/react_finshark/59.jpg"></a>
	<figcaption>Portfolio</figcaption>
</figure>

# Comment

### DTOs

* CommentDto.cs
{% highlight cs %}
...
public string CreatedBy { get; set; } = string.Empty;
...
{% endhighlight %}

### Models

* Comment.cs
{% highlight cs %}
...
public string? AppUserId { get; set; }
public AppUser AppUser { get; set; }
...
{% endhighlight %}

### Mappers

* CommentMapper.cs
{% highlight cs %}
...
public static CommentDto ToCommentDto(this Comment commentModel)
{
    return new CommentDto
    {
        Id = commentModel.Id,
        Title = commentModel.Title,
        Content = commentModel.Content,
        CreatedOn = commentModel.CreatedOn,
        CreatedBy = commentModel.AppUser.UserName,
        StockId = commentModel.StockId
    };
}
...
{% endhighlight %}

### Repository

* CommentRepository.cs
{% highlight cs %}
...
public async Task<List<Comment>> GetAllAsync()
{
    return await _context.Comments.Include(c => c.AppUser).ToListAsync();
}

public async Task<Comment?> GetByIdAsync(int id)
{
    return await _context.Comments.Include(c => c.AppUser).FirstOrDefaultAsync(x => x.Id == id);
}
...
{% endhighlight %}

* StockRepository.cs
{% highlight cs %}
...
public async Task<List<Stock>> GetAllAsync(QueryObject query)
{
    var stocks = _context.Stocks.Include(c => c.Comments).ThenInclude(a => a.AppUser).AsQueryable();
...
{% endhighlight %}

### Controllers

* CommentController.cs
{% highlight cs %}
...
private readonly UserManager<AppUser> _userManager;

public CommentController(ICommentRepository commentRepository, IStockRepository stockRepository, UserManager<AppUser> userManager)
{
    _commentRepository = commentRepository;
    _stockRepository = stockRepository;
    _userManager = userManager;
}

...
[HttpPost]
[Authorize]
[Route("{stockId:int}")]
public async Task<IActionResult> Create([FromRoute] int stockId, [FromBody] CreateCommentRequestDto commentDto)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    if (!await _stockRepository.StockExists(stockId))
    {
        return BadRequest("Stock does not exist");
    }

    var username = User.GetUsername();
    var appUser = await _userManager.FindByNameAsync(username);
    var commentModel = commentDto.ToCommentFromCreate(stockId);
    commentModel.AppUserId = appUser.Id;

    await _commentRepository.CreateAsync(commentModel);

    return CreatedAtAction(nameof(GetById), new { id = commentModel.Id }, commentModel.ToCommentDto());
}
...
{% endhighlight %}

* StockController.cs
{% highlight cs %}
...
[HttpGet]
[Authorize]
public async Task<IActionResult> GetAll([FromQuery] QueryObject query)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    var stock = await _stockRepository.GetAllAsync(query);
    var stockDto = stock.Select(s => s.ToStockDto()).ToList();

    return Ok(stockDto);
}
...
{% endhighlight %}

### Database
- type `dotnet ef migrations CommentOneToOne`
- tpye `dotnet ef database update`

<figure>
  <a href="/assets/img/posts/react_finshark/60.jpg"><img src="/assets/img/posts/react_finshark/60.jpg"></a>
	<figcaption>Comment</figcaption>
</figure>

# HttpClient

### DTOs

* FinancialModelingPrepStock.cs
{% highlight cs %}
namespace api.Dtos.Stock
{
    public class FinancialModelingPrepStock
    {
        public string symbol { get; set; }
        public double price { get; set; }
        public double beta { get; set; }
        public int volAvg { get; set; }
        public long mktCap { get; set; }
        public double lastDiv { get; set; }
        public string range { get; set; }
        public double changes { get; set; }
        public string companyName { get; set; }
        public string currency { get; set; }
        public string cik { get; set; }
        public string isin { get; set; }
        public string cusip { get; set; }
        public string exchange { get; set; }
        public string exchangeShortName { get; set; }
        public string industry { get; set; }
        public string website { get; set; }
        public string description { get; set; }
        public string ceo { get; set; }
        public string sector { get; set; }
        public string country { get; set; }
        public string fullTimeEmployees { get; set; }
        public string phone { get; set; }
        public string address { get; set; }
        public string city { get; set; }
        public string state { get; set; }
        public string zip { get; set; }
        public double dcfDiff { get; set; }
        public double dcf { get; set; }
        public string image { get; set; }
        public string ipoDate { get; set; }
        public bool defaultImage { get; set; }
        public bool isEtf { get; set; }
        public bool isActivelyTrading { get; set; }
        public bool isAdr { get; set; }
        public bool isFund { get; set; }
    }
}
{% endhighlight %}

### Interfaces

* IFinancialModelingPrepService.cs
{% highlight cs %}
using Api.Models;

namespace Api.Interfaces
{
    public interface IFinancialModelingPrepService
    {
        Task<Stock?> FindStockBySymbolAsync(string symbol);
    }
}
{% endhighlight %}

### Services

* FinancialModelingPrepService.cs
{% highlight cs %}
using api.Dtos.Stock;
using Api.Interfaces;
using Api.Mappers;
using Api.Models;
using Newtonsoft.Json;

namespace Api.Service
{
    public class FinancialModelingPrepService : IFinancialModelingPrepService
    {
        private HttpClient _httpClient;
        private IConfiguration _configuration;

        public FinancialModelingPrepService(HttpClient httpClient, IConfiguration configuration)
        {
            _httpClient = httpClient;
            _configuration = configuration;
        }

        public async Task<Stock?> FindStockBySymbolAsync(string symbol)
        {
            try
            {
                var result = await _httpClient.GetAsync($"https://financialmodelingprep.com/api/v3/profile/{symbol}?apikey={_configuration["FinancialModelingPrepKey"]}");
                if (result.IsSuccessStatusCode)
                {
                    var content = await result.Content.ReadAsStringAsync();
                    var tasks = JsonConvert.DeserializeObject<FinancialModelingPrepStock[]>(content);
                    var stock = tasks[0];

                    if (stock != null) 
                    {
                        return stock.ToStockFromFinancialModelingPrep();
                    }
                }

                return null;
            }
            catch (Exception e) 
            {
                Console.WriteLine(e);
                return null;
            }
        }
    }
}
{% endhighlight %}

### appsettigs

* appsettigs.json
{% highlight json %}
...
"FinancialModelingPrepKey": "{YOUR_FINANCIAL_MODELING_PREP_APIKEY}",
...
{% endhighlight %}

### Mappers

* StockMapper.cs
{% highlight cs %}
...
public static Stock ToStockFromFinancialModelingPrep(this FinancialModelingPrepStock financialModelingPrepStock)
{
    return new Stock
    {
        Symbol = financialModelingPrepStock.symbol,
        CompanyName = financialModelingPrepStock.companyName,
        Purchase = (decimal)financialModelingPrepStock.price,
        LastDiv = (decimal)financialModelingPrepStock.lastDiv,
        Industry = financialModelingPrepStock.industry,
        MarketCap = financialModelingPrepStock.mktCap
    };
}
{% endhighlight %}

### Program

* Program.cs
{% highlight cs %}
...
builder.Services.AddScoped<IFinancialModelingPrepService, FinancialModelingPrepService>();
builder.Services.AddHttpClient<IFinancialModelingPrepService, FinancialModelingPrepService>();
...
{% endhighlight %}

### Controllers

* PortfolioController.cs
{% highlight cs %}
...
private readonly UserManager<AppUser> _userManager;
private readonly IStockRepository _stockRepository;
private readonly IPortfolioRepository _portfolioRepository;
private readonly IFinancialModelingPrepService _financialModelingPrepService;

public PortfolioController(UserManager<AppUser> userManager, IStockRepository stockRepository, IPortfolioRepository portfolioRepository, IFinancialModelingPrepService financialModelingPrepService)
{
    _userManager = userManager;
    _stockRepository = stockRepository;
    _portfolioRepository = portfolioRepository;
    _financialModelingPrepService = financialModelingPrepService;
}
...
public async Task<IActionResult> AddPortfolio(string symbol)
{
    var username = User.GetUsername();
    var appUser = await _userManager.FindByNameAsync(username);
    var stock = await _stockRepository.GetBySymbolAsync(symbol);

    if (stock == null)
    {
        stock = await _financialModelingPrepService.FindStockBySymbolAsync(symbol);

        if (stock == null)
        {
            return BadRequest("Stock not found");
        }
        else
        {
            await _stockRepository.CreateAsync(stock);
        }
    }

    var userPortfolio = await _portfolioRepository.GetUserPortfolio(appUser);

    if (userPortfolio.Any(e => e.Symbol.ToLower() == symbol.ToLower()))
    {
        return BadRequest("Cannot add same stock to portfolio");
    }

    var portfolioModel = new Portfolio
    {
        StockId = stock.Id,
        AppUserId = appUser.Id
    };

    await _portfolioRepository.CreateAsync(portfolioModel);

    if (portfolioModel == null) 
    {
        return StatusCode(500, "Cloud not create");
    }
    else
    {
        return Created();
    }
}
...
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/react_finshark/61.jpg"><img src="/assets/img/posts/react_finshark/61.jpg"></a>
  <a href="/assets/img/posts/react_finshark/62.jpg"><img src="/assets/img/posts/react_finshark/62.jpg"></a>
  <a href="/assets/img/posts/react_finshark/63.jpg"><img src="/assets/img/posts/react_finshark/63.jpg"></a>
	<figcaption>HttpClient</figcaption>
</figure>

* CommentController.cs
{% highlight cs %}
...
private readonly UserManager<AppUser> _userManager;
private readonly ICommentRepository _commentRepository;
private readonly IStockRepository _stockRepository;
private readonly IFinancialModelingPrepService _financialModelingPrepService;

public CommentController(UserManager<AppUser> userManager, ICommentRepository commentRepository, IStockRepository stockRepository, IFinancialModelingPrepService financialModelingPrepService)
{
    _userManager = userManager;
    _commentRepository = commentRepository;
    _stockRepository = stockRepository;
    _financialModelingPrepService = financialModelingPrepService;
}
...
[HttpPost]
[Authorize]
[Route("{symbol:alpha}")]
public async Task<IActionResult> Create([FromRoute] string symbol, [FromBody] CreateCommentRequestDto commentDto)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }
    
    var stock = await _stockRepository.GetBySymbolAsync(symbol);

    if (stock == null)
    {
        stock = await _financialModelingPrepService.FindStockBySymbolAsync(symbol);

        if (stock == null)
        {
            return BadRequest("Stock does not exists");
        }
        else
        {
            await _stockRepository.CreateAsync(stock);
        }
    }

    var username = User.GetUsername();
    var appUser = await _userManager.FindByNameAsync(username);
    var commentModel = commentDto.ToCommentFromCreate(stock.Id);
    commentModel.AppUserId = appUser.Id;

    await _commentRepository.CreateAsync(commentModel);

    return CreatedAtAction(nameof(GetById), new { id = commentModel.Id }, commentModel.ToCommentDto());
}
...
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/react_finshark/64.jpg"><img src="/assets/img/posts/react_finshark/64.jpg"></a>
  <a href="/assets/img/posts/react_finshark/66.jpg"><img src="/assets/img/posts/react_finshark/66.jpg"></a>
  <a href="/assets/img/posts/react_finshark/65.jpg"><img src="/assets/img/posts/react_finshark/65.jpg"></a>
	<figcaption>HttpClient</figcaption>
</figure>
