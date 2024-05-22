# Getting Started with Create React App

Setting up a backend RESTful API using Node.js with Express and MongoDB, and then implementing a frontend using React.js to interact with this API.

### Backend Setup (Node.js with Express and MongoDB)

1. **Initialize Node.js Project:**
   - Create a new directory for your project and initialize a Node.js project:
     ```bash
     mkdir todo-list-app
     cd todo-list-app
     npm init -y
     ```

2. **Install Dependencies:**
   - Install necessary packages (`express`, `mongoose`, `body-parser`) for backend development:
     ```bash
     npm install express mongoose body-parser
     ```

3. **Set Up MongoDB Connection:**
   - Create a `server.js` file and set up a connection to your local MongoDB instance:
     ```javascript
     const express = require('express');
     const mongoose = require('mongoose');
     const bodyParser = require('body-parser');

     const app = express();
     const PORT = process.env.PORT || 5000;

     // Body parser middleware
     app.use(bodyParser.json());

     // MongoDB Connection
     mongoose.connect('mongodb://localhost/todo-list-db', {
       useNewUrlParser: true,
       useUnifiedTopology: true
     });

     const db = mongoose.connection;
     db.on('error', console.error.bind(console, 'MongoDB connection error:'));
     db.once('open', () => {
       console.log('Connected to MongoDB');
     });

     // Define Task Schema
     const taskSchema = new mongoose.Schema({
       title: String,
       description: String,
       completed: { type: Boolean, default: false }
     });

     const Task = mongoose.model('Task', taskSchema);

     // RESTful API Endpoints
     // Example: GET all tasks
     app.get('/api/tasks', async (req, res) => {
       try {
         const tasks = await Task.find();
         res.json(tasks);
       } catch (err) {
         res.status(500).json({ message: err.message });
       }
     });

     // Other CRUD endpoints (POST, PUT, DELETE) can be implemented similarly

     // Start server
     app.listen(PORT, () => {
       console.log(`Server is running on port ${PORT}`);
     });
     ```

4. **Implement CRUD Endpoints:**
   - Expand the `server.js` file to include CRUD operations (`POST`, `PUT`, `DELETE`) for tasks using Mongoose methods (`Task.create`, `Task.findByIdAndUpdate`, `Task.findByIdAndDelete`, etc.).

### Frontend Setup (React.js)

1. **Create React App:**
   - In a new terminal, navigate to the project directory and create a React app:
     ```bash
     npx create-react-app frontend
     ```

2. **Install Axios for API Requests:**
   - Install `axios` to make HTTP requests from the frontend to the backend:
     ```bash
     cd frontend
     npm install axios
     ```

3. **Implement Task Components:**
   - Inside the React app (`src` directory), create components for displaying tasks, adding/editing tasks, etc.
   - Use `axios` to interact with the backend API endpoints created earlier.




