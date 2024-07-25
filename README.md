# Travel List Application

## Project Description
This is a simple travel-list application built with React. It helps users manage and organize items they need to pack for a trip. Users can add, delete, and toggle the packed status of items. The app also provides statistics on the number of items packed and remaining.

## Features
- Add items with descriptions and quantities.
- Toggle the packed status of each item.
- Delete items from the list.
- Sort items by input order, description, or packed status.
- Clear the entire list of items.
- View packing statistics.

## Getting Started
This project was bootstrapped with Create React App.

### Prerequisites
Make sure you have Node.js and npm installed on your machine. You can download them from Node.js.

### Installation
Clone the repository:  
`git clone https://github.com/your-username/travel-list-app.git`  
`cd travel-list-app`

Install the dependencies:  
`npm install`

### Running the App
To start the development server, run:  
`npm start`  
Open http://localhost:3000 to view the app in your browser. The page will reload when you make changes. You may also see any lint errors in the console.

### Running Tests
To launch the test runner in interactive watch mode, run:  
`npm test`  
See the section about running tests for more information.

### Building for Production
To build the app for production, run:  
`npm run build`  
This builds the app for production to the build folder. It correctly bundles React in production mode and optimizes the build for the best performance. The build is minified and the filenames include the hashes. Your app is ready to be deployed! See the section about deployment for more information.

### Ejecting
Note: this is a one-way operation. Once you eject, you can't go back! If you aren't satisfied with the build tool and configuration choices, you can eject at any time. This command will remove the single build dependency from your project.  
`npm run eject`  
This will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc.) right into your project so you have full control over them. At this point you're on your own.

### Learn More
You can learn more in the Create React App documentation. To learn React, check out the React documentation.

### Additional Resources
- Code Splitting
- Analyzing the Bundle Size
- Making a Progressive Web App
- Advanced Configuration
- Deployment
- Troubleshooting npm run build failures

## Code Overview

### App.js
Import the necessary components and hooks from React:

import { useState } from "react";
import Logo from "./logo";
import Form from "./Form";
import PackingList from "./PackingList";
import Stats from "./Stats";

Create the main App component:

export default function App() {
  const [items, setItems] = useState([]);

  function clearList() {
    const confirmed = window.confirm("Are you sure you want to delete all items?");
    if (confirmed) {
      setItems([]);
    }
  }

  function handleAddIems(item) {
    setItems((items) => [...items, item]);
  }

  function handleDeleteItem(id) {
    setItems((items) => items.filter((item) => item.id !== id));
  }

  function handleTogglitems(id) {
    setItems((items) =>
      items.map((item) =>
        item.id === id ? { ...item, packed: !item.packed } : item
      )
    );
  }

  return (
    <div className="app">
      <Logo />
      <Form onAddItems={handleAddIems} />
      <PackingList
        items={items}
        onDeleteItems={handleDeleteItem}
        onToggleItems={handleTogglitems}
        onClearItems={clearList}
      />
      <Stats items={items} />
    </div>
  );
}

### Form.js
Import the necessary hooks from React:

import { useState } from "react";

Create the Form component:

export default function Form({ onAddItems }) {
  const [description, setDescription] = useState(" ");
  const [quantity, setQuantity] = useState(1);

  function handleSubmit(e) {
    e.preventDefault();
    if (description === " ") return;
    const newItem = { description, quantity, packed: false, id: Date.now() };
    console.log(newItem);
    onAddItems(newItem);
    setDescription(" ");
    setQuantity(1);
  }

  return (
    <form className="add-form" onClick={handleSubmit}>
      <h3>What do you need for your 🥰 trip</h3>
      <select value={quantity} onChange={(e) => setQuantity(Number(e.target.value))}>
        {Array.from({ length: 20 }, (_, i) => i + 1).map((num) => (
          <option value={num} key={num}>{num}</option>
        ))}
      </select>
      <input type="text" placeholder="Item..." value={description} onChange={(e) => setDescription(e.target.value)} />
      <button>Add</button>
    </form>
  );
}

### Item.js
Create the Item component:

export default function Item({ item, onDeleteItems, onToggleItem }) {
  return (
    <li>
      <input type="checkbox" value={item.packed} onChange={() => onToggleItem(item.id)} />
      <span style={item.packed ? { textDecoration: "line-through" } : {}}>
        {item.quantity} {item.description}
      </span>
      <button onClick={() => onDeleteItems(item.id)}>❌&times</button>
    </li>
  );
}

### Logo.js
Create the Logo component:

export default function Logo() {
  return <h1>🌴 Far Away 💼</h1>;
}

### PackingList.js
Import the necessary hooks and components:

import { useState } from "react";
import Item from "./Item";

Create the PackingList component:

export default function PackingList({ items, onDeleteItems, onToggleItems, onClearItems }) {
  const [sortBy, setSortBy] = useState("input");
  let sortedItems;

  if (sortBy === "input") {
    sortedItems = items;
  }
  if (sortBy === "description") {
    sortedItems = items.slice().sort((a, b) => a.description.localeCompare(b.description));
  }
  if (sortBy === "packed") {
    sortedItems = items.slice().sort((a, b) => Number(a.packed) - Number(b.packed));
  }
  return (
    <div className="list">
      <ul>
        {sortedItems.map((item) => (
          <Item item={item} onDeleteItems={onDeleteItems} onToggleItem={onToggleItems} key={item.id} />
        ))}
      </ul>
      <div className="actions">
        <select value={sortBy} onChange={(e) => setSortBy(e.target.value)}>
          <option value="input">Sort by input order</option>
          <option value="description">Sort by description</option>
          <option value="packed">Sort by packed status</option>
        </select>
        <button onClick={() => onClearItems()}>Clear list</button>
      </div>
    </div>
  );
}

### Stats.js
Create the Stats component:

export default function Stats({ items }) {
  if (!items.length)
    return (
      <p className="stats">
        <em>Start adding some items to your packing list 🚀</em>
      </p>
    );
  const numItems = items.length;
  const numPacked = items.filter((item) => item.packed).length;
  const percentage = Math.round((numPacked / numItems) * 100);
  return (
    <footer className="stats">
      <em>
        {percentage === 100
          ? "You got everything! Ready to go ✈️"
          : `You have ${numItems} items on your list, and you already packed ${numPacked} (${percentage}%)`}
      </em>
    </footer>
  );
}
