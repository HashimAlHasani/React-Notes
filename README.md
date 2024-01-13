# #########################################################################################
# Part.58 - GraphQL API and Apollo Intro

GraphQL is an alternative to a REST API.
- In the REST API we will make individual requests with different methods and get the appropriate data back.
- With GraphQl we have a single endpoint and we can customize what we want on the frontend.
- GraphQL is query language for your API.
- We can ask for what we want but we are going to do it from the client instead of structuring those query langauges in the backend to the database.
- GraphQL can work very easily with relationships in our data; it will create those relationships in a graph like structure and we can grab the initial data and then grab the nested data which would be connected to the original data.
  - For example, not only we would be able to grab the customers in a single request, we can also grab all of the customers orders in a single request, and get that in a single structured back. (What attributes we want can be customizable)
- The end result is that we are going to have one API endpoint which is typically `/GraphQL` and then we just modify the request that we are sending to that endpoint to get different data.
- We can find GraphQL in a lot of different locations (front-end and back-end), also bunch of different languages support it, and bunch of different clients in each language (We want JavaScript and Apollo)
- Notice we have 2 sections `Client` code and a `Server` code.
- This is a connection where we have to setup GraphQl in a server environment if you were managing your own API, and you'll have GraphQL on the client to work with that backend.
- `graphql.org/code/#javascript`

- Starting out we are just going to worry about the front-end and consume an already existing API thats out there.
- We are going to use the `Apollo client` which can be explained in `https://www.apollographql.com/docs/react/`
- We are going to use an existing GraphQL endpoint: `https://spacex-production.up.railway.app/`
  - In the api website we will see a tool called `GraphiQL`, this will basically make the query for us, we can edit the query as we like, and when we run it will give us a single JSON response (no need for many API requests, we can just make one), we can use the `GraphiQL` in this website `https://studio.apollographql.com/public/SpaceX-pxxbxen/variant/current/explorer`

- We are going to create a react application to consume this GraphQL endpoint, type in the terminal:
```
npx create-react-app graphql --template typescript
```
- Then open our app by typing in the terminal: `code graphql`
- We are going to remove the files that we will not need in our `src` folder and our final `src` project directory should look like this:
```
>node_modules
>public
^src
    App.css
    App.tsx
    index.css
    index.tsx
    react-app-env.d.ts
.gitignore
package-lock.json
package.json
README.md
tsconfig.json
```
- In `index.tsx` we removed anything related to `webvitals`
- In `App.tsx` we removed unused stuff and removed everything inside the `<div className="APP>...</div>`
- We removed all of the css in `App.css` and `index.css`

- Now we can our packages that we need by typing into the terminal:
```
npm install @apollo/client graphql
```
- We are going to write some code in `index.tsx` that is going to surround our entire app. Add these imports in `index.tsx`:
```
import { ApolloClient, InMemoryCache, ApolloProvider, gql, } from "@apollo/client";
```
- Now we need to create an instance of a client in `index.tsx` after the imports:
```
let client = new ApolloClient({
  uri: "",
  cache: new InMemoryCache(),
});
```
- The uri is going to come from the endpoint we've been using earlier which is `https://spacex-production.up.railway.app/`

- We are going to make a query in `index.tsx` after our instance of a client, and to do so we can do:
```
client
  .query({
    query: gql`
      {
        launchesPast(limit: 10) {
          mission_name
          launch_date_local
          launch_site {
            site_name_long
          }
        }
      }
    `,
  })
  .then((result) => {
    console.log(result);
  });
```
- You can see the query we are making is prefixed with `gql` and then inside back ticks ` `...` `

# #########################################################################################
# Part.57 - Pie Chart with Chart.js (react-chartjs-2)

What we want to achieve now is to display a pie chart of our crypto currencies and fix the user-interface.

- The Pie Chart we are going to get the code from `react-chartjs-2.js.org/examples/pie-chart/`.
- We want the input default value to be empty (instead of having 0 and then deleting 0 to type your number), so in `CryptoSummary.tsx` we changed `0` to `NaN` in the `amount` state variable default value.
```
const [amount, setAmount] = useState<number>(NaN);
```
- In `react-chartjs-2.js.org/examples/pie-chart/` we are going to get the code in the website's `App.tsx`:
  - We are going to get the `data = {}` object.

- We deleted the `options` state in our `App.tsx` code, and we uncommented the `data` variable state.
- We changed the import from `line` to `Pie`, in `App.tsx`
- We changed the `ChartJs.register()` in `App.tsx` to: `ChartJS.register(ArcElement, Tooltip, Legend);`
- We changed the `<ChartData<>>` type in the data variable state (The nested `<>`) to `<ChartData<pie>>`
- We uncommented where we rendered the line chart at the end of `App.tsx` in the data ternary operator and changed it to:
```
{data ? (
  <div style={{ width: 600 }}>
    <Pie data={data} />
  </div>
) : null}
```
- Currently we are not setting anything to our data state variable, we want to set the data of the selected crypto currency and the best way to do this is to create a `useEffect()` that depends on the `selected` state:
```
useEffect(() => {
  if (selected.length === 0) return;
  setData({
    labels: selected.map((s) => s.name),
    datasets: [
      {
        label: "# of Votes",
        data: selected.map((s) => s.owned * s.current_price),
        backgroundColor: [
          "rgba(255, 99, 132, 0.2)",
          "rgba(54, 162, 235, 0.2)",
          "rgba(255, 206, 86, 0.2)",
          "rgba(75, 192, 192, 0.2)",
          "rgba(153, 102, 255, 0.2)",
          "rgba(255, 159, 64, 0.2)",
        ],
        borderColor: [
          "rgba(255, 99, 132, 1)",
          "rgba(54, 162, 235, 1)",
          "rgba(255, 206, 86, 1)",
          "rgba(75, 192, 192, 1)",
          "rgba(153, 102, 255, 1)",
          "rgba(255, 159, 64, 1)",
        ],
        borderWidth: 1,
      },
    ],
  });
}, [selected]);
```
- We don't the pie chart to be on our website without having any crypto currencies selected so we add the if statement at the top of the `useEffect()` to check if the length of the selected array is 0, we would return without passing/setting any data.
- We then set the `labels` to equal to the name of the selected cryptocurrency by mapping over the selected array, and we also did the same for the `data`, we mapped over the selected array, and multiplied the amount of coins owned to the current price of the coin.

# #########################################################################################
# Part.56 - Aggregate Data with map and reduce

What we want to do is when we for example choose the cryptocurrency Ethereum and input `500`, this will give a total of `$x`, after that we choose another coin for example Bitcoin and input `100`, this will give a total of `$y`, our main goal is to find the total of `$x` and `$y` and display it, can look simple but it is not because each coin is a component so we need to figure out how to move their state to their parent.

- What we are going to do first in `CryptoSummary.tsx` is we are going to store the values as in `number` not `string`, so we changed the `amount` state type:
```
const [amount, setAmount] = useState<number>(0);
```
- We will now get some errors, to fix them we need to remove each of the `.parseFloat(amount)` and change it to just `amount`, then we are going to change the way we are doing `setAmount` to be: `setAmount(parseFloat(e.target.value))`

- This `amount` is tied to a single component so how do we actual push that up to the parent?
  - We'll want to pass down a function from the parent which is `App.tsx`
  - Then take this in as a prop inside of the `CryptoSummary.tsx` component
  - This will allow us to invoke that function whenever we want to make a change
  - Inshort we want to set the parents state by calling a function passed in as a prop

- First we need to define the function inside the `export type AppProps ={...}` in `CryptoSummary.tsx` that will take two parameters (to pass data to it):
```
export type AppProps = {
  crypto: Crypto;
  updateOwned: (crypto: Crypto, amount: number) => void;
};
```
- We will retrieve this value using destructuring so we can do:
```
export default function CryptoSummary({crypto, updateOwned,}: AppProps): JSX.Element {...}
```
- We will call this function inside the `onChange()` in the `CryptoSummary.tsx` component:
```
updateOwned(crypto, parseFloat(e.target.value));
```
- We will then create the function `updateOwned()` in `App.tsx` right before the return:
```
function updateOwned(crypto: Crypto, amount: number): void {}
```
- Then we can pass to the child component in `App.tsx` where we map the selected cryptocurrencies:
```
return <CryptoSummary crypto={s} updateOwned={updateOwned}
```
- To keep track of the owned amount and aggregate all of the owned amounts across all of the different cryptocurrencies to get the total portfolio value, We are going to add a property to the cryptocurrency object to keep track how much is owned.

- At first we need to add the property to `Crypto` type in `Types.tsx`: `owned: number;`
- We added the logic to `updateOwned()` function in `App.tsx`:
```
function updateOwned(crypto: Crypto, amount: number): void {
  let temp = [...selected];
  let tempObj = temp.find((c) => c.id === crypto.id);
  if (tempObj) {
    tempObj.owned = amount;
    setSelected(temp);
  }
}
```
- What will the function `updateOwned()` do is:
  - save the selected state variable to a temporary array: `let temp = [...selected];`
  - then we create a temporary object which will get the crypto: `let tempObj = temp.find((c) => c.id === crypto.id);`
  - then we need to check if temporary object exists.
  - if it does we will set the owned property of the temporary object to the value of amount: `tempObj.owned = amount;`
  - then we set the new temporary selected array `temp`: `setSelected(temp);`

- Now, we just have to go to the bottom of our html page, and aggregate the data and display a total value.
- Right before the closing tag `</>` in the return in `App.tsx`:
```
{selected
    ? selected
        .map((s) => {
          return s.current_price * s.owned;
        })
        .reduce((prev, current) => {
          return prev + current;
        }, 0)
    : null}
```
- What we are doing above is as follows:
  - we are checking if selected is there (selected ternary operator: `selected ? ... : null`)
  - we are then calling `.map()` on the selected state, and returning the total of each selected state (cryptocurrency)
  - then we are calling the method `.reduce()`, which is going to keep track of the prev state and the current state (parameters), and we are going to return the total of previous + current, and it takes another argument which is the start index, which is `0`.

- What we want to deal with now is `NaN` (not a number),
  - In `CryptoSummary.tsx`, we added a ternary operator to check if there is a `Nan`:
  ```
  {isNaN(amount)
    ? "$0.00"
    : "$" +
      (crypto.current_price * amount).toLocaleString(undefined, {
        minimumFractionDigits: 2,
        maximumFractionDigits: 2,
      })}
  ```
  - In `App.tsx` we are going to check is `s.owned` is `NaN` if yes return 0:
  ```
  {selected
    ? "Your portfolio is worth: $" +
      selected
        .map((s) => {
          if (isNaN(s.owned)) {
            return 0;
          }

          return s.current_price * s.owned;
        })
        .reduce((prev, current) => {
          return prev + current;
        }, 0)
        .toLocaleString(undefined, {
          minimumFractionDigits: 2,
          maximumFractionDigits: 2,
        })
    : null}
  ```

# #########################################################################################
# Part.55 - Calculate Crypto Values

We want to compare between 2 Cryptocurrencies, so after when we select a coin we will be able to also select a different coin and add to a list.

- To achieve this behaviour we would need to keep track of selected as an array, so lets change the state variable to:
```
const [selected, setSelected] = useState<Crypto[] | null>();
```
- What we are currently trying to do is when we choose 1 coin, and then choose another it will added to the first coin we chose as a list so for example `[...selected, newCoin]` and to make our `App.tsx` return like this:
```
return (
  <>
    <div className="App">
      <select
        onChange={(e) => {
          const c = cryptos?.find((x) => x.id === e.target.value) as Crypto;
          setSelected([...selected, c]);
        }}
        defaultValue={"default"}
      >
        <option value="default" disabled>
          Choose a Crypto Currency
        </option>
        {cryptos
          ? cryptos.map((crypto) => {
              return (
                <option key={crypto.id} value={crypto.id}>
                  {crypto.name}
                </option>
              );
            })
          : null}
      </select>
    </div>

    {selected.map((s) => {
      return <CryptoSummary crypto={s} />;
    })}

    {/*selected ? <CryptoSummary crypto={selected} /> : null*/}
    {/*data ? (
      <div style={{ width: 600 }}>
        <Line options={options} data={data} />
      </div>
    ) : null*/}
  </>
);
```
- We commented out the section where it actually draws the chart (we don't need it right now)
- We also changed the `selected` ternary operator so that it will map through the list and return each crypto currency as `<CryptoSummary/>` element.
- We can also see that `setSelected([...selected, c])` this will save whatever was in the list and add `c` to it.

- Notes on the code so far:
  - we commented out the `data` and `options` state.
  - we commented out the `useEffect()` that would actually get the data for the line chart.
  - we commented our `selected` ternary at the bottom.
  - we commented out the `data` ternary at the bottom too.

- We also changed the `CryptoSummary.tsx` file so that it would get an input from us for the number of coints we want to calculate their total price:
```
export default function CryptoSummary({ crypto }: AppProps): JSX.Element {
  useEffect(() => {
    console.log(crypto.name, amount, crypto.current_price * parseFloat(amount));
  });

  const [amount, setAmount] = useState<string>("0");

  return (
    <div>
      <span>{crypto.name + " $" + crypto.current_price}</span>
      <input
        type="number"
        style={{ margin: 10 }}
        value={amount}
        onChange={(e) => {
          setAmount(e.target.value);
        }}
      ></input>
      <p>
        $
        {(crypto.current_price * parseFloat(amount)).toLocaleString(undefined, {
          minimumFractionDigits: 2,
          maximumFractionDigits: 2,
        })}
      </p>
    </div>
  );
}
```
- As you can see we made a variable state called amount and set it equal to what is inputted, then at the bottom we can do our calculations so that it will show the amount of coins we inputted multiplied by the coin current price.

# #########################################################################################
# Part.54 - Dynamic Chart with Multiple Drop Downs (Chart.js)

We will create another drop down list that will have the options: 30 days - 7 days - 1 day, based on the chosen option the timestamp of the chart shall change.

- We are going to create a new `<select>...</select>` after our first select.
```
<select>
  <option>30 Days</option>
  <option>7 Days</option>
  <option>1 Day</option>
</select>
```
- We added `onChange()` even handler to our second drop down:
```
onChange={() => {
  axios.get(
    `https://api.coingecko.com/api/v3/coins/${c?.id}/market_chart?vs_currency=usd&days=30&interval=daily`
  );
}}
```
- We will have a problem in here since `${c?.id}` is not passed down and vice versa in the first drop down the `days=30` is not passed down, so to fix this we need to create 2 state variables one for id (selected state) and one for range:
```
const [selected, setSelected] = useState<Crypto | null>();
const [range, setRange] = useState<string>("30");
```
- What we need to do now is to find a way to pass the crypto name and the crypto range, so the best way is to use an `useEffect()` with an dependency array that would include a state for the name and a state for the range:
```
useEffect(() => {
  if (selected) {
    axios
      .get(
        `https://api.coingecko.com/api/v3/coins/${selected?.id}/market_chart?vs_currency=usd&days=${range}&interval=daily`
      )
      .then((response) => {
        setData({
          labels: response.data.prices.map((price: number[]) => {
            return moment.unix(price[0] / 1000).format("MM-DD-YYYY");
          }),
          datasets: [
            {
              label: selected?.id.toUpperCase(),
              data: response.data.prices.map((price: number[]) => {
                return price[1];
              }),
              borderColor: "rgb(255, 99, 132)",
              backgroundColor: "rgba(255, 99, 132, 0.5)",
            },
          ],
        });
      });
  }
}, [selected, range]);
```
- As you can see above we moved the axios code we have written in the first drop down in `onChange()` to the `useEffect()`
we just created.
- You can also see above that we have included the state of both the `selected` and `range` variables.
- You can also see in the dependency array we have `[selected, range]`, and this will make the `useEffect()` re-render every time there will be a change to the `selected` and `range` states.
- The `if (selected) {...}`, is used because we don't want the page to render with a default `range`, but not default `selected`, it might cause 404 errors if we don't include the `if (selected) {...}`

- Our first `onChange()` for the first drop down look like this now:
```
onChange={(e) => {
  const c = cryptos?.find((x) => x.id === e.target.value);
  setSelected(c);
}}
```
- Our second `onChange()` for the second drop down look like this now:
```
onChange={(e) => {
  setRange(e.target.value);
}}
```
- When we have the range equal to 1 day, the line chart won't look very informative, so what we need to do is to set the interval in the API url to hourly if the value of the second drop down is equal to 1 day. Unfortunalty CoinGecko requires a membership in order to access their hourly interval so this won't be possible for us.
- We called the `setOptions()` in our `useEffect()` we just created, and also copied other options from the default options sate to the `useEffect()`:
```
setOptions({
  responsive: true,
  plugins: {
    legend: {
      position: "top" as const,
    },
    title: {
      display: true,
      text: "Price Over Last " + range + ` Day` + (range==="1"?"":"s"),
    },
  },
});
```

# #########################################################################################
# Part.53 - Crypto Price Chart with Chart.js

- We need to install `charts.js` and `react-chartjs-2`, to do so we should type in the terminal:
```
npm install chart.js react-chartjs-2
```
- There are good usage examples with `react-chartjs-2`, so if we go to their examples page `react-chartjs-2.js.org/examples`, we can see some chart examples code that we can import and a quick example.
- We will use a line chart so you can get the code from `react-chartjs-2.js.org/examples/line-chart`, and go to file explorer from the online editor they have to see the code needed for the line chart.
- We will copy:
  - The imports
  - The `options={...}` object which will say the should be on the chart such as `legend` or `title`
  - The `labels=[...]` array which is going to show the x-axis values
  - The `dataset: [...]` array where we will pass in our own data, in the `data:` property.
  - Then we will use state for these `data` and for these `options`, so changing any of values will re-render the content on the page.
- We are going to firstly paste the imports into `App.tsx`:
```
import {
  Chart as ChartJS, CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend,
} from "chart.js";
import { Line } from "react-chartjs-2";