4. **Task Component (`Todos.js`):**
```javascript
import React from "react";
import Checkbox from "@mui/material/Checkbox";
import ClearIcon from "@mui/icons-material/Clear";
import KeyboardArrowUpIcon from "@mui/icons-material/KeyboardArrowUp";
import KeyboardArrowDownIcon from "@mui/icons-material/KeyboardArrowDown";
import EditIcon from "@mui/icons-material/Edit";


function Todos({ taskList, setTaskList, setTask, setEditing}) {
  function handleChangeCheckBox(index) {
    const updatedTaskList = [...taskList];
    updatedTaskList[index].isChecked = !updatedTaskList[index].isChecked;
    setTaskList(updatedTaskList);
  }

  function handleDeleteTask(index) {
    const updatedTaskList = [...taskList];
    updatedTaskList.splice(index, 1);
    setTaskList(updatedTaskList);
  }

  function handleTaskUp(index) {
    if (index !== 0) {
      const updatedTaskList = [...taskList];
      const temp1 = updatedTaskList[index];
      const temp2 = updatedTaskList[index - 1];
      updatedTaskList[index] = temp2;
      updatedTaskList[index - 1] = temp1;
      setTaskList(updatedTaskList);
    }
  }

  function handleTaskDown(index) {
    if (index !== taskList.length - 1) {
      const updatedTaskList = [...taskList];
      const temp1 = updatedTaskList[index];
      const temp2 = updatedTaskList[index + 1];
      updatedTaskList[index] = temp2;
      updatedTaskList[index + 1] = temp1;
      setTaskList(updatedTaskList);
    }
  }

  function handleTaskEdit(index){
    const updatedTaskList = [...taskList]
    const taskToEdit = updatedTaskList[index];
    setTask(taskToEdit.text)
    setEditing({ isEdit: true, index: index });
  }

  return (
    <>
      <section className="todoListContainer">
        {taskList.map((task, index) => (
          <div key={index} className="todoItem">
            <div className="container">
              <div className="checkBox">
                <Checkbox
                  sx={{
                    color: "rgba(227, 221, 211, 0.428)",
                  }}
                  checked={task.isChecked}
                  onChange={() => handleChangeCheckBox(index)}
                />
              </div>

              <div className="itemName">
                <p className={task.isChecked ? "strikethrough" : ""}>
                  {task.text}
                </p>
              </div>
            </div>

            <div className="icons">
              <KeyboardArrowUpIcon
                onClick={() => handleTaskUp(index)}
                sx={{
                  cursor: "pointer",
                  fontSize: "2em",
                  transition: "font-size 0.3s",
                  ":hover": {
                    fontSize: "2.05em",
                    color: "#59C2FF",
                  },
                }}
              />

              <KeyboardArrowDownIcon
                onClick={() => handleTaskDown(index)}
                sx={{
                  cursor: "pointer",
                  fontSize: "2em",
                  transition: "font-size 0.3s",
                  ":hover": {
                    fontSize: "2.05em",
                    color: "#59C2FF",
                  },
                }}
              />

              <EditIcon
                onClick={() => handleTaskEdit(index)}
                sx={{
                  cursor: "pointer",
                  fontSize: "1.5em",
                  transition: "font-size 0.3s",
                  ":hover": {
                    fontSize: "1.7em",
                    color: "#59C2FF",
                  },
                }}
              />

              <ClearIcon
                onClick={() => handleDeleteTask(index)}
                sx={{
                  cursor: "pointer",
                  fontSize: "1.7em",
                  transition: "font-size 0.3s",
                  ":hover": {
                    fontSize: "1.75em",
                    color: "#59C2FF",
                  },
                }}
              />
            </div>
          </div>
        ))}
      </section>
    </>
  );
}

export default Todos;
   ```



**UI & Task Component (`TaskInputForm.js`):**
```javascript
import React from "react";

function TaskInputForm({ inputError, task, handleChange, handleClickTaskAdd, editing}) {
  return (
    <>
      <form
        className={`container ${inputError ? "shake" : ""}`}
        onSubmit={(e) => e.preventDefault()}
      >
        <input
          type="text"
          name="task"
          placeholder="Enter Task"
          value={task}
          onChange={handleChange}
        />

        <button onClick={handleClickTaskAdd}> {editing.isEdit? "Edit": "Add"} Task</button>
      </form>
    </>
  );
}

export default TaskInputForm;
```

**UI Component (`Footer.js`):**
```javascript
function Footer() {
  return (
    <>
      <footer className="container">
        <p id="footerText">Copyright © 2024 • Kayung</p>
      </footer>
    </>
  );
}

export default Footer;
```

**UI Component (`Header.js`):**
```javascript
function Header() {
  return (
    <>
      <header id="title" className="container">
        <h1>ToDo List</h1>
        
      </header>
    </>
  );
}

export default Header;
```

