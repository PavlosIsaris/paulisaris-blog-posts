---
title: "PHP 8 Is here! See what's new"
date: "2021-04-11"
tags: [web]
image: img/posts/whats_new_php_8.jpg
---

post photo by [pexels](https://www.pexels.com/)

# Introduction

**PHP 8 is finally here!**

PHP 8.0 is a major update of the PHP language.
It contains many new features and optimizations including **named arguments**, **union types**, **attributes**, **constructor property promotion**, **match expression**, **nullsafe operator**, **JIT**, and improvements in the **type system**, **error handling**, and **consistency**.

In this article, we will review all the new features and changes, and share some thoughts about each of the changes, as well as on the roadmap that PHP seems to be carving ahead.

You can read all about these in the [official release announcement](https://www.php.net/releases/8.0/en.php) as well.

# New features

## Named arguments

In PHP 8, when calling a function, you can omit the non-required arguments, and pass only what is desired.

### Named Parameters example

```php
function my_awesome_function(string $name, string $value = "", int $expires = 0) {
    ...
}
```

Let's say now that we want to call this function, but only specify the `$name` and `$expires` attributes.

In PHP 7:

```php
// calling the function. but we only want to specify $name and $expires
my_awesome_function('test', '', time() + 60 * 60 * 2)
```

In PHP 8:

```php
// calling the function. but we only want to specify $name and $expires
my_awesome_function(name: 'test', expires: time() + 60 * 60 * 2)
```

### My thoughts on named parameters

This is a nice and handy touch. Many languages nowadays support this kind of method calling.  
However, this design can lead to functions that break the [Single-responsibility_principle](https://en.wikipedia.org/wiki/Single-responsibility_principle), by resulting in methods that "do too much" by having many parameters. So, as always, use with caution ;-)

## Attributes

Attributes are the new kid in the block. It is essentially a configuration language embedded directly into code.  
Attributes is a **native PHP syntax** that offers the ability to add structured, machine-readable metadata information on declarations in code: Classes, methods, functions, parameters, properties, etc.

### Attributes Example

In PHP 7 (With PHPDocs):

```php
class BookController
{
    /**
     * @Route("/api/books/{id}", methods={"GET"})
     */
    public function get($id) { /* ... */ }
}
```

In PHP 8:

```php
class BookController
{
    #[Route("/api/books/{id}", methods: ["GET"])]
    public function get($id) { /* ... */ }
}
```

## Constructor property promotion

The basic idea is simple: ditch all the class properties and the variable assignments, and prefix the constructor parameters with `public`, `protected`, or `private`. PHP will take that new syntax, and transform it to normal syntax under the hood, before actually executing the code.

### Constructor properties example

In PHP 7:

```php
class Point {
  public float $x;
  public float $y;
  public float $z;

  public function __construct(
    float $x = 0.0,
    float $y = 0.0,
    float $z = 0.0
  ) {
    $this->x = $x;
    $this->y = $y;
    $this->z = $z;
  }
}
```

In PHP 8:

```php
class Point {
  public function __construct(
    public float $x = 0.0,
    public float $y = 0.0,
    public float $z = 0.0,
  ) {}
}
```

### My thoughts on Constructor property promotion

Constructor property promotion reduces the amount of code that is required, leading to smaller and cleaner classes.  
If you still want to have class properties that are not part of the constructor parameters, you can declare them in the old way, and instantiate them (or nor), inside the constructor.

## Union types

Union types are a way of declaring **multiple types** for a property/variable.
So if a function parameter can take either `string` or `int` values, you can now declare it as `string|int`.  
This is something that you could not do in PHP 7, only using PHPDocs (so it was not part of the PHP core libraries, but existed in the PHPDocs).   
Instead of PHPDoc annotations for a combination of types, you can use native union type declarations that are validated at **runtime**.

### Union types example

In PHP 7:

```php
class Book {
  /** @var int|float */
  private $price;

  /**
   * @param float|int $price
   */
  public function __construct($price) {
    $this->price = $price;
  }
}

new Book('test'); // OK at runtime
```

In PHP 8, since Union types are part of the PHP runtime library and compiler, this will throw an error:

```php
class Book {
  public function __construct(
    private int|float $price
  ) {}
}

new Book('test'); // TypeError
```

### My thoughts on Union types

As a [SOLID Principles](https://en.wikipedia.org/wiki/SOLID) advocate, I am not a big fan of this change.  
I can understand that it can help speed things up and adds a level of control over my code, but I believe that if your code gets **often** to the point that you need to use union type for a property, then there is definitely something you need to think about.

## Match expression

Match expression syntax is one of the nicest features in PHP 8 that improves the `switch` syntax in multiple ways.

Let's start by comparing the two. Here's a classic `switch` example (example from [this](https://stitcher.io/blog/php-8-match-or-switch) article):

In PHP 7 (using `switch`):

```php
switch ($statusCode) {
    case 200:
    case 300:
        $message = null;
        break;
    case 400:
        $message = 'not found';
        break;
    case 500:
        $message = 'server error';
        break;
    default:
        $message = 'unknown status code';
        break;
}
```

In PHP 8 (using `match`):

```php
$message = match ($statusCode) {
    200, 300 => null,
    400 => 'not found',
    500 => 'server error',
    default => 'unknown status code',
};
```

### My thoughts on the match expression feature

`match` will do **strict** type checks instead of **loose** ones. It's like using `===` instead of `==`.
In my opinion, this is a good chance, since it makes the code stricter and more expressive.  
Also, If you forget to check for a value, and when there's no default arm specified, PHP will throw an `UnhandledMatchError` exception.  
Again more strictness, but it will prevent subtle bugs from going unnoticed.  
To fix this, you should wrap the `match` expression inside a separate method, and deal with the `UnhandledMatchError` exception there, by returning a default value.

## Nullsafe operator

Instead of null check conditions, you can now use a chain of calls with the new nullsafe operator.  
When the evaluation of one element in the chain fails, the execution of the entire chain aborts, and the entire chain evaluates to null.

### Example of nullsafe operator

In PHP 7:

```php
$country =  null;

if ($book !== null) {
  $author = $book->author;

  if ($author !== null) {
    $address = $author->getAddress();
 
    if ($address !== null) {
      $country = $address->country;
    }
  }
}
```

In PHP 8:

```php
$country = $book?->author?->getAddress()?->country;
```

### My thoughts on nullsafe operator

I have mixed feelings about this.  
Of course, it is a handy touch since it leads to less and definitely more readable code.  
But again, by looking at the big picture, why would our code even ***need to check so deeply and greedily?***  
Should our code have Business rules that restrain such behavior?
So, again a handy new feature, but use with caution...

## Saner string to number comparisons

When comparing to a numeric string, PHP 8 uses a number comparison. Otherwise, it converts the number to a string and uses a string comparison.

In PHP 7:

```php
0 == 'foobar' // true
```

In PHP 8:

```php
0 == 'foobar' // false
```

This is definitely a step in the right direction, but only given that PHP is trying to be a stricter language.  
When we say equals, we should mean **equals**!

## My overall thoughts on PHP 8

PHP is undoubtedly trying to become a stricter, more "serious" language.  
As a big fan of [OOP](https://en.wikipedia.org/wiki/Object-oriented_programming) and [SOLID Principles](https://en.wikipedia.org/wiki/SOLID) principles, I am totally happy and on board with this direction.  
Also, as you will see in the upgrade guide, PHP 8 is generally backwards-friendly, since it does not break a lot of functionality from the previous major releases. Cool!  

So let's try to use all the new features of PHP as consciously as possible, and build awesome things!

If you are content with the new PHP version, take a look at the [migration guide](https://www.php.net/manual/en/migration80.php).  
What are your thoughts on PHP 8?