ChartJS.register( CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend);
```
- We are going to create new state variabels in `App.tsx`:
```
const [data, setData] = useState();
const [options, setOptions] = useState();
```
- The types for the state variables are going to get also imported inside `App.tsx`:
```
import type { ChartData, ChartOptions } from 'chart.js';
```
- We can assign the types to our state variables like so: 
```
const [data, setData] = useState<ChartData<"line">>();
const [options, setOptions] = useState<ChartOptions<"line">>();
```
- Both `ChartData` and `ChartOptions` are generic types so we need to pass in a value inside of `<>`, and we did pass the value `"line"` above which is the chart type we want.
- Now, we can render a line chart at the end of the return before the `</>` in `App.tsx`:
```
{data ? <Line options={options} data={data} /> : null}
```
- Now, in order to get this working we need to add a default value inside of the `options` state variable: (the default options are the ones we will get from the website in the `options = {...}`)
```
  const [options, setOptions] = useState<ChartOptions<"line">>({
    responsive: true,
    plugins: {
      legend: {
        position: "top" as const,
      },
      title: {
        display: true,
        text: "Chart.js Line Chart",
      },
    },
  });
```
- Now what we want to do is when we select a drop down value, which is going to be in the `onChange()` event handler, we will get a new request and then update the data state.
```
onChange={(e) => {
  const c = cryptos?.find((x) => x.id === e.target.value);
  setSelected(c);
  axios.get(url).then((response) => {
    setData({...});
  });
}}
```
- The url to the api we are going to make a request to is: (from CoinGecko)
```
https://api.coingecko.com/api/v3/coins/bitcoin/market_chart?vs_currency=usd&days=30&interval=daily
```
- What we are going to pass in the `setData({...})` is the dataset we copied from the website:
```
setData({
  labels: [1, 2, 3, 4],
  datasets: [
    {
      label: "Dataset 1",
      data: [4, 7, 10, 3],
      borderColor: "rgb(255, 99, 132)",
      backgroundColor: "rgba(255, 99, 132, 0.5)",
    },
  ],
});
```
- We added some styling to our line chart:
```
<div style={{ width: 600 }}>
  <Line options={options} data={data} />
</div>
```
- We are then going to put our `labels` and `data` as follows:
```
setData({
  labels: response.data.prices.map((price: number[]) => {
    return price[0];
  }),
  datasets: [
    {
      label: c?.id.toUpperCase(),
      data: response.data.prices.map((price: number[]) => {
        return price[1];
      }),
      borderColor: "rgb(255, 99, 132)",
      backgroundColor: "rgba(255, 99, 132, 0.5)",
    },
  ],
});
```
- We also changed the `.get(url)` url to allow us to change based on the selected value:
```
.get(`https://api.coingecko.com/api/v3/coins/${c?.id}/market_chart?vs_currency=usd&days=30&interval=daily`)
```
- We need to update the timestamp values to be a more readable dates, we can do this `moment` package, we need to install it by typing:
```
npm install moment
```
- Then we are going to import it in `App.tsx`:
```
import moment from "moment";
```
- To use moment, we will go to where we are getting these dates value and change it to:
```
labels: response.data.prices.map((price: number[]) => {
  return moment.unix(price[0] / 1000).format("MM-DD-YYYY");
}...)
```
- We divided the timestamp by a 1000, because our timestamps data is in ms, and `.unix(...)` uses seconds.
- Since I am using 2 different APIs, I got some 404 errors (Page not found) so I went back and used the original coingecko API:
```
https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false&locale=en
```
- Don't forget to change `response.data.data` to `response.data`
- Don't forget to change `Types.tsx` to:
```
export type Crypto = {
  circulating_supply: number;
  current_price: number;
  fully_diluted_valuation: number;
  high_24h: number;
  id: string;
  image: string;
  low_24h: number;
  market_cap: number;
  market_cap_change_24h: number;
  market_cap_change_percentage_24h: number;
  market_cap_rank: number;
  max_supply: number;
  name: string;
  price_change_24h: number;
  price_change_percentage_24h: number;
  symbol: string;
  total_supply: number;
  total_volume: number;
};
```
- Don't forget to change `CryptoSummary.tsx` return to:
```
return <p>{crypto.name + " $" + crypto.current_price}</p>;
```
# #########################################################################################
# Part.52 - Generate Drop Down List from API

We will return now a drop down list in `App.tsx`, which would make our website look more appealing:
```
return (
  <div className="App">
    <select>
      {cryptos
        ? cryptos.map((crypto) => {
            return <option>{crypto.name}</option>;
          })
        : null}
    </select>
  </div>
);
```
- We surround the ternary operator with a `<select>...</select>` tag and each option will be surrouned with an `<option>...</option>` tag.
- We also added these attributes to the option tag `<option key={crypto.id} value={crypto.id}>`, the key is for the react errors we might get, and the value is to decide which one is clicked.

- What we have to do now is to add the `onChange()` event handler in the select tag:
```
<select
  onChange={(e) => {
    const c = cryptos?.find((x) => x.id === e.target.value);
  }}
>
```
- What we are basically doing is we are trying to find (search through the list) the element with the id of the chosen input, then when it finds it, it will assign it to the variable `const c`.
- We can display this information on page, and the best way to do that is to have a `selected` state variable.
```
const [selected, setSelected] = useState<Crypto | null>();
```
- Note the type is a single `Crypto` and not an array of type `Crypto`.
- Now in the `onChange()` event handler, we can do `setSelected(c);`
- We can also now render the selected value, by using the `selected` state variable: (after the `</div>` tag in the return of `App.tsx`, note we should also surround everything inisde of the return with an empty tag `<>...</>`)
```
{selected ? <CryptoSummary crypto={selected} /> : null}
```
- We can have a default value that would say something like `Select an Option`: (it should be above of the `.map()` and above the `cryptos` ternary)
```
<option selected disabled>
  Choose a Crypto Currency
</option>
```
# #########################################################################################
# Part.51 - TypeScript Components

We can use the following link as a cheat sheet if we want extra help regarding TypeScript:
```
https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/function_components
```
- We are going now to create our first component. So inside of `src` folder we will create another folder called `components`, and inside the `components` folder, we are going to create a file called `CryptoSummary.tsx`

- We are going to move the functionality of displaying a crypto name and price into that new `CryptoSummary.tsx` component.
- We added this code to `CryptoSummary.tsx`:
```
export default function CryptoSummary(props: any) {
  return <p>{crypto.name + " $" + crypto.current_price}</p>;
}
```
- We returned the `<CryptoSummary/>` component in `App.tsx`:
```
return <CryptoSummary crypto={crypto} />;
```
- As you can see above we can assign `props: any`, this means it will accept any type however this defeats the purpose of TypeScript, the whole point is to statically type our variables in order to avoid run-time errors.
- What we should/will do is the following: 
```
import { Crypto } from "../App";

export type AppProps = {
  crypto: Crypto;
};

export default function CryptoSummary({ crypto }: AppProps) {
  return <p>{crypto.name + " $" + crypto.current_price}</p>;
}
```
- The parameter is an object that will be passed so we won't need to do `props.`, in other words the `CryptoSummary` functional component is destructuring the crypto prop directly in its parameter list.
- We assigned the `{crypto}` object a type we will create called `AppProps`
- `AppProps` will assign the object passed down `crypto`, a type of `Crypto.`
- We can import the type we created in `App.tsx`, as we will need to assign it to the crypto object `{crypto}`

- We can also specify the return type which in our case is `JSX.Element`, and we do this in `CryptoSummary.tsx`:
```
export default function CryptoSummary({ crypto }: AppProps): JSX.Element {...}
```
- The `: JSX.Element` means that the return type of the function `CryptoSummary()` is `JSX.Element`, hence for example we can't return the number 5. Assigning the return type of a component might not be helpful but we can always do it to ensure our code is doing what we want it to do.

- We can define the types we created such as `Crypto` and `AppProps` in a file called `Types.tsx`:
```
export type Crypto = {
  circulating_supply: number;
  current_price: number;
  fully_diluted_valuation: number;
  high_24h: number;
  id: string;
  image: string;
  low_24h: number;
  market_cap: number;
  market_cap_change_24h: number;
  market_cap_change_percentage_24h: number;
  market_cap_rank: number;
  max_supply: number;
  name: string;
  price_change_24h: number;
  price_change_percentage_24h: number;
  symbol: string;
  total_supply: number;
  total_volume: number;
};
```
- Then when we want to use it we can import it like so: `import { Crypto } from "./Types";`

- You might also come with the phrase `interaces` instead of `types`, we can use either of them to type props and state, however we can consider using `type` for our React Component Props and State, for consistency and because it is more constrained.

# #########################################################################################
# Part.50 - TypeScript and Axios Intro

We are going to start with a completely new app, and we will be adding 2 interesting things, which are TypeScript and Axios.

- We are going now learn how to start a TypeScript React project
- TypeScript is very similar to JavaScript but it adds static typing so our variables are going to have types.
  - This will help prevent runtime errors during the execution of the software by moving those errors to compile time errors.

 - To start a new Create React App project with TypeScripts we can run:
```
npx create-react-app cryptocurrencies --template typescript
```
- Then we can type this into the command to open our project code on vsc:
```
code cryptocurrencies
```
- On the left hand side we can see the files/folders of our project, we will notice a pretty similar structure with a couple of differencies such the `react-app-env.d.ts` file.

- We are going to remove the files that we will not need in our `src` folder and our final `src` project directory should look like this:
```
>node_modules
>public
^src
    App.css
    App.tsx
    index.css
    index.tsx
    react-app-env.d.ts
.gitignore
package-lock.json
package.json
README.md
tsconfig.json
```
- We can see at the bottom the `tsconfig.json` file, this file is where we can config our TypeScript rules.
- You can notice now that the JavaScript files end with `.tsx`

- We deleted everything inside of the `App.css` file.
- We deleted everything inside of the `index.css` file.
- We edited the `index.tsx` file and it should look like this now:
```
import React from "react";
import ReactDOM from "react-dom/client";
import "./index.css";
import App from "./App";

const root = ReactDOM.createRoot(
  document.getElementById("root") as HTMLElement
);
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```
- We edited the `App.tsx` file and it should look like this now:
```
import React from "react";
import "./App.css";

function App() {
  return <div className="App">Hello</div>;
}

export default App;
```
- To run this application we can `cd` into `cryptocurrencies` file in the terminal and run:
```
npm run start
```
- The other thing we are going to use is `Axios` which is another way to make requests, pretty similar to `fetch()` but slightly different and a little bit cleaner.

- Now we want to install `Axios`, so in the terminal we should type:
```
npm install axios
```
- Now we need to import it inside `App.tsx`:
```
import axios from "axios";
```
- We are going now to use an API for cryptocurrencies that doesn't use API key. We are going to use `CoinGecko API`.
  - from `coingecko.com/en/api/documentation`
  - under coins
  - open the `Get /coins/markets`
  - click the `Try it Out` button
  - scroll down a bit and hit `Execute`
  - you'll see a `Request URL` copy it (`https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false&locale=en`)

- Now in `App.tsx` we can assign it to a value:
```
const url = 'https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false&locale=en';
```
- `url` is a string so we don't have to worry about the type too much, because the type is inferred from the value we assigned to the variable `url`.
  - The type is automatically deiced for `url` because of what we assigned to it.
  - That's all done at compile time, because TypeScript is compiled down to regular JavaScript.

- Now to actually get the data from the API, we can get the `url` inside a `useEffect()` hook:
```
function App() {
  const [cryptos, setCryptos] = useState();

  useEffect(() => {
    const url = "https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false&locale=en";
    axios.get(url).then((response) => {
      setCryptos(response.data);
    });
  }, []);

  return <div className="App"></div>;
}