**UI Component (`Greeting.js`):**
```javascript
const Greeting = () => {
  const date = new Date();
  const hours = date.getHours();
  const months = [
    "January", 
    "February", 
    "March", 
    "April", 
    "May", 
    "June", 
    "July", 
    "August", 
    "September", 
    "October", 
    "November", 
    "December"
  ]

  let month = months[date.getMonth()]
  
 let today = `${date.getUTCDate()} ${month} ${date.getFullYear()}`

  let greeting = "";

  if (hours > 0 && hours < 12) {
    greeting = "Good morning";
  } 
  else if (hours >= 12 && hours < 18) {
    greeting = "Good afternoon";
  } 
  else if (hours >= 18 && hours < 21) {
    greeting = "Good evening";
  } 
  else {
    greeting = "Good night";
  }

  return (
    <div className="py-7 px-10 max-sm:px-2 max-sm:py-3">
      <div className=" max-w-[1300px] max-lg:container flex justify-between items-center">
        <h1 className=" text-white font-bold flex items-center gap-1 text-3xl max-sm:text-lg"><span className="max-sm:text-3xl">&#128075;</span>{greeting}</h1>

        <div>
          <p className=" text-white font-semibold text-lg max-sm:text-sm">{today}</p>
        </div>
      </div>
    </div>
  );
};

export default Greeting;

```
**Create a Style Component (`App.css`):** 
For handling the styles of the UI
```javascript
:root {
  --primary-color: #5468ff;
  --secondary-color: #5adaff;
  --box-shadow-color: rgba(45, 35, 66, .4);
  --background-color: #161B22;
  --text-color: #fff;
}

*::before,
*,
*::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: inherit;
}

body {
  font-family: "JetBrains Mono", monospace, sans-serif;
  overflow-x: hidden;
}
.img{
  width: auto;
  height:fit-content;
}
.greeting{
  
}
#title h1 {
  font-size: 2.5em;
  margin: 1em;
}

#footerText {
  font-style: italic;
  margin-top: 5em;
}

.container {
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0.5em;
}

button {
  align-items: center;
  background-image: radial-gradient(100% 100% at 100% 0, var(--secondary-color) 0, var(--primary-color) 100%);
  border: 0;
  border-radius: 6px;
  box-shadow: var(--box-shadow-color) 0 2px 4px, var(--box-shadow-color) 0 7px 13px -3px, var(--box-shadow-color) 0 -3px 0 inset;
  box-sizing: border-box;
  color: var(--text-color);
  cursor: pointer;
  font-family: "JetBrains Mono", monospace;
  height: 48px;
  justify-content: center;
  line-height: 1;
  list-style: none;
  overflow: hidden;
  padding: 0 16px;
  position: relative;
  text-align: left;
  text-decoration: none;
  transition: box-shadow .15s, transform .15s;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  white-space: nowrap;
  will-change: box-shadow, transform;
  font-size: 18px;
  margin-left: 0.5em;
}

button:focus {
  box-shadow: var(--primary-color) 0 0 0 1.5px inset, var(--box-shadow-color) 0 2px 4px, var(--box-shadow-color) 0 7px 13px -3px, var(--primary-color) 0 -3px 0 inset;
}

button:hover {
  box-shadow: var(--box-shadow-color) 0 4px 8px, var(--box-shadow-color) 0 7px 13px -3px, var(--primary-color) 0 -3px 0 inset;
  transform: translateY(-2px);
}

button:active {
  box-shadow: var(--primary-color) 0 3px 7px inset;
  transform: translateY(2px);
}

input {
  padding: 12px;
  font-size: 18px;
  border: 1px solid var(--primary-color);
  border-radius: 6px;
  width: 25vw;
  transition: box-shadow .15s, transform .15s;
}

input:focus {
  box-shadow: var(--primary-color) 0 0 0 1.5px inset;
}

input:hover {
  box-shadow: var(--box-shadow-color) 0 4px 8px, var(--box-shadow-color) 0 7px 13px -3px, var(--primary-color) 0 -3px 0 inset;
}

input:active {
  box-shadow: var(--primary-color) 0 3px 7px inset;
  transform: translateY(2px);
}

.todoItem {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 6px;
  font-size: 14px;
  border: 1px solid var(--primary-color);
  border-radius: 6px;
  width: 35vw;
  transition: box-shadow 0.15s, transform 0.15s;
  box-sizing: border-box;
  margin-bottom: 10px;
  box-shadow: var(--box-shadow-color) 0 2px 4px, var(--box-shadow-color) 0 7px 13px -3px, var(--box-shadow-color) 0 -3px 0 inset;
  background-color: var(--background-color);
}

.todoItem:hover {
  box-shadow: var(--box-shadow-color) 0 4px 8px, var(--box-shadow-color) 0 7px 13px -3px, var(--primary-color) 0 -3px 0 inset;
}

.todoItem:active {
  box-shadow: var(--primary-color) 0 3px 7px inset;
  transform: translateY(2px);
}

.todoItem:focus {
  box-shadow: var(--primary-color) 0 0 0 1.5px inset;
}

@keyframes shake {
  0%, 100% {
    transform: translateX(0);
  }

  10%, 30%, 50%, 70%, 90% {
    transform: translateX(-10px);
  }

  20%, 40%, 60%, 80% {
    transform: translateX(10px);
  }
}

.shake {
  animation: shake 1s ease-in-out;
}

.popup {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: var(--primary-color);
  color: var(--text-color);
  padding: 20px;
  border-radius: 6px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
  z-index: 1000;
  text-align: center;
}

.popup p {
  margin: 0;
  font-size: 18px;
}

.popup button {
  background-color: var(--text-color);
  color: var(--primary-color);
  border: none;
  padding: 10px 20px;
  margin-top: 10px;
  cursor: pointer;
  border-radius: 4px;
}

.strikethrough {
  text-decoration: line-through;
  color: rgba(227, 221, 211, 0.428);
}

.itemName p {
  font-size: 1.5em;
}

.todoListContainer {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

.icons {
  margin-right: 0.5em;
  color: rgba(227, 221, 211, 0.428);
  display: flex;
  align-items: center;
  justify-content: center;
}

@media screen and (max-width: 900px) {
  #title h1 {
    font-size: 2.2em;
  }

  input {
    font-size: 1.1em;
    width: 40vw;
  }

  .itemName p {
    font-size: 1.3em;
  }

  .todoItem {
    width: 80%;
  }
}

@media screen and (max-width: 600px) {
  #title h1 {
    font-size: 2em;
  }

  button {
    font-size: 18px;
  }

  input {
    font-size: 1em;
    width: 50vw;
  }

  .itemName p {
    font-size: 1.2em;
  }

  .todoItem {
    width: 90%;
  }
}
```



