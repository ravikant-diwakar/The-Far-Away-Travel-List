# The "Far Away" Travel List

A React web app to create and manage your travel packing list. Easily add items, track packed items, and sort your list.

![image](https://github.com/ravikant-diwakar/The-Far-Away-Travel-List/assets/110620635/4e7087a9-25b0-45d3-a37f-2a551a44412a)

![image](https://github.com/ravikant-diwakar/The-Far-Away-Travel-List/assets/110620635/d59b7d70-3118-433f-9c5b-72e4346148f0)



### App.js

```jsx
export default function App() {
  // items ka state define kiya, jo list of items rakhta h
  const [items, setItems] = useState([]);

  // Function to add items to the list
  function handleAddItems(item) {
    setItems((items) => [...items, item]);
  }

  // Function to delete an item from the list
  function handleDeleteItem(id) {
    setItems((items) => items.filter((item) => item.id !== id));
  }

  // Function to toggle packed status of an item
  function handleToggleItem(id) {
    setItems((items) =>
      items.map((item) =>
        item.id === id ? { ...item, packed: !item.packed } : item
      )
    );
  }

  // Function to clear the entire list
  function handleClearList() {
    const confirmed = window.confirm(
      "Are you sure you want to delete all items?"
    );

    if (confirmed) setItems([]);
  }

  // Main render method
  return (
    <div className="app">
      <Logo />
      <Form onAddItems={handleAddItems} />
      <PackingList
        items={items}
        onDeleteItem={handleDeleteItem}
        onToggleItem={handleToggleItem}
        onClearList={handleClearList}
      />
      <Stats items={items} />
    </div>
  );
}
```

### Form.js

```jsx
// Form component to add new items
export default function Form({ onAddItems }) {
  const [description, setDescription] = useState("");  // Item description ka state
  const [quantity, setQuantity] = useState(1);         // Item quantity ka state

  // Form submission handle karne ka function
  function handleSubmit(e) {
    e.preventDefault();

    if (!description) return;

    // Naya item create kiya
    const newItem = { description, quantity, packed: false, id: Date.now() };

    // Add item function ko call kiya
    onAddItems(newItem);

    // Form reset karna
    setDescription("");
    setQuantity(1);
  }

  // Form render karna
  return (
    <form className="add-form" onSubmit={handleSubmit}>
      <h3>What do you need for your 😍 trip?</h3>
      <select
        value={quantity}
        onChange={(e) => setQuantity(Number(e.target.value))}
      >
        {Array.from({ length: 20 }, (_, i) => i + 1).map((num) => (
          <option value={num} key={num}>
            {num}
          </option>
        ))}
      </select>
      <input
        type="text"
        placeholder="Item..."
        value={description}
        onChange={(e) => setDescription(e.target.value)}
      />
      <button>Add</button>
    </form>
  );
}
```

### PackingList.js

```jsx
// Packing list component
export default function PackingList({
  items,
  onDeleteItem,
  onToggleItem,
  onClearList,
}) {
  const [sortBy, setSortBy] = useState("input");  // Sorting criteria ka state

  // Sorted items list create karna h
  let sortedItems;

  if (sortBy === "input") sortedItems = items;

  if (sortBy === "description")
    sortedItems = items
      .slice()
      .sort((a, b) => a.description.localeCompare(b.description));

  if (sortBy === "packed")
    sortedItems = items
      .slice()
      .sort((a, b) => Number(a.packed) - Number(b.packed));

  return (
    <div className="list">
      <ul>
        {sortedItems.map((item) => (
          <Item
            item={item}
            onDeleteItem={onDeleteItem}
            onToggleItem={onToggleItem}
            key={item.id}
          />
        ))}
      </ul>

      <div className="actions">
        <select value={sortBy} onChange={(e) => setSortBy(e.target.value)}>
          <option value="input">Sort by input order</option>
          <option value="description">Sort by description</option>
          <option value="packed">Sort by packed status</option>
        </select>
        <button onClick={onClearList}>Clear list</button>
      </div>
    </div>
  );
}
```

### Stats.js

```jsx
// Stats component to show statistics of the packing list
export default function Stats({ items }) {
  // Agar items nahi h to message dikhaye
  if (!items.length)
    return (
      <p className="stats">
        <em>Start adding some items to your packing list 🚀</em>
      </p>
    );

  // Calculate statistics
  const numItems = items.length;
  const numPacked = items.filter((item) => item.packed).length;
  const percentage = Math.round((numPacked / numItems) * 100);

  // Statistics ko render karna
  return (
    <footer className="stats">
      <em>
        {percentage === 100
          ? "You got everything! Ready to go ✈️"
          : ` 💼 You have ${numItems} items on your list, and you already packed ${numPacked} (${percentage}%)`}
      </em>
    </footer>
  );
}
```
