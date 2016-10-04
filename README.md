# Life after PHP 5.4

---

## Hi, I'm Jelmer

![Sumo jelmer](img/Sumo_Jelmer.png)

:twitter: [@justCarakas](http://twitter.com/justcarakas)

:github: [carakas](http://github.com/carakas)

---

## I work at Sumocoders

---

# I'm a Fork core developer

---

## Topics

- Some gems from PHP 5.4

- What we skipped with PHP 5.5

- Along came PHP 5.6

- 5 + 1 = PHP 7

- Soon on your servers PHP 7.1

---

## Disclaimer

### Not all changes will be discussed

I just took some things out to highlight that I thought where interesting

---

## Some gems from PHP 5.4

- Traits

- Short array syntax

- Build in development web server

- Function array dereferencing `foo()[0]`

- ... 

---

## What we skipped with PHP 5.5 (1/2)

- Generators

- Try-catch was joined by finally

- Password hashing API

- List support added for foreach

---

## What we skipped with PHP 5.5 (2/2)

- New array function: array_column

- Class name resolution via ::class

- OPcache

- DateTimeImmutable

---

### Generators

`yield` vs `return` === `machine gun` vs `shotgun`

example: normal implementation of range

```php
foreach (range(0, 777777) as $i) {
    echo $i;
}
// peak usage 109.5 MB
```

example: generator implementation of range

```php
foreach (memoryFriendlyRange(0, 777777) as $i) {
    echo $i;
}
// peak usage 0.25 MB
```

---

### Generators

```php
function memoryFriendlyRange($start, $limit, $step = 1)
{
    if ($start < $limit) {
        if ($step <= 0) {
            throw new LogicException('Step must be positive');
        }

        for ($i = $start; $i <= $limit; $i += $step) {
            yield $i;
        }
    } else {
        if ($step >= 0) {
            throw new LogicException('Step must be negative');
        }

        for ($i = $start; $i >= $limit; $i += $step) {
            yield $i;
        }
    }
};
```

---

### Try-catch was joined by finally

Everything in the finally block will always be executed, also if we have an exception.

```php
function getLines($file) {
    $f = fopen($file, 'r');
    try {
        while ($line = fgets($f)) {
            yield $line;
        }
    } finally {
        fclose($f);
    }
}
```

---

### Password hashing API

- Bye bye `md5` en `sha1`

- Default library to help with the hashing of passwords

- `PASSWORD_DEFAULT` will always contain the most secure algorithm

- You can detect with `password_needs_rehash` if the algorithm has changed and rehash the password on login

- `password_get_info` can be used to check the used algorithm and the cost

---

### Password hashing API

```php
// Verify stored hash against plain-text password
if (password_verify($password, $hash)) {
    // Check if a newer hashing algorithm is available
    // or the cost has changed
    if (password_needs_rehash($hash, PASSWORD_DEFAULT)) {
        // If so, create a new hash, and replace the old one
        $newHash = password_hash($password, PASSWORD_DEFAULT);
    }

    // Log user in
}
```

---

### List support added for foreach

The only sad thing is that you can't use an associative array

```php
$profiles = [
    [
        1,
        'Jelmer',
        'jelmer@sumocoders.be',
    ],
    [
        2,
        'Stijn',
        'stijn@sumocoders.be',
    ],
];

foreach ($profiles as list($id, $name, $email)) {
    var_dump($id, $name, $email);
}
```

---

### New array function: array_column

```php
array_column (array $input, $column_key [, $index_key = null ])
```

```php
$profiles = [
    [
        'name' => 'Jelmer',
        'email' => 'jelmer@sumocoders.be',
    ],
    [
        'name' => 'Stijn',
        'email' => 'stijn@sumocoders.be',
    ],
];
$mailingList = array_column($profiles, 'email', 'name');

// true
$mailingList === [
  'Jelmer' => 'jelmer@sumocoders.be',
  'Stijn' => 'stijn@sumocoders.be'
];
```

---

### Class name resolution via ::class

```php
namespace Name\Space;
class ClassName {}

// Name\Space\ClassName
echo ClassName::class;
```
---

### OPcache

- Is your code not updating? => `opcache_reset()`

- Performance improvement by storing the parsed code in memory

---

### DateTimeImmutable

Exactly the same as DateTime only it is immutable

Calling the change method on this class will return a new instance instead of updating the current value

---

### Questions?

---

## Along came PHP 5.6

- Constant expressions

- Variadic functions via ...

- Argument unpacking via ...

- Exponentiation via **

- Default character encoding

---

### Constant expressions

Pre PHP 5.6

```php
const PATH_WWW = '/Sites/forkcms/demo';
const BACKEND_PATH = '/Sites/forkcms/demo/src/Backend';
const BACKEND_CORE_PATH = '/Sites/forkcms/demo/src/Backend/Core';
```

PHP 5.6 and up

```php
const PATH_WWW = '/Sites/forkcms/demo';
const BACKEND_PATH = PATH_WWW . '/src/Backend';
const BACKEND_CORE_PATH = BACKEND_PATH . '/Core';
```

And also arrays

```php
const ARR = ['a', 'b'];
```

---

### Variadic functions via ...

Pre PHP 5.6

```php
function hire($name) {
    $professions = array_slice(func_get_args(), 1);
    // rest of function
}
```

PHP 5.6 and up

```php
function hire($name, ...$professions) {
    // rest of function
}
```

---

### Argument unpacking via ...

```php
function addUser($id, $name, $email) {
    // rest of function
}

$user=[1, 'bob', 'bob@thebuild.er']
addUser(...$user);
```

---

### Exponentiation via **

```php
//true
8 === 2**3;

$bananas = 2;
$bananas **= 3;
// true
$bananas === 8;
```

---

### Default character encoding

UTF8 is now the default in php.ini for `default_charset`

It also will be used for the following functions if no character encoding is given

- `htmlentities()`

- `html_entity_decode()`

- `htmlspecialchars()`

- mbstring functions if the specific configuration is missing

---

### Questions?

---

## 5 + 1 = PHP 7

- Deprecated Static calls to non-static methods

- Scalar type declarations

- Return type declarations

- Null coalescing operator

- Spaceship operator

- Anonymous classes

- Group use declarations

---

### Deprecated Static calls to non-static methods

PHP 7 became a little less forgiving

Calling non-static methods statically has been deprecated

But then again, you shouldn't be doing it anyway

```php
class foo {
    function bar() {
        echo 'I am not static!';
    }
}

foo::bar();
```

---

### Scalar type declarations

---

### Return type declarations

PHPDoc used to tell us the return type

Now you can enforce it

```php
function getName(): string
{
    return $this->string;
}
```

---

### Null coalescing operator

```php
$username = isset($_GET['user']) ? $_GET['user'] : 'nobody';
```

Can now be written as

```php
$username = $_GET['user'] ?? 'nobody';
```

And it can be chained

```php
$username = $_GET['user'] ?? $_POST['user'] ?? 'nobody';
```

---

### Spaceship operator

```php
// Integers
echo 1 <=> 1; // 0
echo 1 <=> 2; // -1
echo 2 <=> 1; // 1

// Floats
echo 1.5 <=> 1.5; // 0
echo 1.5 <=> 2.5; // -1
echo 2.5 <=> 1.5; // 1
 
// Strings
echo "a" <=> "a"; // 0
echo "a" <=> "b"; // -1
echo "b" <=> "a"; // 1
```

---

### Anonymous classes

```php
$debugLogger = new class implements Logger {
    public function log(string $msg) {
        echo $msg;
    }
};
$app = new Application($debugLogger);
```

---

### Group use declarations

Pre PHP 7

```php
use some\namespace\ClassA;
use some\namespace\ClassB;
use some\namespace\ClassC as C;
```

PHP 7 and up

```php
use some\namespace\{ClassA, ClassB, ClassC as C};
```

---

### Questions?

---

## Soon on your servers PHP 7.1 (1/2)

- Too few arguments exception

- Nullable types

- Void functions

- Class constant visibility

---

## Soon on your servers PHP 7.1 (2/2)

- Multi catch exception handling

- Symmetric array destructuring

- Support for keys in list()

- Support for negative string offsets

---

### Too few arguments exception

PHP is a forgiving language but...

warning bumped up to error: ArgumentCountError 

---

### Nullable types

```php
function nullableReturn(): ?string
{
    return null;
}
```
```php
function nullableParameter(?string $parameter)
{
    // rest of function
}
```

---

### Void functions

```php
function blackHole(...$whateverComesMyWay): void
{
    return;
}
```

##### warning: returning null will give an error

---

### Class constant visibility

```php
class ConstDemo
{
    const PUBLIC_CONST_A = 1;
    public const PUBLIC_CONST_B = 2;
    protected const PROTECTED_CONST = 3;
    private const PRIVATE_CONST = 4;
}
```

---

### Multi catch exception handling

```php
try {
    // some code
} catch (FirstException $e) {
    // some code to handle this
} catch (SecondException $e) {
   // exactly the same :(
}
```
Can now be written like this
```php
try {
    // some code
} catch (FirstException | SecondException $e) {
    // some code to handle this
}
```

---

### Symmetric array destructuring

Just a fancy way to say that
```php
list($id, $name) = [1, 'bob'];
```
can now be used as
```php
[$id, $name] = [1, 'bob'];
```

---

### Support for keys in list()

Remember list not working with associative arrays?

**They fixed it**

```php
$profiles = [
    [
        'name' => 'Jelmer',
        'email' => 'jelmer@sumocoders.be',
    ],
    [
        'name' => 'Stijn',
        'email' => 'stijn@sumocoders.be',
    ],
];

foreach ($profiles as ['name' => $name, 'email' => $email]) {
    var_dump($name, $email);
}
```

---

### Support for negative string offsets

You could already do this

```php
// true
'b' === 'abc'[1];
```

But now they also added

```php
// true
'b' === 'abc'[-2];
```

---

### Questions?

---

## Thank you!

![Thanks](img/thanks.png)

---

## Resources

- http://php.net