export default App;
```
- You can see it is pretty similar to the `fetch()` method we used to use.
- The primary difference is that we do not need to a `return response.json();` and have an another `.then()`, instead we can just `return resposne.data;` which is going to have the actuall data from the request.
- The `, []` will make it only execute once on initial page load. (Empty Dependency Array)

- We create a state variable so we can store the `response.data`:
```
const [cryptos, setCryptos] = useState();
```
- In the `.then(response)` we do `setCryptos(response.data);`

- We'll try to display the data now so in the return:
```
return <div className="App">{cryptos ? cryptos.map() : null}</div>;
```
- We might get an error for `.map()`, we can say that the data being returned from the API should match some structure we define in our code, what this would look like is defining a type outside the `function App(){...}`:
```
export type Crypto = {
  circulating_supply: number;
  current_price: number;
  fully_diluted_valuation: number;
  high_24h: number;
  id: string;
  image: string;
  low_24h: number;
  market_cap: number;
  market_cap_change_24h: number;
  market_cap_change_percentage_24h: number;
  market_cap_rank: number;
  max_supply: number;
  name: string;
  price_change_24h: number;
  price_change_percentage_24h: number;
  symbol: string;
  total_supply: number;
  total_volume: number;
};
```
- Then we can change the call of our `useState()` hook by setting the type to `Crypto[]` and then we can use the `|` (or symbol) then say `null`. So this can either be null or it is going to be an array of `Crypto[]`:
```
const [cryptos, setCryptos] = useState<Crypto[] | null>();
```
- Then what we have to do now is to create an arrow function inside the `.map()` method as an argument, that would just return the names of crypto and their prices:
```
  return (
    <div className="App">
      {cryptos
        ? cryptos.map((crypto) => {
            return <p>{crypto.name + " $" + crypto.current_price}</p>;
          })
        : null}
    </div>
  );
```
- Reminder, `null` and `undefined` are not the same in JavaScript. `Undefined` is when a variable has no value where `null` is a value and this value is just nothing.

- If you want to follow the code you can check this repository:
```
https://github.com/HashimAlHasani/ts-axios
```
# #########################################################################################
# Part.49 - Custom Hook on Button Click (onClick POST with useFetch)

- So now we lost the ability to add customers to the customers list, and we can't do our normal `useFetch()` and pass `method: "POST"` this will not work. What we can do is make `UseFetch.js` return a function that can be invoked on button click. So what we need to do now is to create a function in `UseFetch.js`.

- In `Customers.js` we changed the `newCustomers()` function:
```
function newCustomer(name, industry) {
  appendData({ name: name, industry: industry });
}
```
- The `appendData()` function is actually the function returned from `UseFetch.js`.
- In `Customers.js`, we can do:
```
const {
  request,
  appendData,
  data: { customers } = {},
  errorStatus,
} = useFetch(url, {
  method: "GET",
  headers: {
    "Content-Type": "application/json",
    Authorization: "Bearer " + localStorage.getItem("access"),
  },
});
```
- The `request` above is the returned function from `UseFetch.js`, and is the original function to get the data.
- The `appendData` above is also a returned function from `UseFetch.js`, and is the function to apend our data.

- In `UseFetch.js` we removed the `useEffect()` hook, and made a new function called `request()`:
```
function request() {
  fetch(url, {
    method: method,
    headers: headers,
    body: body,
  })
    .then((response) => {
      if (response.status === 401) {
        navigate("/login", {
          state: {
            previousUrl: location.pathname,
          },
        });
      }
      if (!response.ok) {
        throw response.status;
      }
      return response.json();
    })
    .then((data) => {
      setData(data);
    })
    .catch((e) => {
      setErrorStatus(e);
    });
}
```
- In `UseFetch.js` we also created another function called `appendData()`:
```
function appendData(newData) {}
```
- In `UseFetch.js` we changed the return of our main function to return the call of both functions:
```
return { request, appendData, data, errorStatus };
```
- Now in `Customers.js` to get our customers data we just have to invoke the request function in a `useEffect()` once:
```
useEffect(() => {
  request();
}, []);
```
- We created the `appendData()` function in `UseFetch.js`:
```
function appendData(newData) {
  fetch(url, {
    method: "POST",
    headers: headers,
    body: JSON.stringify(newData),
  })
    .then((response) => {
      if (response.status === 401) {
        navigate("/login", {
          state: {
            previousUrl: location.pathname,
          },
        });
      }

      if (!response.ok) {
        throw response.status;
      }

      return response.json();
    })
    .then((d) => {
      const submitted = Object.values(d)[0];

      const newState = { ...data };
      Object.values(newState)[0].push(submitted);
      setData(newState);
    })
    .catch((e) => {
      setErrorStatus(e);
    });
}
```
- We need to focus on the `.then((d) => {...})` part really well since it might get confusing.
  - First thing we grab the object that is being added to the array: `const submitted = Object.values(d)[0];`
  - Then duplicate the existing state, which is going to be a new object in memory: `const newState = { ...data };`
  - Then we push the new object onto that new state: `Object.values(newState)[0].push(submitted);`
  - Then we replace the existing state with that new object: `setData(newState);`

- We have done this in order to make it generic, so that if we want to use the same hook for another list it would also work. so what `Object.value(d)[0]` gets in our customers list, the new added customer, and `Object.values(newState)[0]` will get us the customers array.

- Now in the `Customers.js` we just need to toggle show the pop model when we press the add button:
```
function newCustomer(name, industry) {
  appendData({ name: name, industry: industry });

  if (!errorStatus) {
    toggleShow();
  }
}
```
- What we need to do now is to adjust the `Definition.js` since we might broke it with our new `UseFetch.js` edits.
- We need to alter the things we care about:
  - Added the request function while using our `useFetch()` hook:
  ```
  const { request, data: [{ meanings: word }] = [{}], errorStatus } = useFetch(
    "https://api.dictionaryapi.dev/api/v2/entries/en/" + search
  );
  ```
  - Added a new `useEffect()` hook to call our request function:
  ```
  useEffect(() => {
    request();
  }, []);
  ```
# #########################################################################################
# Part.48 - Default Values and Nested Data with Destructuring

If we try to destructure a property that doesn't exist on an object we might get runtime errors, or exception thrown.

- We changed how we called our `useFetch()` hook in `Definition,js`:
```
const { data: word, errorStatus } = useFetch(
  "https://api.dictionaryapi.dev/api/v2/entries/en/" + search
);
```
- In `UseFetch.js` we did this change:
```
export default function useFetch(url, { method, headers, body } = {})
```
- Looking at the `= {}`, what this will do is assign an empty object if this `{ method, headers, body }` is undefined.
- In `Definition.js` instead of `word[0].meanings` we can do at the top:
```
const { data: [{ meanings: word }] = [{}], errorStatus } = useFetch(
    "https://api.dictionaryapi.dev/api/v2/entries/en/" + search
);
```
- Then in the return we can change `word[0].meanings` to `word` and `word?.[0]?.meanings` to `word`.

# #########################################################################################
# Part.47 - Destructuring Explained (Custom Hook Parameters and Return Data)

We want to make our `useFetch()` hook more useable across our react application especially when we make a CRUD operation such as in `Customers.js`, or authorizing the user via authorization tokens such as in `Customer.js`.

- We are going to edit our `UseFetch()` to achieve a more useable hook:
```
import { useState, useEffect } from "react";
import { useLocation, useNavigate } from "react-router-dom";

export default function useFetch(url, method, headers, body) {
  const [data, setData] = useState();
  const [errorStatus, setErrorStatus] = useState();
  const navigate = useNavigate();
  const location = useLocation();

  useEffect(() => {
    fetch(url, {
      method: method,
      headers: headers,
      ...(method !== "GET" ? { body: JSON.stringify({ body }) } : {}),
    })
      .then((response) => {
        if (response.status === 401) {
          navigate("/login", {
            state: {
              previousUrl: location.pathname,
            },
          });
        }
        if (!response.ok) {
          throw response.status;
        }
        return response.json();
      })
      .then((data) => {
        setData(data);
      })
      .catch((e) => {
        setErrorStatus(e);
      });
  }, []);

  return [data, errorStatus];
}
```
- In `Customer.js` we can call it in such a way: (Since it is a `GET` method then it won't have a body)
```  
const result = useFetch(url, "GET", {
  "Content-Type": "application/json",
  Authorization: "Bearer " + localStorage.getItem("access"),
});
```
- We can improve the way we are returing and passing data by using Destructuring in `UseFetch.js`.
- First we are going to return an object instead of array: `return { data, errorStatus };`
- This will change the way inside of `Customers.js` that we are retrieving those values:
```
  const { data, errorStatus } = useFetch(url, "GET", {
    "Content-Type": "application/json",
    Authorization: "Bearer " + localStorage.getItem("access"),
  });
```
- This will decrease the chance of typing things out incorrectly, so as you can notice the variable names in the return in `UseFetch.js` are the same as the variable names in `Customers.js`
- We can also do the same for the `function useFetch(url, method, headers, body)` parameters:
```
export default function useFetch(url, { method, headers, body }) {...}
```
- So now when we put the arguments in our `useFetch()` call, the order won't really matter as long as we write the same parameter/argument name. So when we call our `useFetch()` in `Customers.js`:
```
const url = baseUrl + "api/customers/";
const {
  data: { customers } = {},
  errorStatus,
} = useFetch(url, {
  method: "GET",
  headers: {
    "Content-Type": "application/json",
    Authorization: "Bearer " + localStorage.getItem("access"),
  },
});
```
- We did the `data: {customers}` so that we don't have to everytime call `data.customers` which might be annoying, and we make it `= {}` to prevent any accessing of an undefined.

# #########################################################################################
# Part.46 - Create a Custom Hook (useFetch)

If we ever have some functionality that we want to repeat throughout our application we create our own hook, on our case we are going to create a `useFetch()`, which is going to allow us to isolate all of the fetching logic in one location, and then anytime we need to get something from our API we can just use that hook and save a lot of lines of code.

- To create the `useFetch()` custom hook, we are going to create a new folder inside the `src` folder called `hooks`.
- Inside `hooks` folder we are going to create a file called `UseFetch.js`:
```
import { useState, useEffect } from "react";

export default function useFetch(url) {
  const [data, setData] = useState();
  const [errorStatus, setErrorStatus] = useState();

  useEffect(() => {
    fetch(url)
      .then((response) => {
        if (!response.ok) {
          throw response.status;
        }
        return response.json();
      })
      .then((data) => {
        setData(data);
      })
      .catch((e) => {
        setErrorStatus(e);
      });
  });

  return [data, errorStatus];
}
```
- The `UseFetch.js` will perform a basic fetch as we always do inside a `useEffect()` hook, and would use 2 state variables, one for the `data` and one for the `errorStatus`, and will return them as an array.

- We will use our custom hook `useFetch()` in `Definition.js`, and after refactoring our code will look much cleaner and shorter:
```
import { useParams, Link } from "react-router-dom";
import { v4 as uuidv4 } from "uuid";
import NotFound from "../components/NotFound";
import DefinitionSearch from "../components/DefinitionSearch";
import useFetch from "../hooks/UseFetch";

export default function Definition() {
  let { search } = useParams();

  const [word, errorStatus] = useFetch(
    "https://api.dictionaryapi.dev/api/v2/entries/en/" + search
  );

  if (errorStatus === 404) {
    return (
      <>
        <NotFound />
        <Link to="/dictionary">Search another</Link>
      </>
    );
  }

  if (errorStatus) {
    return (
      <>
        <p>Something went wrong, try again?</p>
        <Link to="/dictionary">Search another</Link>
      </>
    );
  }

  return (
    <>
      {word?.[0]?.meanings ? (
        <>
          <h1>Here is a definition:</h1>
          {word[0].meanings.map((meaning) => {
            return (
              <p key={uuidv4()}>
                {meaning.partOfSpeech + ": "}
                {meaning.definitions[0].definition}
              </p>
            );
          })}
          <p>Search again:</p>
          <DefinitionSearch />
        </>
      ) : null}
    </>
  );
}
```
# #########################################################################################
# Part.45 - User Register Form and API

- In `urls.py` we added a new path:
```
path('api/register/', views.register, name='register'),
```
- In `views.py` we are going to create the register function:
```
def register():
    pass
```
- In `serializers.py` we are going to define another serializer which is the user serializer:
```
from django.contrib.auth.models import User

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = '__all__'
```
- Also in `serializers.py` we are going to define a function called `create` inside the `UserSerializer` Class:
```
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = '__all__'

    def create(self, validated_data):
        user = User.objects.create(
            username=validated_data['username'],
            email=validated_data['email']
        )

        user.set_password(validated_data['password'])
        user.save()
        return user
```
- In `views.py` make sure to import at first `UserSerializer` from `customers.serializers`, then in the register function:
```
@api_view(['POST'])
def register(request):
    serializer = UserSerializer(data=request.data)
    if serializer.is_valid():
        serializer.save()
        return Response(status=status.HTTP_201_CREATED)
