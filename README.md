# To Do List With a Database

we don't actually have a ```node_module``` folder
because whenever we download something from the Internet,
usually it won't come with a node_modules

install node_modules

```
npm i
```

How are we storing our data currently?

we now have an array with default objects

``` js
const items = ["Buy Food", "Cook Food", "Eat Food"];
```

and then when we add or post our new objects into app.js, 
we push items into the relevent array.

Now, we are deleting all these arrays, using mongoose instead.

First thing is to install mongoose

```
npm i mongoose
```

Now we are going to require Mongoose

```js
const mongoose = require("mongoose");
```

The next thing we are going to do is to create new database inside mongo db


```js
mongoose.connect("mongodb://localhost:27017/todolistDB", {useNewUrlParser: true});
```
```
{useNewUrlParser: true}
```
to avoid deprecation

After specifying the database, the next thing is creating a new schema.

We want to create a new item schema

```js
const itemsSchema = {
    name: String
};
```

Now we have our itemsSchema, the next thing we need to create is a new Mongoose model based on this schema.

(the singular version of the collection name, the schema to create this items collection )

```js
const Item = mongoose.model("Item", itemsSchema);
```

Deleting everything that related to the getDate();

Simply render the default list with title "Today"

After deleting it, we can see that the second thing that passing over to our list.ejs is our items array which we deleted it, so it no longer exists.

```js
res.render("list", {listTitle: "Today", newListItems: items});
```

Instead, we need to pass items that inside our items collection.

To do that, we need some default items.

Create 3 new documents using our brand new mongoose model.

```js
const item1 = new Item({
    name: "Welcome to your to do list!"
});

const item2 = new Item({
    name: "Hit the + button to add a new item!"
});

const item3 = new Item({
    name: "<-- Hit this to delete an item!"
});
```
Now gets all of them into an array, and call this array defaultItems, it contains item1, item2, item3

```js
const defaultItems = [item1, item2, item3];
```

Now we have the array, then we can use a method in mongoose called insertMany(), so we can insert those items into our items collection.

```js
Item.insertMany(defaultItems, function(err) {
    if(err){
        console.log(err);
    } else {
        console.log("Successfully saved the default items to the DB.")
    }
});
```

The next question is that how we can do the same thing in our command line when we said find and managed to grab all of the documents inside our items collection.

How do we do that inside app.js?

```js
Item.find({}, function(err, foundItems) {
    console.log(foundItems);
});
```

This will find everything inside our item collection.

Now we can get this method to triggered if we go access to the root route.

while we're able to log our items by using find method, the website is broken. 

because it doesn't know the items that we are passing to our list.ejs.

That's because items that's no longer exists.

Items reffered to our items array which we deleted and instead we want to pass over the foundItems.

Now we are going to pass over the foundItems to our to our list.ejs

```js
    res.render("list", { listTitle: "Today", newListItems: foundItems });
```

Now the wesite is back , but we still have problems.

The first problem is that our items are being rendered their entirety.

What we want just the name field.

Instead writing the entire document, we can simply tap into the name field.

```ejs
<p><%=  newListItems[i].name  %></p>
```


The second problem is that when we rerun our app.js, we insert more items into our database because it's rerunning InsertMany()
lines of code.


We want our default items to be inserted if items collection is empty.

so we have to check when the user access the root route whether if the item collection is empty, we are going to add our default items.

The first thing to do is to clear the database.

```
db.dropDatabase();
```

Rewrite the for loop into forEach loop 

newlistitems is an array of items documents, so we can call forEach loop on it. 

```ejs
<% newListItems.forEach(function(item){ %>
    <div class="item">
        <input type="checkbox">
        <p><%=  item.name  %></p>
      </div>
<% }) %>
```

How could we check to see if our collection of items is empty?

```js

   Item.find({}, function (err, foundItems) {
    if (foundItems.length === 0) {
      Item.insertMany(defaultItems, function (err) {
        if (err) {
          console.log(err);
        } else {
          console.log("Successfully saved the default items to the DB.")
        }
      });
    }
    else {
      res.render("list", {
        listTitle: "Today",
        newListItems: foundItems
      });

    }

  });
```

But on the browser, we are not getting the items show up.

```js
if (foundItems.length === 0) {
      Item.insertMany(defaultItems, function (err) {
        if (err) {
          console.log(err);
        } else {
          console.log("Successfully saved the default items to the DB.")
        }
      });
      res.redirect('/')
}
```

