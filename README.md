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

- New array function: array_column

---

## What we skipped with PHP 5.5 (2/2)

- Class name resolution via ::class

- OPcache

- Non-scalar keys in foreach

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

## Questions?

---

## Thank you!

![Thanks](img/thanks.png)

---

## Resources

- http://php.net