**UI & Task Component (`App.js`):**
For Handling the contents of the App and UI of the todolist
```javascript
import React, { useState, useEffect } from "react";
import "./App.css";
import TaskInputForm from "./components/TaskInputForm";
import Todos from "./components/Todos";
import Header from "./components/Header";
import Footer from "./components/Footer";
import Greeting from "./components/Greeting";
import logo from './logo.svg';


function App() {
  const [task, setTask] = useState("");
  const [taskList, setTaskList] = useState(null); // Set initial state to null
  const [inputError, setInputError] = useState(false);
  const [showPopup, setShowPopup] = useState(false);
  const [editing, setEditing] = useState({index: null, isEdit: false});


  // Load tasks from localStorage on component mount
  useEffect(() => {
    const storedTaskList = JSON.parse(localStorage.getItem("taskList")) || [];

    // Set the taskList state only if it's null
    if (taskList === null) {
      setTaskList(storedTaskList);
    }
  }, [taskList]);

  // Save tasks to localStorage whenever taskList changes
  useEffect(() => {
    if (taskList !== null) {
      localStorage.setItem("taskList", JSON.stringify(taskList));
    }
  }, [taskList]);

  function handleClickTaskAdd() {
    const formattedTask = task.trim().toLowerCase();

    if (formattedTask === "") {
      setInputError(true);

      setTimeout(() => {
        setInputError(false);
      }, 500);

      return;
    }

    if (taskList.some((t) => t.text.toLowerCase() === formattedTask)) {
      setShowPopup(true);

      setTimeout(() => {
        setShowPopup(false);
      }, 1500);

      setTask("");

      return;
    }

    // Update taskList state and localStorage
    if (!editing.isEdit) {
      const updatedTaskList = [
        ...(taskList || []),
        { text: task, isChecked: false },
      ];
      setTaskList(updatedTaskList);
    }
    else{
      const updatedTaskList = [...taskList]
      const editIndex = editing.index;
      console.log(editIndex)
      updatedTaskList[editIndex].text = task;
      setTaskList(updatedTaskList);
      setEditing({index: null, isEdit: false});
    }
    setTask("");
  }

  function handleChange(e) {
    setTask(e.target.value);
  }

  return (
    <>
      <Header />
      <img src={logo} className="img" alt="logo" sx={{
                  width: "100px",
                  height: "100px",
                }}/>
     <center><Greeting /></center> 
      <main>
        <TaskInputForm
          inputError={inputError}
          task={task}
          handleChange={handleChange}
          handleClickTaskAdd={handleClickTaskAdd}
          editing={editing}
        />

        <Todos
          taskList={taskList || []}
          setTaskList={setTaskList}
          setTask={setTask}
          editing={editing}
          setEditing={setEditing}
        />

        {showPopup && (
          <div className="popup">
            <p>Task already exists!</p>
          </div>
        )}
      </main>

      <Footer />
    </>
  );
}

export default App;

```






