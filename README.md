# #########################################################################################
# P3
Props is how you pass data from the parent down to a child component.

- We can pass down props and use it like below,
function Employee(props){
    return <h3>Employee {props.name}</h3>;
}

- then we can assign the name in the App.js as follows:
<Employee name="Hashim"/>

- We can use the following to avoid having an empty role string:
- So if props.role is true then return props.role if not return the string "No role"
<p>{props.role ? props.role : "No role"}</p>

- We can also use this syntax which will do exactly as what is above but can make us more specific:
{props.role ? <p class="role">{props.role}</p> : <p class="norole">No role</p>}

Don't try to assign a value for props inside a component instead do it from the component call in the App.js

# #########################################################################################
# P2
Components work in a very similar way to functions, the only difference is they are going to return
HTML, specifically they are going to return what is known as JSX.

In src we create a components folder, and inside it we created a Employee.js component file.

In the Employee.js we need to "export default Employee;" and when we want to use it in other
files such as the App.js we need to "import Employee from './components/Employee';"

To use/call the Employee component we can either use "<Employee/>" or "<Employee></Employee>"

# #########################################################################################
# P1
So to begin with we need to delete some useless files, and we need to keep only those files:
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