# data-
آرشف آنلاین 
pip install flask sqlite3 gunicorn
myshop/
├── app.py
├── requirements.txt
├── Procfile
├── runtime.txt
├── templates/
│   └── index.html
├── static/
│   └── styles.css
├── database.db
from flask import Flask, render_template, request, redirect
import sqlite3

app = Flask(__name__)

def init_db():
    conn = sqlite3.connect('database.db')
    c = conn.cursor()
    c.execute('''CREATE TABLE IF NOT EXISTS sales
                 (id INTEGER PRIMARY KEY, item TEXT, quantity INTEGER, price REAL, date TEXT)''')
    conn.commit()
    conn.close()

@app.route('/')
def index():
    conn = sqlite3.connect('database.db')
    c = conn.cursor()
    c.execute('SELECT * FROM sales')
    sales = c.fetchall()
    conn.close()
    return render_template('index.html', sales=sales)

@app.route('/add', methods=['POST'])
def add_sale():
    item = request.form['item']
    quantity = request.form['quantity']
    price = request.form['price']
    date = request.form['date']
    conn = sqlite3.connect('database.db')
    c = conn.cursor()
    c.execute('INSERT INTO sales (item, quantity, price, date) VALUES (?, ?, ?, ?)',
              (item, quantity, price, date))
    conn.commit()
    conn.close()
    return redirect('/')

if __name__ == '__main__':
    init_db()
    app.run(debug=True)
<!DOCTYPE html>
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>مدیریت خرید و فروش</title>
    <link rel="stylesheet" href="/static/styles.css">
</head>
<body>
    <h1>مدیریت خرید و فروش</h1>
    <form action="/add" method="post">
        <input type="text" name="item" placeholder="کالا" required>
        <input type="number" name="quantity" placeholder="تعداد" required>
        <input type="number" step="0.01" name="price" placeholder="قیمت" required>
        <input type="date" name="date" required>
        <button type="submit">افزودن خرید/فروش</button>
    </form>
    <h2>لیست خرید و فروش</h2>
    <ul>
        {% for sale in sales %}
            <li>{{ sale[1] }} - {{ sale[2] }} - {{ sale[3] }} - {{ sale[4] }}</li>
        {% endfor %}
    </ul>
</body>
</html>
body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f4;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    margin: 0;
}
.contact-form {
    background: #fff;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    width: 300px;
}
.contact-form h2 {
    margin-top: 0;
}
.contact-form input, .contact-form textarea {
    width: 100%;
    padding: 10px;
    margin: 5px 0;
    border: 1px solid #ccc;
    border-radius: 5px;
}
.contact-form button {
    background-color: #007BFF;
    color: #fff;
    border: none;
    padding: 10px;
    border-radius: 5px;
    cursor: pointer;
}
.contact-form button:hover {
    background-color: #0056b3;
}
flask
sqlite3
gunicorn
web: gunicorn app:app
python-3.9.1

