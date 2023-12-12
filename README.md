 **Problem Analysis**

The problem is to build an application that tracks gifts. The application should allow users to:

* Add new gifts
* View a list of all gifts
* Edit gifts
* Delete gifts

**Design**

The application will be a Flask application. The following HTML files will be needed:

* `index.html`: This file will be the home page of the application. It will display a list of all gifts.
* `add_gift.html`: This file will allow users to add new gifts.
* `edit_gift.html`: This file will allow users to edit existing gifts.
* `delete_gift.html`: This file will allow users to delete existing gifts.

The following routes will be needed:

* `/`: This route will display the home page.
* `/add_gift`: This route will display the form for adding new gifts.
* `/edit_gift/<gift_id>`: This route will display the form for editing existing gifts.
* `/delete_gift/<gift_id>`: This route will delete the specified gift.

**Database**

The application will use a SQLite database to store the gift data. The following table will be needed:

```
CREATE TABLE gifts (
  id INTEGER PRIMARY KEY,
  name TEXT,
  description TEXT,
  price REAL
);
```

**Implementation**

The application can be implemented using the following code:

```python
from flask import Flask, render_template, request, redirect, url_for
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///gifts.db'
db = SQLAlchemy(app)

class Gift(db.Model):
  id = db.Column(db.Integer, primary_key=True)
  name = db.Column(db.String(80))
  description = db.Column(db.String(120))
  price = db.Column(db.Float)

@app.route('/')
def index():
  gifts = Gift.query.all()
  return render_template('index.html', gifts=gifts)

@app.route('/add_gift', methods=['GET', 'POST'])
def add_gift():
  if request.method == 'POST':
    name = request.form['name']
    description = request.form['description']
    price = request.form['price']
    gift = Gift(name=name, description=description, price=price)
    db.session.add(gift)
    db.session.commit()
    return redirect(url_for('index'))
  return render_template('add_gift.html')

@app.route('/edit_gift/<gift_id>', methods=['GET', 'POST'])
def edit_gift(gift_id):
  gift = Gift.query.get(gift_id)
  if request.method == 'POST':
    gift.name = request.form['name']
    gift.description = request.form['description']
    gift.price = request.form['price']
    db.session.commit()
    return redirect(url_for('index'))
  return render_template('edit_gift.html', gift=gift)

@app.route('/delete_gift/<gift_id>')
def delete_gift(gift_id):
  gift = Gift.query.get(gift_id)
  db.session.delete(gift)
  db.session.commit()
  return redirect(url_for('index'))

if __name__ == '__main__':
  app.run()
```

**Testing**

The application can be tested by running the following commands:

```
$ python app.py
$ curl http://localhost:5000/
$ curl http://localhost:5000/add_gift -d "name=Gift 1" -d "description=This is a gift." -d "price=10.00"
$ curl http://localhost:5000/
$ curl http://localhost:5000/edit_gift/1 -d "name=Gift 1 (edited)" -d "description=This is a gift. (edited)" -d "price=10.00"
$ curl http://localhost:5000/
$ curl http://localhost:5000/delete_gift/1
$ curl http://localhost:5000/
```