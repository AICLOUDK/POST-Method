# POST-Method
1. Update Backend to Handle POST Requests
backend/server.js (Add the POST route)
CopyRun
const express = require('express');
const cors = require('cors');
const app = express();
const PORT = 3000;

// Middleware to parse JSON bodies
app.use(cors());
app.use(express.json()); // Important for POST data

// Serve static files
app.use(express.static('../frontend'));

// Sample data
let books = [
  {
    "title": "The Great Gatsby",
    "author": "F. Scott Fitzgerald",
    "year": 1925
  },
  {
    "title": "To Kill a Mockingbird",
    "author": "Harper Lee",
    "year": 1960
  }
];

// GET route to fetch books
app.get('/api/books', (req, res) => {
  res.json(books);
});

// POST route to add new book
app.post('/api/books', (req, res) => {
  const newBook = req.body; // Get data from request body
  books.push(newBook); // Add to array
  res.status(201).json({ message: 'Book added', book: newBook });
});

app.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}`);
});
2. Update Frontend to Send POST Requests
frontend/index.html (Add a form to input new books)
CopyRun
<h2>Add a New Book</h2>
<form id="bookForm">
  <input type="text" id="title" placeholder="Title" required />
  <input type="text" id="author" placeholder="Author" required />
  <input type="number" id="year" placeholder="Year" required />
  <button type="submit">Add Book</button>
</form>
frontend/script.js (Handle form submission)
CopyRun
// Existing code to fetch and display books
fetch('http://localhost:3000/api/books')
  .then(response => response.json())
  .then(books => {
    displayBooks(books);
  })
  .catch(error => console.error('Error:', error));

// Function to display books
function displayBooks(books) {
  const container = document.getElementById('books-container');
  container.innerHTML = ''; // Clear existing
  books.forEach(book => {
    const div = document.createElement('div');
    div.className = 'book';
    div.innerHTML = `
      <h3>${book.title}</h3>
      <p><strong>Author:</strong> ${book.author}</p>
      <p><strong>Year:</strong> ${book.year}</p>
    `;
    container.appendChild(div);
  });
}

// Handle form submission to POST new book
document.getElementById('bookForm').addEventListener('submit', function(e) {
  e.preventDefault(); // Prevent page refresh

  const newBook = {
    title: document.getElementById('title').value,
    author: document.getElementById('author').value,
    year: parseInt(document.getElementById('year').value),
  };

  fetch('http://localhost:3000/api/books', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(newBook)
  })
  .then(response => response.json())
  .then(data => {
    alert(data.message);
    // Refresh the book list
    fetch('http://localhost:3000/api/books')
      .then(res => res.json())
      .then(books => displayBooks(books));
    // Reset form
    document.getElementById('bookForm').reset();
  })
  .catch(error => console.error('Error:', error));
});
3. Summary
The backend now handles POST requests to add new books.
The frontend includes a form for user input.
When the form is submitted, a POST request is sent to the server.
The server adds the book to the list and responds with a message.
The frontend refreshes the list to show the new entry.