```
- Now we can visit `localhost:8000/api/register`, and put in the Content text for example:
```
{
"email": "hello@hello.com",
"username": "Tomiisme",
"password": "password"
}
```
- Then we can hit post and see the `HTTP 201 Created`, then we can go to `localhost:8000/admin` go to the `Users` table and see that a new user is created.

- In `pages` folder, we created a new file called `Register.js`, and copied `Login.js` code into it.
  - We changed function name to `function Register()`
  - Created a new state for email: `const [email, setEmail] = useState("");`
  - Changed the url we are sending this to: `const url = baseUrl + 'api/register/';`
  - In the fetch body attribute we added: `email: email,` in `JSON.stringify({...})`
  - Added a new input in the form for the email. (copy paste username `div` and edit names)

- We need now to return an `access` and `refresh` token on a successful register, so in `views.py`:
```
from rest_framework_simplejwt.tokens import RefreshToken
```
- We made changes to `def register()`:
```
@api_view(['POST'])
def register(request):
    serializer = UserSerializer(data=request.data)
    if serializer.is_valid():
        user = serializer.save()
        refresh = RefreshToken.for_user(user)
        tokens = {
            'refresh': str(refresh),
            'access': str(refresh.access_token)
        }
        return Response(tokens, status=status.HTTP_201_CREATED)
    return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```
- In `App.js` we added a new Route for Register:
```
<Route path="/register/" element={<Register />} />
```
- In `Register.js` we added a `useEffect()` that will log users out if they visit this page:
```
useEffect(() => {
  localStorage.clear();
  setLoggedIn(false);
}, []);
```
# #########################################################################################
# Part.44 - Auth Refresh Tokens

- The `access` token will expire fairly quickly and nobodys want to log in every couple of minutes (that time can be configured from the backend).
- The `refresh` token will give you a new `access` token every couple of minutes so you don't have to log in over and over.
- The `refresh` token is going to have a longer lifetime (example a day).
- Everytime you use your `refresh` token to get a new `access` token, it will give you also a new `refresh` token aswell.
- We are going to create a loop that is going to execute every couple of minutes to get a new `access` token and a new `refresh` token.
- We will store the refresh token in localStorage, but this is not 100% secure because malicious users can get the `access` token using some scripting, however, we can reduce the chances of this happening by reducing the absolute token `expiration` time of tokens.

- We created a new dictionary in `settings.py`, in order to make every `refresh` token give us another `refresh` token and made the lifetime of the `access` token to 15 minutes:
```
from datetime import timedelta

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=15),
    'ROTATE_REFRESH_TOKENS': True,
}
```
- In `settings.py` we can see `SECRET_KEY = '...'`, this is a Django SECRET_KEY, and it's important to keep this key confidential for security reasons. If it got leaked people will be able to generate their own tokens using the `SECRET_KEY` value.
- One way we can make the `SECRET_KEY` more secure is by loading it from an environment variable: (we didn't implement this as this website is not for production but read more about it if you have a website for production)
- This website will really help:
```
https://dev.to/themfon/how-to-protect-your-django-projects-secret-key-2ac6
```
- We are going to create a loop that will just execute every couple minutes to get a new `access` and `refresh` token. The loop executing time must be less than the `access` token expiration time.
- In `App.js`, we are going to use a `useEffect()` hook, inside it we are going to use a `setIntervanl()` method on a function that will happen every 3 minutes (for testing purposes), the function will fetch the url and make a `POST` request, and will create a new refresh token every 3 minutes, then we store this in localStorage:
```
useEffect(() => {
  function refreshTokens() {
    if (localStorage.refresh) {
      const url = baseUrl + "api/token/refresh/";

      fetch(url, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          refresh: localStorage.refresh,
        }),
      })
        .then((response) => {
          return response.json();
        })
        .then((data) => {
          localStorage.access = data.access;
          localStorage.refresh = data.refresh;
          setLoggedIn(true);
        });
    }
  }
  const minute = 1000 * 60;
  refreshTokens();
  setInterval(refreshTokens, minute * 3);
}, []);
```
# #########################################################################################
# Part.43 - Create a Logout Button

- Right now in `App.js` we have the `loggedIn` state set to `true` by default which doesn't really make sense.
- What the fixes is actually going to do for us, is when we log in on 1 tab, and then open `localhost:3000/customers` on another tab, it should sustain our access and not ask us to log in again.
- We can do 2 fixes:
  - Check the localStorage for an `access` token, however, it may be expired.
  ```
  const [loggedIn, setLoggedIn] = useState(localStorage.access ? true : false);
  ```
  - Long term goal, use `refresh` token and if it works, stay logged in, otherwise, send to login page -> this will be implemented later on.

- We changed the ternary operator for the `login` `logout` we previously had in `Header.js` and we created a new ternary operator that should work in a better way and allow us to actually log out:
```
{loggedIn ? (
  <NavLink
    to={"/login"}
    onClick={() => {
      setLoggedIn(false);
      localStorage.clear();
    }}
    className="block rounded-md px-3 py-2 text-base font-medium no-underline text-gray-300 hover:bg-gray-700 hover:text-white "
  >
    Logout
  </NavLink>
) : (
  <NavLink
    to={"/login"}
    onClick={() => {}}
    className="block rounded-md px-3 py-2 text-base font-medium no-underline text-gray-300 hover:bg-gray-700 hover:text-white "
  >
    Login
  </NavLink>
)}
```
- What we also want to do is that anytime we set the `loggedIn` state to false, we'll also do `localStorage.clear()`.
- In order to do this in `App.js` we created this function:
```
function changeLoggedIn(value) {
  setLoggedIn(value);
  if (value === false) {
    localStorage.clear();
  }
}
```
- We also need to change what we passed in, from `setLoggedIn` to `changeLoggedIn`, in `App.js`:
```
<LoginContext.Provider value={[loggedIn, changeLoggedIn]}>
```
# #########################################################################################
# Part.42 - useContext Hook Introduction

if we are working with some state, and we want the state to be used across the entire application, that is where `useContext()` can come in handy, this will prevent us from having to pass props down multiple layers, when we would want to us this:
- A theme across the website.
- Logging in.
  - So instead of trying to figure out if the user is logged in/out on all of our different components of our website, we are going to create a context at the root level and surround our entire application with that code, so whether users are logged in or not will be accessible through the entire application.

- We are going to change the `Calendar` in the dashboard to a log in/out button, and which value is shown depends on the current state of that user.

- At first we need to create a state at the global level, and we will try to access it from one of our components.

- in `App.js` we will firstly need to import:
```
import { createContext } from 'react';
```
- and then outside the `function App(){...}` we are going to write: (export will allow us to import this into other files)
```
export const LoginContext = createContext();
```
- we will also to include its tag arround our `<BrowserRouter>...</BrowserRouter>` and create a logged in state:
```
...
import { createContext, useState } from "react";

export const LoginContext = createContext();

export default function App() {
  const [loggedIn, setLoggedIn] = useState(true);

  return (
    <LoginContext.Provider value={[loggedIn, setLoggedIn]}>
      <BrowserRouter>
        <Header>
          <Routes>
            ...
          </Routes>
        </Header>
      </BrowserRouter>
    </LoginContext.Provider>
  );
}
```
- To apply this to one of our components for example the `Header.js`, we will go the `Header.js` import these:
```
import { useContext } from "react";
import { LoginContext } from "../App";
```
- Then outside/above the `return(...)` we can:
```
const loggedIn = useContext(LoginContext);
```
- We want to add a functionality where we can toggle the `loggedIn` state value (we previously set it to default true), so in `App.js`:
```
<LoginContext.Provider value={[loggedIn, setLoggedIn]}>
```
and to use actually use it in the `Customers.js` page:
```
const [loggedIn, setLoggedIn] = useContext(LoginContext);
```
do not forget to do these imports in `Customers.js`:
```
import { useContext } from "react";
import { LoginContext } from "../App";
```
also in `Customers.js`, we can add in the `fetch()` method in the `.then(data)` section if `response.status === 401` (which means unauthorized)
```
setLoggedIn(false);
```
- We are going to use this functionality in the `Header.js` to begin with we removed the Calendar from our navigation array:
```
const navigation = [
  { name: "Employees", href: "/Employees" },
  { name: "Customers", href: "/Customers" },
  { name: "Dictionary", href: "/Dictionary" },
];
```
- In `Login.js` we `setLoggedIn(true)`, if the log in is successful in `.then(data)` section.
- In `Customers.js` we `setLoggedIn(false)` for every `if(response.status === 401)`.
- In `Customer.js` we `setLoggedIn(false)` for every `if(response.status === 401)`.
- In `Header.js` we added: (after the `navigation.map(...)` for both mobile and desktop design)
```
<NavLink
  to={loggedIn ? "/logout" : "/login"}
  className="block rounded-md px-3 py-2 text-base font-medium no-underline text-gray-300 hover:bg-gray-700 hover:text-white "
>
  {loggedIn ? "Logout" : "Login"}
</NavLink>
```
# #########################################################################################
# Part.41 - useLocation and useNavigate State (Redirect to Previous Page on Login)

- Our log-in form doesn't redirect to a new page after we log in.
- We are going to send information to the login page to say where we came from, and this is going to be done by passing state with navigate.
- our `useNavigate` hook takes another argument which will be an object, and inside the object we are going to have `state: {...}` in `Customers.js`:
```
navigate("/login", {
  state: {
    previousUrl: "/customers",
  },
});
``` 
- Now in the `Login.js` we are going to get the information, we'll be using a new hook called `useLocation()`, and it is imported from `react-router-dom`:
```
const location = useLocation();
```
- `console.log(location.state.previousUrl)` in a `useEffect()` hook would give us `/customers`, this is the value we want to use when we navigate, so now in the login event handler, in `Login.js` in the `fetch()` method in `.then(data)`
we can type:
```
navigate(location.state.previousUrl);
```
- Don't forget to create a navigate variable at the top:
```
const navigate = useNavigate();
```
- Now, when we log in we will be directed to the `/customers` page.

- Now, we want to generate the current url when we pass in the previous url, what we are doing now is that we are hard coding `/customers` in `Customers.js`, what we want to do is basically say the `previousUrl` is the `currentUrl`. How we can know that they are currently on the `customers` page, this can come from `location` as well, so `location.pathname` should give what we want:
```
navigate("/login", {
  state: {
    previousUrl: location.pathname,
  },
});
```
- Don't forget to write at the top `const location = useLocation();` to assign the hook to a variable.

- Now, what we have to do is to copy this behaviour to any page that would redirect us to the login page, mostly in `Customer.js`.
- So lets say we are not logged in and we tried to visit `localhost:3000/customers/13`, it would redirect us to the login page, when we log in successfully, it will redirect us to the `previousUrl` which is equal to `location.pathname` which is `localhost:3000/customers/13`

- Now, we need to consider when we visit the `Login.js` directly, we don't have a `previousUrl`, so when we log in we might face an error, so to fix this issue we can do the following in the `.then(data)` section in `Login.js`:
```
navigate(
  location?.state?.previousUrl
    ? location.state.previousUrl
    : "/customers"
);
```
# #########################################################################################
# Part.40 - localStorage and Bearer Auth Tokens

- localStorage is a little database that stays on the browser of the client.
- When we give an `access` token, so that people who log in can use our API, we want that `access` token to be used throughout our application, and that's going to be sent with every request.
- So when I log in with a valid account I'll get an `access` token, so I want to take this value, save it somewhere (localStorage), and then use it for my future requests.

- We added some code to set `access` and `refresh` tokens on the localStorage in `Customers.js` in the `.then(data)` section:
```
localStorage.setItem("access", data.access);
localStorage.setItem("refresh", data.refresh);
console.log(localStorage);
```
- Somethings you might see are whether the website is on light/dark mode, this can be stored on localStorage.
- localStorage is saved temporarly, but is not guaranteed to be saved forever.
- We can type on the browsers console:
    - `localStorage.clear()`
    - `localStorage`
- We will see that the Storage length is 0, and this is similar to what we do when we log a user out (get rid of access and refresh properties).

- Now since we have these values (`access` and `refresh` tokens) we can use them in another request to prove that we have access to the API.
- For example to access the customers list, we can in `Customers.js` in the `fetch(url)` section, add an object parameter like so:
```
fetch(url, {
    headers: {
        "Content-Type": "application/json",
        Authorization: "Bearer " + localStorage.getItem("access"),
    },
})
```
- We will do the same for the `Customer.js` to access each individual customer.
- We still have to do one more thing, the buttons we have (Cancel, Save, Delete) would give us `401 (Unauthorized)` errors.
- To fix this add the if statement to each of the `fetch()` methods in the `.then(response)` section in `Customer.js`:
```
if (response.status === 401) {
    navigate("/login");
}
```
- Now, we just have to attach the access token, by adding the headers to each of the `fetch()` methods:
```
headers: {
    "Content-Type": "application/json",
    Authorization: "Bearer " + localStorage.getItem("access"),
}
```
# #########################################################################################
# Part.39 - Create a Login Page

- We will focus now on the front-end authentication.

- Now if we try to open `localhost:8000/api/customers`, since we are not authenticated we will get the error: `detail": "Authentication credentials were not provided.`
- Also, on the front-end if we try to open the customers tab or `localhost:3000/customers` we will not be able to see the list of customers (`401 Unauthorized status code`).

- Now, in the `Customers.js` what we need to do is add an if statement to handle the status code `401`, and we can do so in the `.then(response)` section in our `useEffect()` hook:
```
if (response.status === 401) {
    navigate("/login");
}
```
- We are also using the `useNavigate()` hook:
```
const navigate = useNavigate();
```
- So now whenever we are not logged in and try to open the customers tab it will redirect us to the `localhost:3000/login` or the login page.
- We need to do the same in `Customer.js` when we try to access a specified customer such as `localhost:3000/customers/12`.

- The next step is that we are going to make a log-in form then make a request to the backend to get the `access` token.

- We are going to create in the `pages` folder a new page called `Login.js`.
- We are also going to add the Routing in `App.js`: `<Route path="/login" element={<Login />} />`

- We took the form we had in `Customer.js`, and pasted it in `Login.js`.
- We changed `name` to `username`, and `industry` to `password`, and made proper edits for the form html.
- We took some button designs from `Customers.js` and added a `Login` button and attached the button designs to it.
- On submitting the form a function called `login` will be triggered.
- The `login()` function will fetch the url, and the fetch will have a body parameter which will set the username and password, and this means we will need to use the `useState()` hook and assign 2 state variables one for username and one for password.
- The `console.log(data);` we have will show us the `access` and `refresh` token if we log in using a valid account, if the credentials are wrong it will give us: `No active account found with the given credentials`
- `Login.js`:
```
import { useState } from "react";
import { baseUrl } from "../shared";

export default function Login() {
  const [username, setUsername] = useState("");
  const [password, setPassword] = useState("");

  function login(e) {
    e.preventDefault();

    const url = baseUrl + "api/token/";

    fetch(url, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        username: username,
        password: password,
      }),
    })
      .then((response) => {
        return response.json();
      })
      .then((data) => {
        console.log(data);
      });
  }

  return (
    <form className="m-2 w-full max-w-sm" id="customer" onSubmit={login}>
      <div className="md:flex md:items-center mb-6">
        <div className="md:w-1/4">
          <label htmlFor="username">Username</label>
        </div>
        <div className="md:w-3/4">
          <input
            className="bg-gray-200 appearance-none border-2 border-gray-200 rounded w-full py-2 px-4 text-gray-700 leading-tight focus:outline-none focus:bg-white focus:border-purple-500"
            type="text"
            id="username"
            value={username}
            onChange={(e) => {
              setUsername(e.target.value);
            }}
          />
        </div>
      </div>
      <div className="md:flex md:items-center mb-6">
        <div className="md:w-1/4">
          <label htmlFor="password">Password</label>
        </div>
        <div className="md:w-3/4">
          <input
            className="bg-gray-200 appearance-none border-2 border-gray-200 rounded w-full py-2 px-4 text-gray-700 leading-tight focus:outline-none focus:bg-white focus:border-purple-500"
            id="password"
            type="password"
            value={password}
            onChange={(e) => {
              setPassword(e.target.value);
            }}
          />
        </div>
      </div>
      <button className="bg-slate-800 hover:bg-slate-500 text-white font-bold py-2 px-4 rounded">
        Login
      </button>
    </form>
  );
}
```
- We are now officially logged in, but we are not doing anything with the `access` token, so the page is not acting as we want, we now need to take the `access` token and include in any of our requests.

# #########################################################################################
# Part.38 - Intro to JWTs and Authentication (JSON Web Tokens)

- JWTs - JSON Web Tokens (This will manage logging in with an API)
- The log in process:
    - Request to the server
    - Give them username and a password
    - The server will give us a token in response
    - Will use the token in our API request

- The tool we are going to use is called `Simple JWT` - `django-rest-framework-simplejwt.readthedocs.io`
- To install it do the following:
    - Navigate to the `backend` folder and activate `.venv` by typing `.venv\Scripts\activate`
    - Type `pip install djangorestframework-simplejwt`

- After you install, navigate to `settings.py`, and add an dictionary called `REST_FRAMEWORK`:
```
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': ('rest_framework_simplejwt.authentication.JWTAuthentication',)
}
```
- Now we can make a new path in the `urls.py` to get a token:
```
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
    ...
]
```
- Then open `http://localhost:8000/api/token/` on the browser, and you'll see that you have a username and password that you can input, write your credentials and click `POST` (these are the credentials you created your superuser with in part #26).
- We will get a `refresh` and `access` tokens after clicking `POST`
- We will include this `access` token with our requests to restricted API endpoints.
- What is meant by restricted API endpoints, we want one so that users have to be authenticated to access.

- Going back to our `views.py` we are going to use a new api decorator on top of both `customer()` and `customers()` functions:
```
@permission_classes([IsAuthenticated])
```
- We will need to import the following:
```
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticated
```
- Important Note: the `@api_view` decorator needs to go above the `@permission_classes` decorator.

