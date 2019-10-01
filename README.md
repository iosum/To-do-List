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