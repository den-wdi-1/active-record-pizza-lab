# ActiveRecord Methods and Finders

<!-- 1:00 10 mins -->

<!--Hook: So we know how to do DB migrations now.  We also know how to model out our data.  This class is about one thing, and one thing only: practice, practice, practice.  So without further ado, let's get to CRUD with Active Record.-->

### Objectives
*After this lesson, students will be able to:*
- **Query** a model using AR methods
- **Instantiate** and **create** a new instance of a model
- **Edit** a model's attributes
- **Destroy** a model

### Preparation
*Before this lesson, students should already be able to:*
- **Write** a model that inherits from ActiveRecord
- **Instantiate** a new instance of a class
- **Describe** the structure of SQL tables, rows and columns

## Intro

So now that you've looked at what SQL can do, and you've created Ruby classes from scratch, and even models that combine the two using ActiveRecord – it's time to see all the convenience and power that this library gives you. Today we're CRUDing.

You'll notice you have some starter code waiting for you. As much as we love torturing you by making you build everything from scratch yourself, we're giving you a starter app today that's pretty similar to what you learned in the last module, so you can focus on these concepts.

If you struggled with models and migrations or feel like what we have here isn't something you can do on your own just yet, that's okay – you just need to practice tonight.

Our example app today is for a pizza shop – Luigi's Pizza Bazaar. We'll be making _pizza_. With _code_. Take 5 minutes and familiarize yourself with what you have in the starter folder.

Don't forget to `bundle install`, `rake db:create`, and `rake db:migrate`

<!-- 1:10 5 mins -->

## Investigate Starter Code

<!-- Play some pizza music. -->

> 5 minute investigatory break.

> Note: Today we're using `tux`, which gives us an interactive shell just like irb and pry, but customized to include our Sinatra application. Certainly not the only kid on the block, but it'll prove useful to mess around with our code in an irb-like setting.

<!-- 1:15 10 mins -->

## Create - Codealong

One of the top four coolest parts of CRUD, create is when we use our model like a blueprint to make instances of the model. This is where we start filling up our database with rows of new records. Exciting stuff, so let's do this.

Let's jump (```cd```) into our pizza app and jump into our tux shell.  Now, we've made new instances before. Who remembers?

```ruby
p1 = Pizza.new
#<Pizza id: nil, name: nil, sauce: nil, cheese: nil, mushrooms: nil, extra_toppings: nil, created_at: nil, updated_at: nil>
```

That's cool, but not much good when it's all empty. Let's overwrite `p1` with a pizza that has more substance.

```ruby
p1 = Pizza.create({name: "Art Lover", sauce: "red sauce", cheese: true, mushrooms: false, extra_toppings: "artichokes, chopped garlic"})
```

DID YOU SEE THAT? ActiveRecord just took our ruby method (`.create`) and converted it to SQL for us! It saved it to our table as a new row, with all those attributes – all we had to do was pass it a hash.

> Just so you know, ActiveRecord also gives us setter methods for each of our attributes, so we could just as easily say `p1.cheese = false`. Then we'd just have to save it with `p1.save`.

<!--1:25 5 mins -->

## Create - Independent Practice

Take five minutes to create at least three more pizzas. Make them with different options – try passing in just some of our attributes, trying using setter methods and `.save`. Get a handful of records in your database, we'll use them in the next section.

<!--1:30 15 minutes -->
## Read - Codealong

Sweet, now that we have some pizzas in our database, how do we find them again?

Well, let's start easy:

```ruby
Pizza.all
```

While technically it comes back as an "ActiveRecord Relation", it's a thin layer on top of an array, and so you could easily loop through all your pizzas and do something with each one.

What else falls under "read"?

**How about finding one particular pizza?** We hinted earlier this week at this, but ActiveRecord automatically gives each row an 'id' column and auto-increments ids for us. Which means that **a)** you should never define an id for a record, let the library do it, and **b)** _every record has it's own unique id_.

So what if we want to find Pizza #3?

```ruby
Pizza.find(3)
```

Easy as that. There are a few other easy methods you might want to know exist –

```ruby
Pizza.first
Pizza.last
```

#### The Real Querying - Where

Now, before we can move on to the **UD** in CRUD, there's a big READ we need to learn about: querying. Querying, by definition, is just a way to ask questions of our database.

Like, "Find me any pizzas where there's no cheese":

```ruby
Pizza.where(cheese: false)
```

Or, "Find me any pizzas where there's cheese **and** the sauce is called 'marinara'":

```ruby
Pizza.where(cheese:true, sauce: 'marinara')
```

Both of those get you back an array, because a `where` query is asking for _any_ records it finds. You could combine this with `.first`, `.last`, a `.each` loop, or whatever you need.

#### The Real Querying - Find

While _where_ returns you an array, _find_ returns you one item – or a 'not found' error.

```ruby
Pizza.find(794)
# ActiveRecord::RecordNotFound: Couldn't find Pizza with 'id'=794
```

Ruby's errors are actually super helpful if you read them. But let's see some that work. Obviously we just learned:

```ruby
Pizza.find(2)
```

But how about something more interesting:

```ruby
Pizza.find_by(cheese: true)
```

Now, this is interesting – it does return one, and not an array. It returns the first that it finds. If we try this, we'll see there's _more_ than one:

```ruby
Pizza.where(cheese: true).count # => 3
```

So be mindful of when you're using `where` and when you're using `find`. Both are super useful, you just have to know the difference.