- Now when we try to open `http://localhost:8000/api/customers/` we will get a `HTTP 401 Unauthorized` error, what we need to do now is to take the access token and include it in the request, and to do this we will need an API testing tool called `Postman`.

- Download Postman from google.
- After you download Postman, create an account and log in.
- Press the `+` button, and change the request to `GET`
- Write next to the `GET` the url which is `localhost:8000/api/customers` and press enter.
    - you'll see at the terminal below, `detail": "Authentication credentials were not provided.`
- Exactly as we expected, now we need to go the Authorization tab (under the url we just wrote)
- Select from the `Type` drop down menu `Bearer Token`
- On the right hand side you'll see an input for `Token`, insert the `access` token and press enter, or `Send`.
    - You'll now access the API endpoint and see your data in the terminal below.

- Now copy the `refresh` token, and go to `localhost:8000/api/token/refresh`, and you'll seen an input for the `refresh` token, copy and paste your `refresh` token in it and hit `POST`.
    - This will give us a new `access` token
    - So basically this `refresh` token allow us to get a new `access` token so our original `access` token expires frequently but we don't have to log in again with our username and password we can just keep track of that `refresh` token and use it as needed.
    - This will basically give you access for a period of time (30min for example) but that 30 minutes will restart if you're still active because we're going to use that `refresh` token.
    - So if you go away from your computer for 30 minutes, then you'll be kicked off, because at that point your `access` token and your `refresh` token will expire.

# #########################################################################################
# Part.37 - Tailwind CSS Form and Button Styling

- I'll push the new CSS styling to `https://github.com/HashimAlHasani/react` repository (it is public), so feel free to see what styling I have done.

- The commit name is going to be `Tailwind CSS Form and Button Styling`.

# #########################################################################################
# Part.36 - Display Form Errors on Page

- So when we face a `404` error while trying to search for a customer that is not in the our customers list, the delete button still persists.
- In `Customer.js` we just moved the delete button to the `{customer ? ... : ...}` ternary operator, so that if the customer doesn't exist the delete button would not be shown.

- We have another error, when we delete the text in one of the inputs (leave them empty) and press the save button, the form will randomly disappear.
- To fix it in the `Customer.js` in the `updateCustomer()` function, in the `.then(response)`, we throw an error:
```
if (!response.ok) throw new Error("Something went wrong");
```
- We also need to catch the error in the `.catch()`:
```
(e) => {setError(e.message);}
```
- We want to display an error message so we made a state variable at the top:
```
const [error, setError] = useState();
```
- We also made another ternary operator right after the delete button:
```
{error ? <p>{error}</p> : null}
```
- However, it still doesn't go away when we correctly save, to fix it in `Customer.js` in `.then(data)` we would set the state of `error` to undefined:
```
setError(undefined);
```
- We can also check for all status errors that we might face other than `404`, so to do this we can in the `useEffect()` where we try to fetch the url, in the `.then(response)` we can add:
```
if (!response.ok){
    throw new Error ('Something went wrong, try again later');
}
```
- We will also need to `.catch()` the error we just threw:
```
.catch((e) => {
    setError(e.message);
})
```
- And we will need to also remove the error message when it is successful in the `.then(data)`:
```
setError(undefined);
```
- We also moved the error ternary operator outside the customer ternary operator, so it would show if no valid customer is there and there is a status error.

# #########################################################################################
# Part.35 - Comparing State Objects

What we currently have is that when we add a letter then delete the letter we just added, we will still have the set changed state to equal to `true` and will have the Cancel and Save buttons shown.

- What we will do is track if the strings are equal or not inside a `useEffect()` hook so that it would dynamically show the effect whenever a state change occurs:
```
useEffect(() => {
    if (!customer) return;
    if (!customer) return;

    let equal = true;
    if (customer.name !== tempCustomer.name) equal = false;
    if (customer.industry !== tempCustomer.industry) equal = false;

    if (equal) setChanged(false);
});
```
- The `if (!customer) return` and `if (!customer) return` is a fix to a problem we would face which is that when we page refresh we won't get the initial values of `customer` because it is also coming from a `useEffect()`.

- We also added some tailwind css to the buttons: `className="m-2"`

# #########################################################################################
# Part.34 - Dynamic Edit Form to Update API Data

We want to create an input that would originally have old data, and we can type/delete in this input and when we click a save button the data in the database would be changed.

- We are mainly going to work with `Customer.js` file, and the first thing we are going to do is change the customer
`<p>` tags with `<input/>` tags like so:
```
<div>
    <p className="m-2 block px-2">ID: {tempCustomer.id}</p> 
    <input className="m-2 block px-2" type="text" value={customer.name}/>
    <input className="m-2 block px-2" type="text" value={customer.industry}/>
</div>
```
- Now we want to keep track of the old values and the new values, so to do this we need to have this setup in state:
```
const [tempCustomer, setTempCustomer] = useState();
```
- Also in the `.then(data)` section: (to keep track of the old value and have a copy of it)
```
setCustomer(data.customer);
setTempCustomer(data.customer);
```
- We changed the `value` of the `<input/>`:
```
<div>
    <p className="m-2 block px-2">ID: {tempCustomer.id}</p> 
    <input className="m-2 block px-2" type="text" value={tempCustomer.name}/>
    <input className="m-2 block px-2" type="text" value={tempCustomer.industry}/>
</div>
```
- We added an `onChange` attribute to the name `<input/>`:
```
onChange={(e) => {
    setTempCustomer({...tempCustomer, name: e.target.value});
}}
```
- We can track the `customer` state and the `tempCustomer` state by `console.log(...)` them in a `useEffect()` hook:
```
useEffect(() => {
    console.log('customer', customer);
    console.log('tempCustomer', tempCustomer);
});
```
- We also added the same `onChange` for the industry:
```
onChange={(e) => {
    setTempCustomer({...tempCustomer, industry: e.target.value});
}}
```
- Now, we want to have a save and cancel button to popup when someone makes a change, we'll create a state called `changed` to track if the `onChange` eventhandler is currently `true` or `false`:
```
const [changed, setchanged] = useState(false);
```
- We also added inside the `onChange`: `setChanged(true);`
- So right after the industry `<input/>`:
```
{changed ? 
<>
    <button onClick={(e) => {
        setTempCustomer({...customer});
        setChanged(false);
    }}>
        Cancel
    </button>
    <button onClick={updateCustomer}>
        Save
    </button>
</> : null}
```
- What we did in the code above is that we created 2 buttons, one for `Save` and one for `Cancel`, in the `Cancle` button we just set the temporary customar with the original customer data, and set changed to `false`, in the `Save` button, when clicked an `updateCustomer` function will triggered, where we coded the `updateCustomer` right above the function `return`:
```
function updateCustomer(){
    const url = baseUrl + 'api/customers/' + id;
    fetch(url, {
        method: 'POST', 
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify(tempCustomer)
    }).then((response) => {
        return response.json();
    })
    .then((data) => {
        setCustomer(data.customer);
        setChanged(false);
    })
    .catch()
}
```
- the `function updateCustomer()` will perform a `POST` method which will save the customer with the new customer data into the database.

# #########################################################################################
# Part.33 - Close Modal on POST Success (and Add Result to State)

- We tracked the state of `show` (which shows/hides the modal) from the parent component by doing in `Customers.js`:
```
const [show, setShow] = useState(false);
```
and at the bottom:
```
<AddCustomer newCustomer={newCustomer} show={show}/>
```
and in `AddCustomer.js`:
```
const [show, setShow] = useState(props.show);
```
and in `AddCustomer.js` too:
```
show={props.show}
```
- We also manage the `handleShow` and `handleClose` from the parent component too (`Customers.js`), we added a function called `function toggleShow()` in `Customers.js`:
```    
function toggleShow(){
    setShow(!show);
}
```
we also passed it onto `<AddCustomer../>`:
```
<AddCustomer newCustomer={newCustomer} show={show} toggleShow={toggleShow}/>
```
and in `AddCustomer.js` we replaced `handleShow, handleClose` with 
```
props.toggleShow
```
- We removed the `onClick` from the add `<button>` because we need to close out of it only if the add is successful.
- We called the `toggleShow()` function in the `Customers.js` file, in `.then(data)` section, so when we hit `Add` it will close the modal. 

- Now what we want is that when we add a customer it will automatically update the list, instead of requiring a page refresh, to do so we needed to do some editing inside the `Customers.js` file in the `.then(data)` section:
```
.then((data) => {
  toggleShow();
  setCustomers([...customers, data.customer]);
})
```
# #########################################################################################
# Part.32 - Popup Modal to Add Data (POST)

- We want to do a similar thing to `AddEmployee.js` in the customers sections, so we'll be using some similar code of `AddEmployee.js`.
- We created inside `components` folder a `AddCustomer.js` file, and copied `AddEmployee.js` code into it, but we did the necessary name changes.
- We also called `AddCustomer.js` in `Customers.js` after the `</ul>` we wrote: 
```
<AddCustomer newCustomer={newCustomer}/>
```
- For the `AddCustomer.js`:
  - We changed `full name` to `name`.
  - We changed `role` to `industry`.
  - We removed the `img` `div` since it is not needed in here.
  - We changed `employee` to `customer`.

- We also implemented the `function newCustomer()` in `Customers.js`:
```
function newCustomer(name, industry){
    const data = {name: name, industry: industry};
    const url = baseUrl + 'api/customers/';
    fetch(url, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify(data)
    }).then((response) => {
        if(!response.ok){
            throw new Error('Something went wrong!')
        }
        return response.json();
    }).then((data) => {
        //assume the add successful
        //hide the modal
        //make sure the list is updated appropriately
    }).catch((e) => console.log(e));
}
```
# #########################################################################################
# Part.31 - DELETE Request with Fetch

We need now to match our front-end with our back-end, so we will create a delete button on our webpage that when clicked it will redirect back to the list of customers without the customer we just deleted, and this will also delete on the backend too.

- We had an error in the `Customers.js` so we did some re-arrangment and made the customers in an `<ul>..</ul>` and inside each `<li>` we added the key parameter.
```
return (
    <>
        <h1>Here are our customers:</h1>
        <ul>
            {customers ? customers.map((customer) => {
                return (
                <li key={customer.id}>
                    <Link to={"/customers/" + customer.id}>
                        {customer.name}
                    </Link>
                </li>
                );
            }) : null}
        </ul>
    </>
);
```
- We also added the delete `<button>...</button>` inside `Customer.js`.
- We also added the delete functionality too.
- We also added that we are making a json request with using the key `headers:{...}` in `fetch()`.
```
return (
    <>
    {notFound ? <p> The customer with id {id} was not found</p> : null}
        {customer ? 
        <div>
            <p>{customer.id}</p>
            <p>{customer.name}</p>
            <p>{customer.industry}</p>
        </div> : null}
        <button onClick={() => {
            const url = baseUrl + '/api/customers/' + id;
            fetch(url, {
                method: 'DELETE', 
                headers: {
                    'Content-Type': 'application/json'
            },
        })
            .then((response) => {
                if(!response.ok){
                    throw new Error('Something went wrong')
                }
                navigate('/customers');
            })
            .catch((e) => console.log(e));
        }}>Delete</button>
        <br/>
        <Link to="/customers">Go Back</Link>
    </>
)
```
# #########################################################################################
# Part.30 - Code a Full CRUD API (Create, Read, Update, Delete)

- There is a better suggested way to do the `Http404` and `JsonResponse` in our `views.py` which is
  - First we will need to do these imports inside the `views.py`:
  ```
  from rest_framework.decorators import api_view
  from rest_framework.response import Response
  from rest_framework import status
  ```
  - We can use the `Response` we just imported to do all of our different responses whether it is a `JsonResponse` or a `404` or even an `html`.
  - The `api_view` says which methods are allowed.
  - The `status` will give us bunch of options for status codes.
  - Then, above our `def customer()` function, we will define an API that can take `["GET", "POST", "DELETE"]` requests, within an API decorator `@api_view()` that just describes functionality for this function: 
  ```
  @api_view(['GET', 'POST', 'DELETE'])
  ```
  - We changed the `def customer()` function in `views.py`:
  ```
  def customer(request, id):
    try:
        data = Customer.objects.get(pk=id)
    except Customer.DoesNotExist:
        return Response(status=status.HTTP_404_NOT_FOUND)
    serializer = CustomerSerializer(data)
    return Response({'customer': serializer.data})
  ```
- You will see the difference when you visit `http://localhost:8000/api/customers/3`.
- We will see that we have different buttons such as `DELETE`, `OPTIONS`, `GET`, `POST`, however, we still didn't implement their functionality.
- We already have `GET` method implemented, we just have to put it under a suitable if statement, and we will implement the `DELETE` method:
```
@api_view(['GET', 'POST', 'DELETE'])
def customer(request, id):
    try:
        data = Customer.objects.get(pk=id)
    except Customer.DoesNotExist:
        return Response(status=status.HTTP_404_NOT_FOUND)
    
    if request.method == 'GET':        
        serializer = CustomerSerializer(data)
        return Response({'customer': serializer.data})
    elif request.method == 'DELETE':
        data.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```
- We also changed the `def customers()` function in `views.py`:
```
@api_view(['GET', 'POST'])
def customers(request):
    data = Customer.objects.all()
    serializer = CustomerSerializer(data, many=True)
    return Response({'customers': serializer.data})
```
- We will implement the `POST` method, which is used to edit/add the data of a specific customer:
```
@api_view(['GET', 'POST', 'DELETE'])
def customer(request, id):
    try:
        data = Customer.objects.get(pk=id)
    except Customer.DoesNotExist:
        return Response(status=status.HTTP_404_NOT_FOUND)
    
    if request.method == 'GET':        
        serializer = CustomerSerializer(data)
        return Response({'customer': serializer.data})
    elif request.method == 'DELETE':
        data.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
    elif request.method == 'POST':
        serializer = CustomerSerializer(data, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response({'customer': serializer.data})
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```
- Adding a new element to the list will refer to `def customers()` function since it is the list of our customers, and we need to implement the `POST` method for it:
```
@api_view(['GET', 'POST'])
def customers(request):
    if request.method == 'GET': 
        data = Customer.objects.all()        
        serializer = CustomerSerializer(data, many=True)
        return Response({'customers': serializer.data})
    elif request.method == 'POST':
        serializer = CustomerSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response({'customer': serializer.data}, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```
# #########################################################################################
# Part.29 - Return 404 From Backend API (Django)

