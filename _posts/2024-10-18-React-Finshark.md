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
      `https://financialmodelingprep.com/api/v3/search?query=${query}&limit=10&exchange=NASDAQ&apikey=${process.env.REACT_APP_API_KEY}`
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

<!-- TODO: Result image -->

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

### Developer Power Shell
- open `Developer Power Shell` in visual studio
- type `doenet ef migrations add init`
- type `doenet ef database update`

<figure class="third">
  <a href="/assets/img/posts/react_finshark/22.jpg"><img src="/assets/img/posts/react_finshark/22.jpg"></a>
  <a href="/assets/img/posts/react_finshark/23.jpg"><img src="/assets/img/posts/react_finshark/23.jpg"></a>
  <a href="/assets/img/posts/react_finshark/24.jpg"><img src="/assets/img/posts/react_finshark/24.jpg"></a>
	<figcaption>Entity Framework</figcaption>
</figure>

# Controllers

### StockController
- create `StockController.cs` in Controllers folder

* StockController.cs
{% highlight cs %}
using Api.Data;
using Microsoft.AspNetCore.Mvc;

namespace Api.Controllers
{
    [Route("api/stock")]
    [ApiController]
    public class StockController : ControllerBase
    {
        private readonly ApplicationDBContext _context;

        public StockController(ApplicationDBContext context)
        {
            _context = context;    
        }

        [HttpGet]
        public IActionResult Get()
        {
            var stock = _context.Stocks.ToList();
            return Ok(stock);
        }

        [HttpGet("{id}")]
        public IActionResult GetById([FromRoute] string id)
        {
            var stock = _context.Stocks.Find(id);

            if (stock == null)
            {
                return NotFound();
            }

            return Ok(stock);
        }
    }
}
{% endhighlight %}

### SQL Server Management Studio
- right click `dbo.Stocks` table in finshark database
- click `Edit Top 200 Rows`
- insert some values

<figure>
  <a href="/assets/img/posts/react_finshark/25.jpg"><img src="/assets/img/posts/react_finshark/25.jpg"></a>
	<figcaption>Controllers</figcaption>
</figure>

### Run

<figure>
  <a href="/assets/img/posts/react_finshark/26.jpg"><img src="/assets/img/posts/react_finshark/26.jpg"></a>
	<figcaption>Controllers</figcaption>
</figure>

# DTOs
