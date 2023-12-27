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