- We will check for a `404` in the `Customer.js`
- Firstly we need to edit the backend in the `views.py` file: (we'll try to get the data, if we fail, we'll go the django `Http404` with a message `Customer Does not exist`)
```
def customer(request, id):
    try:
        data = Customer.objects.get(pk=id)
    except Customer.DoesNotExist:
        raise Http404('Customer Does not exist')
    serializer = CustomerSerializer(data)
    return JsonResponse({'customer': serializer.data})
```
- We have 2 options to deal with the `404` error:
1. Redirect to a 404 page (new URL): (in `Customer.js`)
```
.then((response) => {
    if(response.status === 404){
        //redirect to a 404 page (new URL)
        navigate('/404');
        //render a 404 component in this page
    }
    response.json()
})
```
2. Render a 404 component in this page: (in `Customer.js`)
```
const [notFound, setNotFound] = useState();
```
in the `.then(response)`:
```
if(response.status === 404){
    //render a 404 component in this page
    setNotFound(true);
}
return response.json()
```
in the function `return(<>...</>)`:
```
{notFound ? <NotFound/> : null}
```
or
```
{notFound ? <p> The customer with id {id} was not found</p> : null}
```
- Now, we want to re-arrange our urls so that we don't have duplicate urls throughout our application.
  - inside the `src` folder we'll create a file named `shared.js`:
  ```
  export const baseUrl = 'http://localhost:8000/';
  ```
  - inside `Customer.js` we change the `const url` to:
  ```
  const url = baseUrl + 'api/customers/' + id;
  ```
  - inside `Customers.js` we change the url to:
  ```
  fetch(baseUrl + 'api/customers/')
  ```
# #########################################################################################
# Part.28 - Create a Details Page

- We are going to create a new page for an individual customer, in the `pages` folder, and we'll name it `Customer.js`, and it will be kind of similar to the `Definition.js` page, because in the `Definition.js` we passed in a search term in the url, and used it to get details on a specific element:
```
const url = 'https://api.dictionaryapi.dev/api/v2/entries/en/' + search;
```
- our `Customer.js`:
```
import { useParams } from "react-router-dom"

export default function Customer(){
    const { id } = useParams();
    
    return(
        <>
            <p>{id}</p>
        </>
    )
}
```
- We need to deal with the routing now in our `App.js`: (added the `<Route/>` below)
```
<Route path='/customers/:id' element={<Customer/>} />
```
- Now, when we visit the page we will see the `id` only displayed on the page, so what we should do now is to take this `id` and make a request to the appropriate API endpoint and we can do this by appending the `id` to the `url` for the request. To do this we need it to happen immediately, one time so we are going to use the `useEffect()` hook in `Customer.js`:
```
import { useEffect, useState } from "react";
import { Link, useParams } from "react-router-dom"

export default function Customer(){
    const { id } = useParams();
    const [customer, setCustomer] = useState();

    useEffect(() => {
        const url = 'http://localhost:8000/api/customers/' + id;
        fetch(url)
        .then((response) => response.json())
        .then((data) => {
            setCustomer(data.customer);
        });
    }, []);
    return (
        <>
            {customer ? 
            <div>
                <p>{customer.id}</p>
                <p>{customer.name}</p>
                <p>{customer.industry}</p>
            </div> : null}
            <Link to="/customers">Go Back</Link>
        </>
    )
}
```
# #########################################################################################
# Part.27 - Create a Details by ID API

We want to be able to get a customer first by identifying the id in the link `http://localhost:8000/api/customers/`, so when we insert an url parameter like `http://localhost:8000/api/customers/1` we want to get the customer with ID = 1.

To do so we will need to modify 2 files:
1. First the `urls.py`: (we added the last path, and to parameterize the url we did `<int:id>`)
```
from django.contrib import admin
from django.urls import path
from customers import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/customers/', views.customers, name='customers'),
    path('api/customers/<int:id>', views.customer, name='customer')
]
```
2. Second the `views.py`: (we added the single customer function, that would take the `<int:id>` in the `urls.py` as a parameter, and return a single `customer`)
```
from customers.models import Customer
from django.http import JsonResponse
from customers.serializers import CustomerSerializer

def customers(request):
    data = Customer.objects.all()
    serializer = CustomerSerializer(data, many=True)
    return JsonResponse({'customers': serializer.data})

def customer(request, id):
    data = Customer.objects.get(pk=id)
    serializer = CustomerSerializer(data)
    return JsonResponse({'customer': serializer.data})
```
- Now, we can make requests to this endpoint and substitute some value whatever we want on the front-end.
- Now in the `Customers.js` in our React application, we can link it.
- We made some changes to the return in the `customers.js`: (upon clicking any of our customers it will redirect us to `"/customers/" + customer.id` which is not yet implemented)
```
return (
    <>
        <h1>Here are our customers:</h1>
        {customers ? customers.map((customer) => {
            return (
            <p>
                <Link to={"/customers/" + customer.id}>{customer.name}</Link>
            </p>
            );
        }) : null}
    </>
);
```
# #########################################################################################
# Part.26 - Consume Backend API

What we will do:
1. How to put data in our database.
2. Get that data to show up in our API.
3. How to request for that data in our React Application.

We created a new file inside `customers` folder and named it `admin.py`. This file we can describe an admin site that allows us to have CRUD access to the database without too much extra effort. 

