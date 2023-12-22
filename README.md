# #########################################################################################
# Part-10
Pass a Component to Props

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

# #########################################################################################
# Part-9
How to Push to State Array

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
# Part-8
Create a Popup Modal with React Bootstrap.

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
# Part-7
Map through State Array (Loop)
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
# Part-6
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
# Part-5
We are going to use Tailwind CSS.
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
# Part-4
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
# Part-3
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
# Part-2
Components work in a very similar way to functions, the only difference is they are going to return
HTML, specifically they are going to return what is known as JSX.

In src we create a components folder, and inside it we created a Employee.js component file.

In the Employee.js we need to ```export default Employee;``` and when we want to use it in other
files such as the App.js we need to ```import Employee from './components/Employee';```

To use/call the Employee component we can either use ```<Employee/>``` or ```<Employee></Employee>```

# #########################################################################################
# Part-1
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