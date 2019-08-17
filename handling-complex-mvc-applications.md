---
title: "Handling complex MVC applications - How to scale and avoid Controller chaos"
date: "2018-10-27"
tags: [object-oriented-design, technical-debt, architecture, clean-code]
image: img/posts/complex_mvc.jpg
---

*This article uses [Laravel](https://laravel.com/) for the code snippets, but the paradigm can be easily adapted to every other MVC framework out there.*

*To make things more interesting, we will lay this article out by posting an imaginary conversation between 2 professionals:*

**Stan**, a seasoned developer, who has made many architectural mistakes (but thankfully seems to be learning from them), and

  
**Ollie**, a novice developer, who just started delving into the world of serious programming and has some simple applications.

Here is how the conversation went:

# Introduction

**[Stan]** Hey Ollie! Today I would like to talk to you about [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller). MVC is a popular Architectural Pattern for building robust applications.

  

**[Ollie]** Wait wait wait… MVC? What is that?

  

**[Stan]** MVC stands for [Model - View - Controller](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller). It essentially defines a strategy for robust code design. It breaks down the application to different parts, in order to achieve higher separation of concerns between the app's modules.
**Having modularized code helps a lot when trying to add more functionality or maintaining it.**
Let’s see the following picture:

![MVC framework layout](https://upload.wikimedia.org/wikipedia/commons/a/a0/MVC-Process.svg)
###### *Image taken from [Wikipedia](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)*

**[Stan]** So, the user interacts with the View, and upon each interaction, relevant Controller methods are called, who then are responsible for updating/ fetching the appropriate data back to the user (again by using the View layer).

**[Ollie]** Gotcha! I have already used MVC in many projects. I know a ton about it!

## The typical code example

**[Stan]** Not too fast, Ollie, there is a lot more to cover.
Now, let’s see a Laravel Controller method in a typical tutorial that we usually stumble upon:

```

<?php  
namespace App\Http\Controllers;  

class HomeController extends Controller {  
  
  public function simpleMethod() {  
  
	$books = Books:all();
	foreach($books as $book) {
		// some business logic here...
	}
    return view('home.home')->with(['books' => $books]);  
  }  
}

```

**[Ollie]** OK, this code seems pretty straightforward!

**[Stan]** What if we could make in better by breaking it down into individual components?

**[Ollie]** Why on earth should we wanna do that? I like that code; simple and it does the job. What is so special about it?

**[Stan]** There is something very special about it, my young Padawan! It’s called “Complex Applications”.
See, in a relatively small project, it is perfectly fine to have everything in a Controller method.

**[Ollie]** Wait wait… what do you mean when saying everything…?

## Layers to the rescue

**[Stan**] I sense that you fail to understand how most applications work… In a typical MVC application, we have the following layers:

-   Validation Layer
    
-   Business Logic Layer
    
-   Database/ Repository Layer
    
-   Error Handling Layer
    
-   Success/ Error displaying layer

To be honest, an MVC application can be broken down further into even more layers, but these are the most basic ones. It’s perfectly fine if most tutorials omit them. It’s not their job to explain how to layer an application, but rather explain the topic that the tutorial is about.

**[Ollie]** OK… So why should I break down my application? If I want to add some functionality, I can do it in my Controller method.

## Rotten code over time

**[Stan]** Of course you can go ahead and do this, but at some point it will result in code duplication , extremely large Controller methods and classes, and rotten code in general.

This is why I said that layering down works better in complex applications.

If you don’t plan to scale your project, then you may be fine leaving everything in your Controller method.

As an application grows, it becomes harder to maintain. As complexity increases, the value of reusable modules increases as well. We know that we have to do something about it before we risk technical debt, which in turn will result in harder maintenance and further development.

Let’s see the code from the previous example, after some time of adding more functionality;

```php

<?php  
namespace App\Http\Controllers;  

class BooksController extends Controller {

    public function complexMethod(HttpRequest $request) {
    
        $authorId = $request->author_id;
        if(!Author::find($authorId))
            // wrong data
            return back()->with('error','Wrong data');
        try {
            
            // this DB query needs to be duplicated if we want
            // to use it in another part of the code
            $books = Books::with('autor')
            ->with('reviews')->where(['author_id' => $authorId])
            ->get();
            
            foreach($books as $book) {
                // some business logic here...
                // this code snippet can turn out to be huge,
                // since it grows with the application complexity.
            }
            
            return view('home.home')->with(['books' => $books]);
        } catch (Exception $e) {
            return back()->with('error', $e->getMessage());
        }
    }
}


```

**[Ollie]** Gee, you are right! I want my applications to be able to scale! How can I separate my code into more layers?

  

**[Stan]** I thought so. Essentially, a Controller’s job is to interact with the View (the V from MVC) layer. **This means that it should handle the user input, and serving back the data to be displayed to the user.**
  
**Nothing more, nothing less.**

## Who is responsible for each layer?

Having said that, let’s revise the layers and see which of them should remain in the Controller and which should be broken apart:

-   **Validation Layer**
	This layer is responsible for validating data entered by the user. In the MVC diagram, it exists very close to the  View layer, so it should be implemented in the Controller class.

-   **Business Logic Layer**
	This layer is usually the one that gets too complicated over time. It defines the Business rules of the application and has nothing to do with the View. So, we need to decouple it from the Controller and package it into another Class in the Business Logic Layer. 

-   **Database/ Repository Layer**
	This layer includes the DB queries of the application. In many complex applications that are data intensive (such as real-time systems), this layer may also be a different application by itself. So it should not be implemented in the Controller, but in another Class living in the Database/ Repository Layer.
	
-   **Error Handling Layer**
	What should we do when an Exception is thrown? It depends. Maybe we want to Log the Exception into a logging channel and take a special action.
	In most MVC applications we want to inform the User about the error, so this layer should be implemented **both** in the Business Logic layer and the Controller layer.
	
-   **Success/ Error displaying layer**
	This layer is coupled with the previous one. When an operation is successful, or when an Exception has been thrown, it is of big importance to inform the User accordingly. This layer is defined between the Controller and the View, and can be implemented in the Controller Class.

**[Ollie]** Wow, I learnt so much! But I am still a bit confused; How should my Controller look like now?

## Layered Code


**[Stan]** Cool question! Look at the following example:

```php

<?php
namespace App\Http\Controllers;

class BooksController extends Controller {

  protected $bookManager;

  function __construct() {
	  $this->bookManager = new BookManager();
  }

  public function complexMethod(HttpRequest $request) {
	  // VALIDATION LAYER
	  // having all rules in a separate validationRules method
	  // allows reusage
	  $validator = Validator::make($request->all(), $this->validationRules($request));

	  if ($validator->fails()) {
	      return back()->with('error','Wrong data');
	  }

	  try {
	      // BUSINESS LOGIC LAYER
	      $books = $this->bookManager->getAllBooksForAuthor($request->author_id);

	      // SUCCESS DISPLAY LAYER
	      return view('home.home')->with(['books' => $books]);
	  } catch (Exception $e) {
	        //ERROR DISPLAY LAYER
		return back()->with('error', $e->getMessage());
	  }

  }

}
```

**[Ollie]** But where are the Business Logic methods and the Database/ Repository layer methods?

**[Stan]** Defined in other classes, of course! One of the best things about Object Oriented Programming is the ability to package different modules into separate classes and use them by creating instances of those classes.
(See the constructor in the last example).

Let's see our `BookManager class`:

```php

<?php  
namespace App\BusinessLogicLayer\;
use App\StorageLayer\BookRepository;

class BookManager {
	
	protected $bookRepository;
	const PUBLISHED_BOOK_STATE = 1;
	
	public function __construct() {
		$this->bookRepository = new BookRepository();
	}

	public function getAllBooksForAuthor($authorId) {
		// here we can add all the business logic:
		// for example, we can check whether the author has any 
		// books that are in a DRAFT state, or we can check if the
		// author is the same user as the logged in user, in order
		// to display more data.
		
		$books = $this->booksRepository->getAllBooks([
			'state' => self::PUBLISHED_BOOK_STATE,
			'author_id' => $authorId
		]);	  
		
		foreach($books as $book) {
			// business logic here
		}

		return $books;
	}
}
```

**[Ollie]** But I don't see any DB queries here, either.

**[Stan]** Exactly! The DB queries are in Repository/ Storage layer, remember? Look at the following class:

```php

<?php 
  
namespace app\StorageLayer;
use app\models\Book;

class BookRepository {

	public function getAllBooks($attributesArray) {  
		return Book::where(attributesArray);  
	}

}
```

## Thinking ahead


**[Ollie]** Isn't this class very small? Can't we just omit it and include the DB query to the Business Logic Layer?

**[Stan]** Not if we want to scale correctly. Remember, as your project grows, there might be a need to have complex DB queries in our class or even transfer the Repository layer in a totally separate project (even in a different server).


**[Ollie]** Wow, I really haven't thought of all these! You are right. This code looks clean and scalable. I guess it makes adding more functionality way easier.

  

**[Stan]** Exactly. In complex applications, it is essential to follow the [Open-closed principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle):

*"software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification"*

  
So, even if you have written a Controller method a long time ago and want to write another one that reuses some of the layers defined in other classes, you can simply call the relevant methods in the new Controller method.

  

**[Ollie]** That is so cool! I feel ready to scale now!


![Scale Projects Meme](https://i.imgflip.com/2ku5ql.jpg)


![enter image description here](https://i.imgflip.com/2ku5ur.jpg)