- So if we type in the browser `http://localhost:8000/admin` it will take us into an admin login page.
- We need now to create an account from the terminal, by typing: `py manage.py createsuperuser`
- We will then fill the username, email-address, password, confirm password.
- go back to the `http://localhost:8000/admin` and log-in using the just created credentials.
- We want the `admin.py` to configure the admin page by showing our table on the `http://localhost:8000/admin`
- Our `admin.py` will look like:
```
from django.contrib import admin
from customers.models import Customer

admin.site.register(Customer) 
```
- We can access the Customers table from the `http://localhost:8000/admin` and we will see an add customer button, we then can add customers (we'll need to fill in their name and industry and then click save) to our customers table.
- After adding an amount of customers we can view our API by typing `http://localhost:8000/api/customers/` into our browser.

- Now we need to consume our API from our React Application (it will be something similar to `Definition.js` in our React application)
- We'll use a `useEffect()` hook with `fetch()` inside in our `Customers.js`, and we have it set up in the `App.js` that when someone visits `/customers` it will render the `<Customers/>` component.
- Our `Customers.js` will look like:
```
import { useEffect, useState } from "react";

export default function Customers(){
    const [customers, setCustomers] = useState();

    useEffect(() => {
        fetch('http://localhost:8000/api/customers/')
        .then((response) => response.json())
        .then((data) => {
            console.log(data);
            setCustomers(data.customers);
        });
    }, []);
    return (
        <>
            <h1>Here are our customers:</h1>
            {customers ? customers.map((customer) => {
                return <p>{customer.name}</p>;
            }) : null}
        </>
    );
}
```
- This won't work because the fetch request comes from an origin that is not explicitly allowed, to fix it we need to install a package to configure cors.
- So in our backend code, we need to type in the command: `pip install django-cors-headers`, then we'll need to type `pip freeze > requirements.txt`.
- We'll then need to add in the `settings.py` in the `INSTALLED_APPS=[...]` array `"corsheaders",`
- We'll also need to add in the `settings.py` in the `MIDDLEWARE=[...]` array:
```
`corsheaders.middleware.CorsMiddleware`,
`django.middleware.common.CommonMiddleware`,
```
- Then we'll need to create an array in `settings.py`:
```
CORS_ALLOWED_ORIGINS = ['http://localhost:3000']
```
- We'll need to restart the django server by exiting and `py manage.py runserver` again.

# #########################################################################################
# Part.25 - Create a REST API Backend

- What we want to do now is to open it in VSC, and get it up in version control (github).
- To open it we can type in the command `Code .`
- To get it up in version control:
  - `git init`
  - `touch .gitignore` or `echo. > .gitignore`
  - (search on google gitignore for django (toptal is recommended)) - copy and paste the code in `.gitignore` code in your project.
  - `git add .`
  - `git commit -m "initial project"`
  - We created a new repository on github named `react-backend-django`
  - `git remote add origin https://github.com/HashimAlHasani/react-backend-django.git`
  - `git branch -M main`
  - `git push -u origin main`

- Note: the link for the backend repository is - `https://github.com/HashimAlHasani/react-backend-django.git`

- We want now to install the framework by typing: `pip install django-rest-framework`
- We can also upgrade our pip if needed by typing: `python.exe -m pip install --upgrade pip`

- There is no changes in our VSC source control, so we have to create a file manually in order to keep an updated list.
  - `pip freeze > requirements.txt` - this is the file name that will keep track on what packages we have installed
  - `pip install -r requirements.txt` - if we want to use this project later we will do the equivalent of `npm install`.
  - The command above is going to go throughth the `requirements.txt` and install every single package.
  - Friendly reminder, whenever you update your packages that you have installed you want to add to the `requirements.txt` by issuing the `freeze` command again.

- REST API: is basically an agreed way of transferring data between applications. This will allow us to use (CRUD), and it will be going in a JSON format.

- Now we are going to create a:
  - Model: This is going to be representation of data. (another word for database table)
  - URL Path: This is the location to access this data.
  - Serializer: This is going to describe how we go from database object to JSON data.

- We typed `py manage.py migrate` on the command, so when we create a model we will need to add that migration aswell.

- We created a new file in `customers` called `models.py` that will describe any model that we need for this application.

- inside `models.py` we wrote:
```
from django.db import models

class Customer(models.Model):
  name = models.CharField(max_length=200)
  industry = models.CharField(max_length=100)
```
- Note make sure that there is an indent as it is important in python.

- Then we need to make sure to add in the `settings.py` file, in the `INSTALLED_APPS = [...]` array, add `customers` to the array.
- We then need to `python manage.py makemigrations customers` in our command.
- We will then get a new `migrations` folder inside it a file called `0001_initial.py` that describes the changes to the database.
- Next we apply this migration to actually make this change to the database.
- So we can type in the command `py manage.py migrate`, this will create the change on the database.

- Now to create the URL Path mentioned above, we are going to open the file `urls.py`, we add some code to this file in order to have a url path to our customers folder:
```
from django.contrib import admin
from django.urls import path
from customers import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/customers/', views.customers, name='customers')
]
```
- We are going to create a file in the customers folder called `views.py`:
```
def customers(request):
  #invoke serializer and return to client
  pass
```
- We will go back to the `customers()` function in the `views.py` file later.

- We will need to add in the `settings.py` file, in the `INSTALLED_APPS = [...]` array, add `rest_framework` to the array.

- We created a new file inside the customers folder called `serializers.py`:
```
from rest_framework import serializers
from customers.models import Customer

class CustomerSerializer(serializers.ModelSerializer):
  class Meta:
    model = Customer
    fields = '__all__'
```
- Now we just have to use the serializer in the `views.py`:
```
from customers.models import Customer
from django.http import JsonResponse
from customers.serializers import CustomerSerializer

def customers(request):
  data = Customer.objects.all()
  serializer = CustomerSerializer(data, many=True)
  return JsonResponse({'customers': serializer.data})
```
- So what we are doing above is that we are taking the data and pass it through the serializer and then we use `serializer.data` to get the serialized version.

- In order to view our database we need to pass in `/api/customers/` to our `localhost:8000` url, so open on the browser:
```
http://localhost:8000/api/customers/
```
# #########################################################################################
# Part.24 - Setup a Django Backend (Full Stack App)

- The backend is responsible for storing information in a database, which is how our application remembers.

- Connecting to the backend from the front-end is going to be similar across all backend technologies.

- The interface is usually built using a `REST API`, which is a great way of communication which has the ability to: (CRUD)
1. Create infromation
2. Read infromation
3. Update infromation
4. Delete infromation

- The CRUD can be done no matter what backend software (NodeJS, ASP.NET, etc.) we are writing.

- I downloaded python from `python.org`. We are going to use some python in order to code for our backend `Django`

- We created a new directory called `backend`

- We wrote this command inside our `/backend` directory: 
- `python3 -m venv .venv`, this will create a virtual environment called `.venv`
- If the command above produced an error you can also use `py -3.12 -m venv .venv`. Be sure to check your `py --version`
- Then we need to activate by typing: `.venv\Scripts\activate`. We will `(.venv)` at the beginning of the line.

- The virtual environment is the lense we look at our project through and it will tell us what packages we have installed and what versions to use for our application, so typically we will have a virtual environment for every project we build in python.

- Note: we have to `.venv\Scripts\activate` (activate it) before we install packages.

- To install packages we type into the command: `python -m pip install django`. Django is the framework that we are going to us to build our backend.

- After installing we will have the command `django-admin` available to us.
- It will list a lot of things but we are interested in `startproject` so we will type into the command: `django-admin startproject customers .`
- If we list our files now using `dir` we will see `customers` and `manage.py`
- For the rest of our commands we are going to use the `manage.py` file. So we are going to type in: `py manage.py runserver`
- we can now see this printed on our cmd: `... http://127.0.0.1:8000/`, so we can now visit `localhost:8000` on our web-browser.

# #########################################################################################
# Part.23 - Build and Style a Search Component

- In `Dictionary.js` we removed the `{ replace: true }`, as we don't want to replace the history spot for us.
- We also changed the `return` of the `defautl function Dictionary()` in order to make an `Enter` key-stroke press our button:
```
return (
    <form onSubmit={() => {
        navigate('/definition/' + word);
    }}>
        <input type="text" onChange={(e) => {
            setWord(e.target.value);
        }}/>
        <button>Search</button>
    </form>
)
```
- We added the following CSS styling to our `<form>`/`<input>`/`<button>`
```
return (
    <form className="flex space-between space-x-2 max-w-[300px]"
    onSubmit={() => {
        navigate('/definition/' + word);
    }}>
        <input
        className="shrink min-w-0 px-2 py-1 rounded"
        placeholder="Dinosaur"
        type="text" onChange={(e) => {
            setWord(e.target.value);
        }}/>
        <button
        className="bg-purple-600 hover:bg-purple-700 text-white font-bold py-1 px-2 rounded">Search</button>
    </form>
)
```
- We also created a new component file called `DefinitionSearch.js`, we will copy all of the cut all the code of `Dictionary.js` and paste it inside the newly created component `DefinitonSearch.js` and our `Dictionary.js` will look like this:
```
import DefinitionSearch from "../components/DefinitionSearch";

export default function Dictionary(){
    return (
        <div className="flex justify-center">
            <DefinitionSearch/>
        </div>
    );
}
```
- We did the step above in order to make the search a component that we can reuse, for example, if we want the user to search for another definiton inside the `Definition.js` page. We can achieve this by adding in our `default function Definition()` return:
```
<p>Search again:</p>
<DefinitionSearch/>
```
# #########################################################################################
# Part.22 - Fetch Response Status Codes and Errors

- There are other Status codes other than `404`:
1. Informational responses (`100` - `199`)
2. Successful respones (`200` - `299`)
3. Redirection messages (`300` - `399`)
4. Client error responses (`400` - `499`)
5. Server error responses (`500` - `599`)

- We changed the `fetch` method we had and added a `throw and catch` method to it as well to handle all types of error and Status codes:
```
const url = 'https://api.dictionaryapi.dev/api/v2/entries/en/' + search;

fetch(url)
  .then((response) => {
      //console.log(response.status);
      if (response.status === 404) {
          setNotFound(true);
      } else if (response.status === 401) {
          navigate('/login')
      } else if (response.status === 500) {
          setError(true);
      }

      if(!response.ok){
          setError(true);

          throw new Error('Something went wrong');
      }
      return response.json();
  })
  .then((data) => {
      setWord(data[0].meanings);
  })
  .catch((e) => {
      console.log(e.message);
});
```
- We also added an `if` statement incase an error occured that is was not specially handled as above:
```
if (error === true) {
    return (
        <>
            <p>Something went wrong, try again?</p>
            <Link to="/dictionary">Search another</Link>
        </>
    );
}
```
# #########################################################################################
# Part.21 - Create 404 (Not Found) Page

- Firstly we need to check for a redirect, and we can do so in our `fetch` method in `Definition.js`.
```
useEffect(() => {
  fetch('https://api.dictionaryapi.dev/api/v2/entries/en/' + search)
    .then((response) => {
      if(response.status === 404){
        console.log(response.status);
      }
    return response.json();
    })
    .then((data) => {
      setWord(data[0].meanings);
    });
}, []);
```
- We check above if the status of our response in our `fetch` is equal to `404` which is the same as the `404 error page not found`.

- What we actually want to do if a `404` error occurs is to redirct the user of this page to a different page, and we can do so by doing the following:
```
useEffect(() => {
  fetch('https://api.dictionaryapi.dev/api/v2/entries/en/' + search)
    .then((response) => {
        if(response.status === 404){
          navigate('/404');
        }
        return response.json()
    })
    .then((data) => {
      setWord(data[0].meanings);
    });
    }, []);
```
- We also changed the return of our `default function Definition()` to:
```    
return (
  <>
    {word ? (
      <>
        <h1>Here is a definition:</h1>
        {word.map((meaning) => {
          return (
            <p key={uuidv4()}>
              {meaning.partOfSpeech + ': '}
              {meaning.definitions[0].definition}
            </p>
          );
        })} 
      </>
    ) : null}
  </>
);
```
- We did the changes to the return above in order to prevent having the `<h1>..</h1>` show on screen when we get redirected to `/404`.

- We created a new component called `NotFound.js`:
```
export default function NotFound(){
    return <p> The page you are looking for was not found</p>
}
```
- We also added in `App.js` 2 extra routes: `<Route path='/404' element={<NotFound/>} />` and `<Route path='*' element={<NotFound/>} />`, the second route is if we manually entered a path that doesn't exist it will direct to `<NotFound/>` component.

- If we want the error url to stay and not show a `/404` path we can do the following in `Definition.js`:
```
const [notFound, setNotFound] = useState(false);
```
- and:
```
if(response.status === 404){
  setNotFound(true);
}
```
- and before the `default function Definition()` return:
```
if(notFound === true) {
        return <NotFound/>;
    }
```
- We added a link button on the NotFound component inside `Definition.js` that will get us back to dictionary when we click it:
```
if(notFound === true) {
  return (
    <>
      <NotFound/>
      <Link to='/dictionary'>Search Another</Link>
    </>
  );
}
```
# #########################################################################################
# Part.20 - Redirect with useNavigate Hook

- We will use another hook called `useNavigate`.

- `useNavigate`: It allows us to force the user to visit a new page on our application, however, it is different than a link; a link the user actually has to click, whereas `useNavigate` we can put this inside event handlers.

- We are going to create a search bar, user types something in, slaps the search button and force them to go to a new page.

- We removed the Route `<Route path='/definition' element={<Definition/>} />`, and this means that we are required to pass in data.

- We will make the `<Route path='/dictionary' element={<Dictionary/>} />` page directs us to the `<Route path='/definition/:search' element={<Definition/>} />` page.

- We firstly modified the `Dictionary.js` to look like this:
```
import { useState, useEffect } from "react";

export default function Dictionary(){
    const [word, setWord] = useState('');

    return (
        <>
            <input type="text" onChange={(e) => {
                setWord(e.target.value);
            }}/>
        </>
    )
}
```
- We added after the `<input>...</input>` an html `<button>...</button>`:
```
<button onClick={() => {
  console.log('click');
}}>Search</button>
```
- We might need to install react router dom if we don't already have it by simply typing in the command:
```
npm install react-router-dom
```
- We need to assign `useNavigate()` to a variable: `const navigate = useNavigate();` and then pass it onto the onClick method:
```
<button onClick={() => {
  navigate('/definition/' + word);
}}>Search</button>
```
- Adding this `navigate('/definition/' + word, { replace: true });`, focus on `{replace: true}` as it will manage how the history of the application works by replacing our current spot in the history

# #########################################################################################
# Part.19 - URL Parameters in Router

- We will use another hook called `useParams`.

- `useParams`: This is used to pass parameters through the URL.

Now in order to use it we are going to do the following:
1. Import and assign it to a variable like this in `Definition.js`: `let {search} = useParams();`
2. We will also append this variable to the API url we have in our `fetch` in `Definition.js`:
```
fetch('https://api.dictionaryapi.dev/api/v2/entries/en/' + search)
```
3. We are going copy and paste the definition Route and add to the new one `/:search` in `App.js`:
```
<Route path='/definition' element={<Definition/>} />
<Route path='/definition/:search' element={<Definition/>} />
```
# #########################################################################################
# Part.18 - Fetch an API to Display on Page

- We moved the `Dictionary.js` to the pages folder.

- We created a new page called `Definition.js`.

- Often times for very good API we will need a key (gives us access). 

- To make the API secure we need to use the API on the backend of an application and then connect to the backend.

- We are going to use a free API from the internet that will not require a key.

- We will make a request to the API using `fetch`, and the syntax for fetch:
```
fetch('')
  .then((response) => response.json())
  .then((date)=>console.log(date));
```
- We put the link to the API inside the brackets `fetch('API URL')...`.

- Our API link that we are going to use is `https://api.dictionaryapi.dev/api/v2/entries/en/hello` which we got from `https://dictionaryapi.dev/`
```
useEffect(() => {
  fetch('https://api.dictionaryapi.dev/api/v2/entries/en/hello')
    .then((response) => response.json())
    .then((data) => {
      setWord(data[0].meanings);
      console.log(data[0].meanings);
    });
}, []);
```
- We traversed the `data` array in order to get what we want, hence, the meanings only.

- We can return and output the definition on the screen by doing the following:
```
return (
  <>
    <h1>Here is a definition:</h1>
    {word ? word.map((meaning) => {
      return (
        <p key={uuidv4()}>
          {meaning.partOfSpeech + ': '}
          {meaning.definitions[0].definition}
        </p>
      )
    }): null}
  </>
)
```
- What we basically did above is that we returned a code block `<>..</>` and inside it we have a ternary operator that will check if a `word` is available, if it is available we will `map` through `word` and pass a callback function to `map` that will return the definition as we traverse the parameter `meaning`, if it is not available we will just pass in `null`.

# #########################################################################################
# Part.17 - useEffect Dependency Array Explained

- Dependency Array: It allows us to restrict what state we care about for `useEffect` to be triggered.

- Dependency array is the second argument that can be passed into `useEffect()`.

- This is without having the second argument: (it just tells us that the state is updated, and then displays the state variable)
```
useEffect(() => {
  console.log("State Updated", word)
});
```
- There are three examples to study for what we pass on as the second argument for the `useEffect()`:
1. No Dependency array --> update for any state change
```
useEffect(() => {
  console.log("State Updated", word + ' ' +  word2);
});
```
2. Empty Dependency array --> execute once
```
useEffect(() => {
  console.log("State Updated", word + ' ' +  word2);
}, []);
```
3. Passing in data --> only execute when those state variables are changed
```
useEffect(() => {
  console.log("State Updated", word + ' ' +  word2);
}, [word]);
```
- For `3.` the state variable `word2` will only execute once, however, since `word` is passed in the array it will update for any state change.

# #########################################################################################
# Part.16 - Intro to useEffect Hook

`useEffect`: This is something that happens when you change something else. So, when you changed the state of the application we have side-effects that are executed and to do that we use the `useEffect` hook.

Mutations, subscriptions, timers, logging and other side effects are not allowed inside the main body of a funciton component (referred to as React's render phase). Doing so will elad to confusing bugs and inconsistencies in the UI.

Instead, use `useEffect`. The function passed to useEffect will run after the render is committed to the screen. Think of effects as an escape hatch from React's purely functional world into the imperative world.

By default, effects run after every completed render, but you can choose to fire them only when certain values have changed.

- Note: `<React.StrictMode>..</React.StrictMode>` will render our components twice, and we are using it in `index.js`.
- Note: `setWord()` is asynchronous and it is not guaranteed to have that value immediately after.
- Note: on the other hand `useEFfect()` will depend on all of the state currently and we can be sure that the state is updated as this executes after the state is updated
- Note: make sure to define the `useEffect()` inside the `{ }` of our default function.
- Note: make sure to define it after any state, and this has to do with the dependencies as well.

- We created a new component called `Dictionary.js`, it is irrelevant to the employees/customers stuff.
```
import { useState } from "react";

export default function Dictionary(){
    const [word, setWord] = useState('');

    return (
        <>
            <input type="text" onChange={(e) => {
                setWord(e.target.value);
            }}/>
            <h1>Let's get the definition for {word}</h1>
        </>
    )
}
```
- So what the code above basically do for now is just take an input and dynamically add it to the sentence in `<h1>..</h1>` tag.

- `useEffect()` takes two parameters:
1. A callback function (we use an arrow function and code our function inside it):
```
useEffect(() => {
  console.log("State Updated", word)
});
```
2. Dependency array - Will be discussed later on.

# #########################################################################################
# Part.15 - Finishing up Our Header

In this part we are just doing some clean up, fixing some minor tailwind css issues, check for both mobile and desktop css compatibality, remove irrelevant code and etc.
- We moved the `{props.children}` to outside the `<Disclosure>..</Disclosure>`, but we surrounded everything with a `<>..</>`, so after the return `<>` and after the `{props.children}` we `</>`.
- We also moved the styling from `Employee.js` to the `Header.js` -> `<div className='bg-gray-300 min-h-screen'>{props.children}</div>`.
- We did some more tailwind css styling down below: (this will center the website nicely when zoomed out and set the bg correctly) 
```
<div className='bg-gray-300'>
    <div className='max-w-7xl mx-auto min-h-screen px-2 py-2'>{props.children}</div>
</div>
```
# #########################################################################################
# Part.14 - Create an Active Page Link in Navbar

There are better react components that we can use for links than `<a>..</a>`.
- We will replace the `<a>..</a>` with `<NavLink>..</NavLink>`.
- Instead of `href={item.href}` we changed it to `to={item.href}`.
- We changed the className to:
```
className={({isActive}) => {
  return `px-3 py-2 rounded-md text-sm font-medium no-underline ` + 
  (isActive ? ' text-white bg-gray-900 ' : `hover:text-white text-gray-300 hover:bg-gray-700 `);
}}
```
- The code above will check which NavLink is active and will then go to the ternary operation and highlight the background if `isActive` returns true. (Note leave a single space before the end of the string so that the css gets passed correctly without conflict)

# #########################################################################################
# Part.13 - Routing with React Router

- We need to install react router dom by typing in the command:
```
npm install react-router-dom
```
- This will require us to import 3 things into our application in `App.js`:
```
import {BrowserRouter, Routes, Route} from 'react-router-dom';
```
- And the structure to create a url path is as follows:
```
function App() {
  return (
  <BrowserRouter>
    <Header>
      <Routes>
        <Route path='/employees' element={<Employees/>} />
      </Routes>
    </Header>
  </BrowserRouter>
  );
}
```
- We created another page just to show how this might work to visit a separate page. The new `Customers.js` page looks like:
```
export default function Customers(){
    return <h1>Hello There!</h1>;
}
```
- Now we can add another `<Route/>` like so:
```
function App() {
  return (
  <BrowserRouter>
    <Header>
      <Routes>
        <Route path='/employees' element={<Employees/>} />
        <Route path='/customers' element={<Customers/>} />
      </Routes>
    </Header>
  </BrowserRouter>
  );
}
```
Then we can change the `href` in `Header.js` to the following:
```
const navigation = [
  { name: 'Employees', href: '/Employees', current: true },
  { name: 'Customers', href: '/Customers', current: false },
  { name: 'Projects', href: '#', current: false },
  { name: 'Calendar', href: '#', current: false },
]
```
# #########################################################################################
# Part.12 - Pages and props.children

- We created a new folder in `src` called `pages`. Inside the `pages` folder we created a new file called `Employees.js`.
- We copied all of the code in `App.js` to the `Employees.js` page, and deleted any unused variables/import/code.
- We made also the directory of the imports to look one directory up by doing `../`:
```
import '../index.css';
import Employee from '../components/Employee';
import { useState } from 'react';
import {v4 as uuidv4} from 'uuid';
import AddEmployee from '../components/AddEmployee';
import EditEmployee from '../components/EditEmployee';
import Header from '../components/Header';
```
- Now our `App.js` looks like this:
```
import './index.css';
import Employees from './pages/Employees';

function App() {
  return <Employees/>
}

export default App;

```
- Using pages will make our code more organized and we can better understand it.
- To make sure that the `<Header/>` component persists throughout all of the pages we can delete it from `Employees.js` page and do the following in `App.js`:
```
function App() {
  return (
  <Header>
    <Employees/>
  </Header>
  );
}
```
- However, we are still missing one more thing to make everything to work and appear as intended:
    - In `Header.js` add props as a parameter `export default function Header(props) {...}`.
    - Before the end of the `Header` function add `{props.children}`. (before the `</>`)

# #########################################################################################
# Part.11 - Create a Navbar with Tailwind CSS

We will get the Navbar from this website:
```
https://tailwindui.com/components/application-ui/navigation/navbars
```
We chose the first Navbar (Be sure to copy the code in react by selecting react in the drop down menu) because it is free.

We added a new file in the components folder called `Header.js` and pasted the header code we just copied.

Also be sure to change the function name from `Example` to `Header`

Now, in `App.js` we add the `<Header/>` component at the top.
```
<div className="App">
    <Header/>
```
As we can see we might have an error because we need to install some modules in our command such as:
1. `npm install @headlessui/react`
2. `npm install @heroicons/react`

We can do some css editing to the `className` attribute that we have in `Header.js`, maybe change some images or such.

# #########################################################################################
# Part.10 - Pass a Component to Props

What we have right now is that we are passing the `updateEmployee` function from:
1. `App.js`:
```
return (
    <Employee 
    key={employee.id}
    id={employee.id}
    name={employee.name} 
    role={employee.role} 
    img={employee.img}
    updateEmployee={updateEmployee}
    />
);
```
To:
2. `Employee.js`:
```
<EditEmployee 
    id={props.id}
    name={props.name} 
    role={props.role} 
    updateEmployee={props.updateEmployee}
/>
```
To:
3. `EditEmployee.js`:
```
<form onSubmit={(e)=>{
    handleClose();
    e.preventDefault();
    props.updateEmployee(props.id, name, role)
}}
```
So what we want to do instead is we want to define the `EditEmployee` component inside of `App.js` and pass that to `Employee`.
1. In `App.js`:
```
<div className='flex flex-wrap justify-center'>
          {employees.map((employee) => {
            const editEmployee = (
            <EditEmployee 
              id={employee.id}
              name={employee.name} 
              role={employee.role} 
              updateEmployee={updateEmployee}
            />)
            ;
            return (
              <Employee 
                key={employee.id}
                id={employee.id}
                name={employee.name} 
                role={employee.role} 
                img={employee.img}
                editEmployee={editEmployee}
              />
            );
          })}
      </div>
```
2. In `Employee.js` We replaced:
```
<EditEmployee 
    id={props.id}
    name={props.name} 
    role={props.role} 
    updateEmployee={props.updateEmployee}
/>
```
with:
```
{props.editEmployee}
```
# #########################################################################################
# Part.9 - How to Push to State Array

1. In the Components folder we created a new file called `AddEmployee.js`, we copied and pasted `EditEmployee.js` as the code might be a bit similar.
2. Don't forget to change the function name and the export at the button of the code to `AddEmployee`.
3. We added `<AddEmployee/>` in the `App.js` file.
4. We added another input for the image url, and placed the attribute `placeholder=""` for all the inputs.
5. We created a function `newEmployee` in `App.js`:
```
  function newEmployee(name, role, img){
    const newEmployee = {
      id: uuidv4(),
      name: name,
      role: role,
      img: img,
    }
    setEmployees([...employees, newEmployee]);
  }
```
6. We need to also need to pass this function in the `<AddEmployee/>` as a prop:
```
<AddEmployee newEmployee={newEmployee}/>
```
7. We also add a `handleClose` on the add `button` in `AddEmployee.js`:
```
onClick={handleClose}
```
8. After we add the first Employee, and reclick the `+ Add Employee` `button`, the previous added employee data is now acting as a `placeholder` and we don't want to do that. To fix this issue we can do the follow:
```
<form onSubmit={(e)=>{
    e.preventDefault();
    setName('');
    setRole('');
    setImg('');
    props.newEmployee(name, role, img)
}}
```
# #########################################################################################
# Part.8 - Create a Popup Modal with React Bootstrap

We need to install react bootstrap by using:
```
npm install react-bootstrap bootstrap
```
We also need to import bootstrap css into `index.js`:
```
import 'bootstrap/dist/css/bootstrap.min.css';
```

1. Learn how to create the modal with react bootstrap, how to open it and how to close it.
    - We created another component in the src/components folder called `EditEmployee.js`.
    - Then we copy our modal from the example modals given in react https://react-bootstrap.netlify.app/ and paste it into our `EditEmployee.js`.
    - We also edited the name of the functions so instead of `function Example()` we named it `function EditEmployee()` and we also `export default EditEmployee;` at the end of the `EditEmployee.js` file.
    - Then we call `<EditEmployee/>` in our `Employee.js`.
    - We also replaced the button in the copied modal in `EditEmployee.js` with the button that we already have in `Employee.js`, but we need to add the attribute `onClick={handleShow}`, as this is the function that will allow the modal to popup.
2. Styling and submitting with a button
    - We got our form that we will style the popup modal with from tailwind css:
        - `https://v1.tailwindcss.com/components/forms`
    - We changed some of the text that would show on the popup from the `EditEmployee.js` file.
    - We pasted our form inside the `<Modal.Body> </Modal.Body>` tag in `EditEmployee.js`.
    - Remember to change the keyword `class` to `className`.
    - We did some cutting in the form we copied in order to customize it to our requirements
    - We added our own button style and we got the style from: (We only took the inline style not all of the button code)
        - `https://v1.tailwindcss.com/components/buttons`
3. Passing props from an employee to the new modal component that will basically autofill the user data into that modal
    - So what we have done is that we set the value of the `value` attribute in the inputs of the form created in the `EditEmployee.js` to `value={props.name}` and `value={props.role}`.
    - We need to also set props as a parameter in the Edit Employee function:
        - `function EditEmployee(props) {...}`
    - Then we need to pass props when we call the `EditEmployee` in the `Employee.js` as follows:
        - `<EditEmployee name={props.name} role={props.role}/>`
    - We added 2 variables in `EditEmployee.js` in order to pass them through the `value` attribute:
        - `const [name, setName] = useState(props.name);` / `const [role, setRole] = useState(props.role);`
    - We pass the 2 variables we just added into the `value` attribute:
        - `value={name}` / `value={role}`
    - We added the `onChange` property to both the inputs and passed an arrow function as an argument that would use the `setName` and `setRole` in order to update the text boxes:
        - `onChange={(e) => {setName(e.target.value)}}`
        - `onChange={(e) => {setRole(e.target.value)}}`
4. Hitting that update button and having that data changed on the homepage of our application
    - We created a new function inside `App.js` that should handle the change of data:
        - `function updateEmployee(id, newName, newRole){}`
    - We also added a new property in the `<Employee/>` inside of `App.js`:
    ```
    <Employee 
        key={uuidv4()}
        name={employee.name} 
        role={employee.role} 
        img={employee.img}
        updateEmployee={updateEmployee}
    />
    ```
    - We also need to add it inside the `Employee.js`:
    ```
    <EditEmployee 
        name={props.name} 
        role={props.role} 
        updateEmployee={props.updateEmployee}
    />
    ```
    - We also need to add a function property in the `<form>...</form>` in the `EditEmployee.js` file:
    ```
    <form onSubmit={()=>{
        handleClose();
        props.updateEmployee(id, name, role)
    }} 
    ```
    - In order to prevent a page refresh we can also edit the function property:
    ```
    <form onSubmit={(e)=>{
        handleClose();
        e.preventDefault();
        props.updateEmployee(id, name, role)
    }}
    ```
    - We need to add the id in the:
    1. `App.js`:  (We removed the usage of guid)
    ```
    {
        id: 1,
        name: "Erwin Smith", 
        role: "Commander", 
        img: "https://images.pexels.com/photos/19554075/pexels-photo-19554075.jpeg",
    },
    ```
    ```
    <Employee
        key={employee.id}
        id={employee.id}
        name={employee.name} 
        role={employee.role} 
        img={employee.img}
        updateEmployee={updateEmployee}
    />
    ```
    2. `Employee.js`:
    ```
    <EditEmployee 
        id={props.id}
        name={props.name} 
        role={props.role} 
        updateEmployee={props.updateEmployee}
    />
    ```
    3. `EditEmployee.js`: (We don't need a state variable for the id so we use props.id)
    ```
    <form onSubmit={(e)=>{
        handleClose();
        e.preventDefault();
        props.updateEmployee(props.id, name, role)
    }}
    ```
    - The last thing we need to do is to actually code the updateEmployee Function that we created in the `App.js`:
    ```
    function updateEmployee(id, newName, newRole){
        const updatedEmployees = employees.map((employee) => {
        if(id == employee.id){
            return {...employee, name: newName, role: newRole};
        }

        return employee;
        });
        setEmployees(updatedEmployees);
    }
    ```
    - Breakdown of the function above:
        - ```const updateEmployess = employees.map()``` the map method will call the callback function for each employee in the `employees` array.
        - We then pass the arrow function as an argument `(employee) => {}`
        - We add the logic in the arrow function: (if the id match we return the new values, if not we return the normal values)
        - the `...employee` just means all of the employee elements (img, id, etc.).
        ```
        if(id == employee.id){
            return {...employee, name: newName, role: newRole};
        }

        return employee;
        ```
        - At the end, we need to use the `setEmployees(updatedEmployees)` with the `updatedEmployess` as an argument.
        
# #########################################################################################
# Part.7 - Map through State Array (Loop)

1. Take all of our data move it to the top in a single variable.
```
  const [employees, setEmployees] = useState(
    [
      {
        name: "Erwin Smith", 
        role: "Commander", 
        img: "https://images.pexels.com/photos/19554075/pexels-photo-19554075.jpeg",
      },
      {
        name: "Levi Ackerman", 
        role: "Commander", 
        img: "https://images.pexels.com/photos/19564637/pexels-photo-19564637/free-photo-of-levi.jpeg",
      },
      {
        name: "Eren Jaeger", 
        role: "Commander", 
        img: "https://images.pexels.com/photos/19564649/pexels-photo-19564649.jpeg",
      },
      {
        name: "Mikasa Ackerman", 
        role: "Survey Corps", 
        img: "https://images.pexels.com/photos/19564661/pexels-photo-19564661/free-photo-of-mikasa.jpeg",
      },
      {
        name: "Armin Arlert", 
        role: "Survey Corps", 
        img: "https://images.pexels.com/photos/19564668/pexels-photo-19564668/free-photo-of-armin.jpeg",
      },
      {
        name: "Hange  Zoë", 
        role: "Survey Corps", 
        img: "https://images.pexels.com/photos/19564675/pexels-photo-19564675/free-photo-of-hange.jpeg",
      },
    ]
  );
```
2. Replace displaying those components with a loop using map.
```
<div className='flex flex-wrap justify-center'>
    {employees.map((employee) => {
        return (
            <Employee 
            name={employee.name} 
            role={employee.role} 
            img={employee.img}
            />
        );
    })}
</div>
```
We might need to have an id to each component in order to differentiate or might be usefull in the future,
to do so we need to use something called a "guid", in order to use it we need to install:
```
npm install uuid
```
Then we need to import it by writing:
```
import {v4 as uuidv4} from 'uuid';
```
Then to use it we can do:
```
<Employee 
    key={uuidv4()}
    name={employee.name} 
    role={employee.role} 
    img={employee.img}
/>
```
# #########################################################################################
# Part.6 - More Tailwind CSS

We are going to use some already written design from tailwind.css by opening it and going into docs, then choosing Utility-First Fundamentals, and getting the code for our chosen design, now our Employee.js (Employee component) code looks like this:
Note: it should always be named `className` instead of `class` in react!
```
function Employee(props){
    return (
        <div className="m-2 py-8 px-8 max-w-sm bg-white rounded-xl shadow-lg space-y-2 sm:py-4 sm:flex sm:items-center sm:space-y-0 sm:space-x-6">
            <img 
            className="object-cover rounded-full h-[100px] w-[100px] block mx-auto h-24 rounded-full sm:mx-0 sm:shrink-0" 
            src={props.img}
            alt="Commander Erwin Smith" 
            />
            <div className="text-center space-y-2 sm:text-left">
                <div className="space-y-0.5">
                <p className="text-lg text-black font-semibold">
                    {props.name}
                </p>
                <p className="text-slate-500 font-medium">
                    {props.role}
                </p>
                </div>
                <button className="px-4 py-1 text-sm text-purple-600 font-semibold rounded-full border border-purple-200 hover:text-white hover:bg-purple-600 hover:border-transparent focus:outline-none focus:ring-2 focus:ring-purple-600 focus:ring-offset-2">
                    Update
                </button>
            </div>
        </div>
    );
}

export default Employee;
```
we replaced `Erin Lindford` with our props so `{props.name}` and we replaced `Product Engineer` with our props so `{props.role}`

We add some css design to our employee components in App.js:
```
<div className='flex flex-wrap justify-center'>
        <Employee name="Hashim" role="Intern"/>
        <Employee name="Abby" role={role}/>
        <Employee name="John"/>
</div>
```
We added an image by using the css attribute ```img```, then in the Employee.Js we set the src to `{props.img}`

# #########################################################################################
# Part.5 - Tailwind CSS

- To install it we write in the command:
```
npm install -D tailwindcss postcss autoprefixer
```
- To initialize it:
```
npx tailwindcss init -p
```
- Then we will have a new file in our project directory called tailwind.config.js and we need to change it to this code:
```
/** @type {import('tailwindcss').config} */
module.exports = {
    content: [
        "./src/**/*.{js,jsx,ts,tsx}",
    ],
    theme: {
        extend: {},
    }, 
    plugins: [],
};
```
- Then we need to add Tailwind directives to our CSS (App.css) by using these essential imports:
```
@tailwind base;
@tailwind components;
@tailwind utilities;
```
- Then we should start our project in command by using ```npm run start```.
- Then we can start using Tailwind in our project by using Tailwind's utility classes to style our content.

An example we can set the background to red with intensity of 300 by add this code to our div:
```
<div className="App bg-red-300">
```
Note: we changed the naming of the css file from App.css to index.css!

Check tailwindcss.com for any css attribute that you may want to use.

# #########################################################################################
# Part.4 - useState Hook

We are not supposed to change the value of the props in the child instead we will change it
in the parent, and the way we are going to change this value is with the use of state,
state will allows us to keep track of values but it is a little bit different than just a 
variable, because the state can be tied to the user interface, so when the state changes 
the user interface will automatically update. (without a page refresh)

So in the App.js we have first initialized a variable called role:
```
let role = "dev";
```
Then we took an input from the user (in App.js) that should supposedly change what is on the screen automatically:
```
<input type="text" onChange={(e) => {
        console.log(e.target.value);
        role = e.target.value;
}}
```
Then we assiged inside the element a prop value of ```<Employee name="Abby" role={role}/>``` that should change the value automatically as soon as it is written in the input, however, we are not achieving this yet, and here comes the use of state!

To use state we need to import it by writing ```import {useState} from 'react';```

And to use it we write ```const [role, setRole] = useState();``` and delete the ```let role = "dev";```
It is by convention people will have the variable ```role``` and the next varibale will be prefixed with set hence, ```setRole```

To insert any default values we can add an argument to the ```useState()``` for example, ```const [role, setRole] = useState("dev");```

Important rule is that we never assign a value to a variable directly we always assign it using the set method such as ```setRole``` like below:
```
<input type="text" onChange={(e) => {
    console.log(e.target.value);
    setRole(e.target.value);
}}
```
The component does not do anything to change the value all it does is display the value, the parent is the one that controls what is passed to the component.

useState is an example of a hook, and usually hooks start with the word "use".

Hooks allows you to use other react features without writing a class.

# #########################################################################################
# Part.3 - Props

Props is how you pass data from the parent down to a child component.

- We can pass down props and use it like below,
```
function Employee(props){
    return <h3>Employee {props.name}</h3>;
}
```
- then we can assign the name in the App.js as follows:
```
<Employee name="Hashim"/>
```
- We can use the following to avoid having an empty role string:
- So if props.role is true then return props.role if not return the string "No role"
```
<p>{props.role ? props.role : "No role"}</p>
```
- We can also use this syntax which will do exactly as what is above but can make us more specific:
```
{props.role ? <p class="role">{props.role}</p> : <p class="norole">No role</p>}
```
Don't try to assign a value for props inside a component instead do it from the component call in the App.js

# #########################################################################################
# Part.2 - Components

Components work in a very similar way to functions, the only difference is they are going to return
HTML, specifically they are going to return what is known as JSX.

In src we create a components folder, and inside it we created a Employee.js component file.

In the Employee.js we need to ```export default Employee;``` and when we want to use it in other
files such as the App.js we need to ```import Employee from './components/Employee';```

To use/call the Employee component we can either use ```<Employee/>``` or ```<Employee></Employee>```

# #########################################################################################
# Part.1 - Setup

So to begin with we need to delete some useless files, and we need to keep only those files:
```
^node_modules (do not touch)
^Public
    favicon.ico
    index.html
    manifest.json
    robots.txt
^src
    App.css
    App.js
    index.js
.gitignore
package-lock.json
package.json
README.md
```
The commands we used in the terminal till now:
- npm create-react-app hello (This creates a react app build files)
- npm run start (This will open the localhost website and it will refresh everytime we save)
- npm run build (This will create a build file, then when we call it, it will save new edits to the build file)

* Note: we need to "npm run build" right before we upload to the internet to ensure we have up-to-date files

- npm install (This will make sure we have all dependencies installed)

- npm create-react-app created a repository so we can see on the left we have Source Control
- On the source control we can see changes and commit them to the repository
- After we do so, we can check by using the command: 
    - git log