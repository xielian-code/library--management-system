# library--management-system
Project 
index.html
 library_project/
│
├── app.py
├── requirements.txt
├── templates/
│   ├── home.html
│   ├── add_book.html
│   ├── search_book.html
│   └── layout.html
└── static/
    └── style.css
    from flask import Flask, render_template, request, redirect, url_for
from flask_sqlalchemy import SQLAlchemy
from datetime import datetime

app = Flask(__name__)

# SQLite for simplicity; replace with MySQL for public server
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///library.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)

# Database models
class Book(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    author = db.Column(db.String(50), nullable=False)
    genre = db.Column(db.String(50))
    available_qty = db.Column(db.Integer, default=1)
    date_added = db.Column(db.DateTime, default=datetime.utcnow)

# Routes
@app.route('/')
def home():
    books = Book.query.all()
    return render_template('home.html', books=books)

@app.route('/add_book', methods=['GET', 'POST'])
def add_book():
    if request.method == 'POST':
        title = request.form['title']
        author = request.form['author']
        genre = request.form['genre']
        qty = int(request.form['qty'])
        new_book = Book(title=title, author=author, genre=genre, available_qty=qty)
        db.session.add(new_book)
        db.session.commit()
        return redirect(url_for('home'))
    return render_template('add_book.html')

@app.route('/search', methods=['GET'])
def search():
    query = request.args.get('query')
    results = Book.query.filter(
        (Book.title.contains(query)) | 
        (Book.author.contains(query)) | 
        (Book.genre.contains(query))
    ).all()
    return render_template('search_book.html', books=results, query=query)

if __name__ == '__main__':
    with app.app_context():
        db.create_all()  # create database if not exists
    app.run(debug=True)
    <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Library System</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
    <nav>
        <a href="{{ url_for('home') }}">Home</a>
        <a href="{{ url_for('add_book') }}">Add Book</a>
        <form action="{{ url_for('search') }}" method="get" style="display:inline;">
            <input type="text" name="query" placeholder="Search books">
            <input type="submit" value="Search">
        </form>
    </nav>
    <div class="content">
        {% block content %}{% endblock %}
    </div>
</body>
</html>
{% extends 'layout.html' %}
{% block content %}
<h1>Library Books</h1>
<table border="1">
    <tr>
        <th>Title</th><th>Author</th><th>Genre</th><th>Quantity</th>
    </tr>
    {% for book in books %}
    <tr>
        <td>{{ book.title }}</td>
        <td>{{ book.author }}</td>
        <td>{{ book.genre }}</td>
        <td>{{ book.available_qty }}</td>
    </tr>
    {% endfor %}
</table>
{% endblock %}
{% extends 'layout.html' %}
{% block content %}
<h1>Add New Book</h1>
<form method="POST">
    <input type="text" name="title" placeholder="Title" required><br>
    <input type="text" name="author" placeholder="Author" required><br>
    <input type="text" name="genre" placeholder="Genre"><br>
    <input type="number" name="qty" placeholder="Quantity" required><br>
    <input type="submit" value="Add Book">
</form>
{% endblock %}
{% extends 'layout.html' %}
{% block content %}
<h1>Search Results for "{{ query }}"</h1>
<table border="1">
    <tr>
        <th>Title</th><th>Author</th><th>Genre</th><th>Quantity</th>
    </tr>
    {% for book in books %}
    <tr>
        <td>{{ book.title }}</td>
        <td>{{ book.author }}</td>
        <td>{{ book.genre }}</td>
        <td>{{ book.available_qty }}</td>
    </tr>
    {% endfor %}
</table>
{% endblock %}
Flask==3.2.0
Flask-SQLAlchemy==3.0.5
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://user:password@host/dbname'
sample_books = [
    ("Harry Potter and the Sorcerer's Stone", "J.K. Rowling", "Fantasy", 5),
    ("The Alchemist", "Paulo Coelho", "Fiction", 4),
    ("Wings of Fire", "A.P.J. Abdul Kalam", "Biography", 3),
    ("Atomic Habits", "James Clear", "Self Help", 6),
    ("Rich Dad Poor Dad", "Robert Kiyosaki", "Finance", 5),
    ("1984", "George Orwell", "Dystopian", 4),
    ("The Psychology of Money", "Morgan Housel", "Finance", 6),
    ("Think and Grow Rich", "Napoleon Hill", "Self Help", 5),
    ("Ikigai", "Héctor García", "Motivational", 4),
    ("The Hobbit", "J.R.R. Tolkien", "Fantasy", 3)
]

for title, author, genre, qty in sample_books:
    book = Book(title=title, author=author, genre=genre, available_qty=qty)
    db.session.add(book)

db.session.commit()
INSERT INTO book (title, author, genre, available_qty) VALUES
('Harry Potter and the Sorcerer''s Stone', 'J.K. Rowling', 'Fantasy', 5),
('The Alchemist', 'Paulo Coelho', 'Fiction', 4),
('Wings of Fire', 'A.P.J. Abdul Kalam', 'Biography', 3),
('Atomic Habits', 'James Clear', 'Self Help', 6),
('Rich Dad Poor Dad', 'Robert Kiyosaki', 'Finance', 5),
('1984', 'George Orwell', 'Dystopian', 4),
('The Psychology of Money', 'Morgan Housel', 'Finance', 6),
('Think and Grow Rich', 'Napoleon Hill', 'Self Help', 5),
('Ikigai', 'Héctor García', 'Motivational', 4),
('The Hobbit', 'J.R.R. Tolkien', 'Fantasy', 3);