**UI & Task Component (`App.js`):**
For Handling the contents of the App and UI of the todolist
```javascript
import React from "react";

function TaskInputForm({ inputError, task, handleChange, handleClickTaskAdd, editing}) {
  return (
    <>
      <form
        className={`container ${inputError ? "shake" : ""}`}
        onSubmit={(e) => e.preventDefault()}
      >
        <input
          type="text"
          name="task"
          placeholder="Enter Task"
          value={task}
          onChange={handleChange}
        />

        <button onClick={handleClickTaskAdd}> {editing.isEdit? "Edit": "Add"} Task</button>
      </form>
    </>
  );
}

export default TaskInputForm;
```




5. **Integrate Task Components: (Index.js and Index.css)**
   - Use React Router (`react-router-dom`) to navigate between different views (e.g., task list, task details, add/edit task forms).

**UI & Task Component (`Index.js`):**
For Handling the contents of the App and UI of the todolist
```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
//reportWebVitals();

```
**UI & Task Component (`Index.css`):**
For Handling the contents of the App and UI of the todolist
```css
:root {
  color-scheme: light dark;
  color: rgba(255, 255, 255, 0.87);
  background-color: #0D1117;

  font-synthesis: none;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

```


**Root Component (`main.js`):**
Created For referencing as the root of the App.js, App.css and Index.js, Index.css
```javascript
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App.js";
import "./index.css";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

```
### Finally a test library

setupTests.js
// jest-dom adds custom jest matchers for asserting on DOM nodes.
// allows you to do things like:
// expect(element).toHaveTextContent(/react/i)
// learn more: https://github.com/testing-library/jest-dom
import '@testing-library/jest-dom';



### Running the Application

1. **Start Backend Server:**
   - In the project root directory, start the backend server:
     ```
     node server.js
     ```

2. **Start Frontend Development Server:**
   - Open a new terminal and navigate to the `frontend` directory:
     ```
     cd frontend
     npm start
     ```

3. **Access the Application:**
   - Open your web browser and go to `http://localhost:3000` or `http://192.168.43.9:3000` to view the Todo List application.