<!--1:45 5 minutes -->

## Read - Independent Practice

Take five minutes again just to practice finding and querying the pizzas you've created. Try different queries, querying multiple fields, finding based on different attributes and ids. Just see what happens for a couple minutes.

<!--1:50 5 minutes -->

## Update - Codealong

Now, the wonderful act of updating a record we know exists. Let's start by grabbing one and throwing it in a variable.

```ruby
p = Pizza.last
```

Updating is easy, and we can do it in two ways, similar to our new/create earlier. The verbose way:

```ruby
p.name = "The Anchovy Explosion"
p.extra_toppings = "Anchovies, anchovies, anchovies"
p.save
```

Or, we could wrap it up in a convenient single command, passing in a hash of what we want to change:

```ruby
p.update_attributes(name: "The Anchovy Explosion", extra_toppings: "Anchovies, anchovies, anchovies")
```

Both will do the trick, and there's no significant difference between them.

<!-- 1:55 5 mins -->

## Update - Independent Practice

Now, take five minutes to practice updating on your own. Use both methods, see which you prefer. Try changing multiple attributes and one attribute.

<!--2:00 5 minutes -->

## Destroy - Codealong

Finally, the most devastating operation, destroying. Hopefully this one's not too surprising, because there's not much else you can do besides get rid of something:

```ruby
p.destroy
```

That'll get rid of it from our database, forever.

Now, you might be tempted to call this 'delete' instead of 'destroy' – in ActiveRecord, that distinction matters. We aren't going to get too deep into it, you're welcome to research it on your own, but the difference is this:

- **Destroy**, the one you will want 99.99% of the time, gets rid of a record, and calls callbacks
- **Delete**, which you'll probably not need to use, gets rid of a record, and _doesn't_ call callbacks

Similar to the concept of JavaScript's callbacks, ActiveRecord lets us run methods if we want to when certain events happen. That's a little too deep for right now, but with a little research, you can find out plenty about it.

If you're having trouble grasping the difference, just remember: use '.destroy'.

<!--2:05 5 minutes -->

## Destroy - Independent Practice

Now, take a couple minutes to practice destroying on your own. Get rid of a couple of those pizzas the customers just aren't buying.

Once this is done, try one more "Create" action, one more "Read" action, and one more "Update" on your DB to leave a pristine pizza menu.

<!--2:10 5 minutes -->

## Conclusion
- What are two ways to create a new instance of a model? Can you imagine when one might be more convenient than the other?
- What are the two ways you can update a model? Can you imagine when one might be more convenient than the other?
- What's the difference between what you get back when querying with `where` and `find`?
- How do you find a certain instance of a model?
- How do you delete a model from the database?


## Licensing
All content is licensed under a CC­BY­NC­SA 4.0 license.
All software code is licensed under GNU GPLv3. For commercial use or alternative licensing, please contact legal@ga.co.
=======
# ActiveRecord Models

## Challenge

Your task is to write a RESTful routes & controllers for our Pizza Shop and fill in each with _actual code_. Earlier this week we built a route-controller skeleton with Sinatra, and now it's time to fill it out.

Later, we'll be demonstrating how to use HTML forms to send data from user input to our controller actions, which will be the final piece of the puzzle for creating a full blown web application. Because of that, there are a few important details to keep in mind.

* You don't have to create the controller actions for `new` and `edit`. Skip those for now (unless you want to jump ahead on your own).
* Take in a JSON body for your `create` and `update` actions. We'll get those to be real data (i.e. added by your user) later.
* Ruby, unlike Javascript, cannot natively parse JSON.  To get all your actions running smoothly, you should require the `json` library for Ruby. Look up the `.to_json` and `JSON.parse` methods for this library, they will come in handy for all your routes.
* Definitely use `tux` to test out if your code works *before* placing it inside your controller!!
* Feel free to test out your routes with the [`curl`](http://conqueringthecommandline.com/book/curl#cid25) command (use `localhost:9292` as the url you'll hit) or Postman.
    * Tip: [how to send a POST request with curl, using parameters!](http://conqueringthecommandline.com/book/curl#uid105)

> In the last 10 minutes, we'll walk through a solution example so you can gauge how you did!

####Requirements

- Write a RESTful controller for our pizza resource with `index`, `show`, `create`, `update`, and `delete` routes. You should be using (but are not limited to) the following ActiveRecord methods:

  - `.find`
  - `.all`
  - `.new`
  - `.save`
  - `.update_attributes`
  - `.destroy`

- Fill in each controller action with the CRUD operation that is appropriate for that action
- Don't forget about `params` and `request.body`

#### Starter Code

The same code as our lesson is included in the `starter-code` folder. The Sinatra setup is ready to go, you just need to fill in your routes and controller logic.

#### Deliverable

Shoot to create a complete RESTful controller with appropriate ActiveRecord methods inside.

You'll get a lot more practice with this so don't sweat it if you're struggling, but if you are:

* Don't freeze up, try things out
* Read all errors carefully
* Identify your knowledge gaps
* Formulate a question
* ASK THAT QUESTION!

###Resources

- [Rails Guides: ActiveRecord](http://guides.rubyonrails.org/active_record_basics.html)


## Licensing
All content is licensed under a CC­BY­NC­SA 4.0 license.
All software code is licensed under GNU GPLv3. For commercial use or alternative licensing, please contact legal@ga.co.