There's just one problem.

If we tried to add a new item right now then our app will crash because again it's telling us there's arry items no longer exists.


This happens in our post route and that is when we decide to add a new item and we trigger the form to post to our root route 


and we try to grab the item that the user tried to post 

and then we tried to push it into our items Array.

How can we do this using Mongo D-B instead?

We are going to simply delete all of work route and normal route.

We know that when this post route is triggered we can tap into something called ```request.body.newItem``` .

and that will refer to the text that the user entered into the input when they clicked on the plus button.

we are going to save into a constant called ```itemName``` and then we need to create a new item document based on the model in mongo db.

```js
const itemName = req.body.newItem;
```

```js
  const item = new Item({
    name: itemName
  });
```

we could actually just use the mongoose shortcut by saying item.save() 

and that will save this item into our collection of items.

In order to get it to show up at the home page all we need to do is just to res.redirect() back over
to the home route.

```js
res.redirect('/');
```

---

## Deleting Items from ToDoList DB

we want to be able to delete the item when we click the checkbox next to it.

To do that, we have to remove that particular item from our collection.

Inside list.ejs, there is a checkbox, and next to it we have an item name paragraph.

if we want to send some data when data gets clicked, we need a form and a post route.

so we create a new form 

```ejs
 <% newListItems.forEach(function(item){ %>
  <form action="">
    <div class="item">
      <input type="checkbox">
      <p><%=  item.name  %></p>
    </div>
  </form>

  <% }) %>
```

when we refresh, styling might looks a little off in some computers.

inside the css file, we are now having a form targeting our form element.

```css
form {
  text-align: center;
  margin-left: 20px;
}
```

we only want to implement the form with a class named item.

```css
form.item {
  text-align: center;
  margin-left: 20px;
}
```

now if we hit refresh, previous styles are restored.

the next thing we need to address is where this form post to?

to specify that, we need a action and a method.

This form is going to make a post request.

but the route needs to be different. so called it /delete

```ejs
<% newListItems.forEach(function(item){ %>
  <form action="/delete" method="POST">
    <div class="item">
      <input type="checkbox">
      <p><%=  item.name  %></p>
    </div>
  </form>
```

we can now create a delete route, and log req.body

```js
app.post("/delete", function(req, res) {
  console.log(req.body);
});
```

so what we do is we want to see what the form sends inside our list.ejs.

but when we see inside our console, we can see nothing happened.

the reason is that inside list.ejs, inside the form below, we actually have a button and a type submit.

whenever we have a submit button inside our form, when the button gets pressed, it will send all of the data inside our form and make the post request to the require route.

how do we get the form to submit when the checkbox is checked?

so we found the answer on stackoverflow.

it told us that we need to add an attribute onChange. 

```
onChange="this.form.submit()"
```

when the checkbox is checked, we will run this.form.submit(), which will take the current form that the input is inside to submit it and to make that post request to the delete route.

we need a way of accessing this input. we need to give it a name.

```ejs
<input type="checkbox" name="checkbox" onChange="this.form.submit()">
```

no we save the file and see the console which it prints out a value on inside a checkbox.

but value on is not really useful for us.

what we want is the item name that actually checked off.

inside for each loop, we can assign a value to our checkbox inside the id.

when we passing the newListItems, we are passing an array inside the mongodb.

each of these items we are looping through it has a name and id.

if we bind id to that value, when the checkbox is checked, we can find out what id is actually checked off.

now we hit save and refresh the page, we can see the id that is cheked inside our items.

now we can delete that particular item with the id.

we store the req.body.checkbox to a constant named checkedItemId.

```js
  const checkedItemId = req.body.checkbox;
```
and we use findByIdAndRemove() to delete the item

if we check the document inside mongoose, if we didn't provide a callback, the item won't actually delete even if we don't care there is any error.

```js
  Item.findByIdAndRemove(checkedItemId, function(err) {
    if(!err) {
      console.log("successfully deleted check item");
    }
  });
```

now we hit save and check the console. we can see the item is deleted.

if we use mongo shell to check, type in db.items.find(). 

we can see the item is longer existed.

to reflet the page, we need to res.redirect to the home route.

```js
Item.findByIdAndRemove(checkedItemId, function(err) {
    if(!err) {
      console.log("successfully deleted check item");
      res.redirect('/');
    }
  });
```


