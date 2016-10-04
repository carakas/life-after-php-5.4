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

- list support added for foreach

- New array function: array_column

---

## What we skipped with PHP 5.5 (2/2)

- array and string literal dereferencing

- Class name resolution via ::class

- OPcache was added

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

## Questions?

---

## Thank you!

![Thanks](img/thanks.png)

---

## Resources

- http://php.net
