# Golang vs PHP: Syntax Comparison

A comprehensive cheat-sheet comparing Go and PHP (8.x) syntax — fundamentals, OOP, type systems, error handling, concurrency, and modern features.

## Table of Contents

* [Naming Convention](#naming-convention)
* [Namespace & Package](#namespace--package)
* [Import & Use](#import--use)
* [Basic Types](#basic-types)
* [Type Hints & Strict Mode](#type-hints--strict-mode)
* [Custom Type](#custom-type)
* [Enum](#enum)
* [Variable](#variable)
* [Function / Method](#function--method)
* [If Statement](#if-statement)
* [Loops](#loops)
* [Switch & Match](#switch--match)
* [Class & Struct](#class--struct)
* [Interface](#interface)
* [Traits (PHP only)](#traits-php-only)
* [Pointer & Reference](#pointer--reference)
* [Array & Slice](#array--slice)
* [Map / Associative Array](#map--associative-array)
* [Null Handling](#null-handling)
* [Error Handling](#error-handling)
* [Closures & Anonymous Functions](#closures--anonymous-functions)
* [Generics](#generics)
* [Type Casting & Conversion](#type-casting--conversion)
* [String Operations](#string-operations)
* [Concurrency](#concurrency)
* [Memory Management](#memory-management)
* [Spread Operator & Variadic](#spread-operator--variadic)
* [Named Arguments](#named-arguments)
* [Attributes & Struct Tags](#attributes--struct-tags)
* [First-Class Callables (PHP 8.1)](#first-class-callables-php-81)
* [Readonly Properties (PHP 8.1+)](#readonly-properties-php-81)
* [Constructor Promotion (PHP 8.0+)](#constructor-promotion-php-80)

---

## Naming Convention

| | Go | PHP |
|---|---|---|
| Local variables | `camelCase` | `$camelCase` |
| Exported / Public | `PascalCase` | `PascalCase` (no sigil) |
| Unexported / Private | `camelCase` | `camelCase` (visibility keyword) |
| Constants | `PascalCase` or `ALL_CAPS` | `ALL_CAPS` |
| Types / Structs / Classes | `PascalCase` | `PascalCase` |
| Functions | `camelCase` | `camelCase` or `snake_case` |
| Interfaces | `PascalCase` (often `-er` suffix) | `PascalCase` (often `Interface` suffix) |

In **Go**, visibility is controlled by casing: uppercase first letter = exported (public), lowercase = package-private. There are no `public`/`private` keywords.

In **PHP**, variables always carry a `$` sigil. Class members use explicit visibility keywords (`public`, `protected`, `private`). Top-level functions and constants have no sigil.

```go
// Go: casing determines visibility
var ExportedVar = 1   // public
var unexportedVar = 2 // package-private
```

```php
<?php
// PHP: sigil on variables, keywords on class members
$localVar = 1;

class Foo {
    public int $pub = 1;
    private int $priv = 2;
}
```

> **Sources:**
> - **Go:** [Effective Go — Names](https://go.dev/doc/effective_go#names) · [Go Spec — Exported identifiers](https://go.dev/ref/spec#Exported_identifiers)
> - **PHP:** [PHP Manual — Variables](https://www.php.net/manual/en/language.variables.php) · [PSR-1 Coding Standard](https://www.php-fig.org/psr/psr-1/)

---

## Namespace & Package

**Go** — every file belongs to a package, declared at the top:
```go
package main

package myapp
```

**PHP** — namespaces are optional; declared with `namespace`:
```php
<?php
namespace App\Http\Controllers;

namespace MyApp\Utils;
```

| | Go | PHP |
|---|---|---|
| Declaration | `package name` | `namespace A\B\C;` |
| Separator | `/` (file path) | `\` (backslash) |
| Multiple per file | No | No |
| Nested | Via directories | `A\B\C` |
| Aliases | `import foo "pkg"` | `use A\B\C as Alias;` |

> **Key difference:** Go packages are tied to directory structure — one directory, one package. PHP namespaces are independent of the filesystem (though PSR-4 aligns them with directories).

> **Sources:**
> - **Go:** [A Tour of Go — Packages](https://go.dev/tour/basics/1) · [Go Spec — Packages](https://go.dev/ref/spec#Packages)
> - **PHP:** [PHP Manual — Namespaces](https://www.php.net/manual/en/language.namespaces.php)

---

## Import & Use

**Go:**
```go
import "fmt"
import "os"

// Grouped (preferred)
import (
    "fmt"
    "log"
    "os"

    "github.com/user/pkg" // third-party
)

// Alias
import f "fmt"

// Blank import (side effects only)
import _ "database/sql/driver"
```

**PHP:**
```php
<?php
// Autoloading handles file inclusion — no explicit import needed for classes
// use brings names into the current namespace scope

use App\Models\User;
use App\Services\{AuthService, PaymentService}; // grouped
use App\Http\Controllers\UserController as UC;  // alias
use function App\Utils\formatDate;              // import a function
use const App\Config\MAX_RETRIES;               // import a constant

// Old-style file inclusion (avoid in modern PHP)
require_once 'vendor/autoload.php'; // Composer autoloader
include 'helpers.php';
```

> **Key difference:** Go imports resolve at compile time; unused imports are compile errors. PHP uses autoloaders (Composer/PSR-4) — `use` is a namespace alias, not a file load. PHP never errors on unused `use` statements.

> **Sources:**
> - **Go:** [A Tour of Go — Imports](https://go.dev/tour/basics/2)
> - **PHP:** [PHP Manual — use keyword](https://www.php.net/manual/en/language.namespaces.importing.php) · [Composer Autoloading](https://getcomposer.org/doc/01-basic-usage.md#autoloading)

---

## Basic Types

| Category | Go | PHP 8.x |
|---|---|---|
| **Boolean** | `bool` | `bool` |
| **Integer** | `int`, `int8`…`int64`, `uint`, `uint8`…`uint64` | `int` (platform-width, typically 64-bit) |
| **Float** | `float32`, `float64` | `float` (always 64-bit double) |
| **String** | `string` (UTF-8 byte sequence) | `string` (byte sequence, not inherently UTF-8) |
| **Null** | `nil` (for pointers, interfaces, maps, slices, channels, funcs) | `null` (its own type `null`) |
| **Byte** | `byte` (alias `uint8`) | no separate byte type |
| **Rune** | `rune` (alias `int32`) | no rune type |
| **Complex** | `complex64`, `complex128` | no complex number type |
| **Mixed** | `any` (alias `interface{}`) | `mixed` |
| **Void** | (no return type) | `void` |
| **Never** | no equivalent | `never` (function never returns) |
| **Iterable** | no equivalent | `iterable` (array or Traversable) |
| **Callable** | `func(...)` type | `callable` |

```go
// Go zero values
var b bool    // false
var i int     // 0
var f float64 // 0.0
var s string  // ""
var p *int    // nil
```

```php
<?php
// PHP default values
$b = false;
$i = 0;
$f = 0.0;
$s = '';
$p = null;

// PHP type juggling — types are coerced automatically
var_dump(0 == "foo"); // bool(false) in PHP 8 (was true in PHP 7!)
var_dump(0 == "");    // bool(false) in PHP 8
var_dump("1" == "01"); // bool(true) — numeric string comparison
```

> **Key difference:** Go is statically typed — every variable has a fixed type at compile time. PHP is dynamically typed — variables can change type at runtime. PHP 8 tightened many comparison coercions that existed in PHP 7.

> **Sources:**
> - **Go:** [A Tour of Go — Basic types](https://go.dev/tour/basics/11) · [Go Spec — Types](https://go.dev/ref/spec#Types)
> - **PHP:** [PHP Manual — Types](https://www.php.net/manual/en/language.types.php) · [PHP 8.0 Migration — Type system changes](https://www.php.net/manual/en/migration80.incompatible.php)

---

## Type Hints & Strict Mode

PHP 8 has a rich optional type system. Go types are always mandatory.

```php
<?php
declare(strict_types=1); // enforce strict type checking in this file

// Scalar type hints
function add(int $a, int $b): int {
    return $a + $b;
}

// Nullable type
function find(?int $id): ?User { ... }

// Union types (PHP 8.0+)
function process(int|string $value): int|float { ... }

// Intersection types (PHP 8.1+) — all types must be satisfied
function log(Stringable&Countable $obj): void { ... }

// Return type void and never
function doNothing(): void {}
function fail(): never { throw new Exception("always throws"); }

// mixed — any type, explicit opt-out of type checking
function anything(mixed $x): mixed { return $x; }

// Typed properties (PHP 7.4+)
class User {
    public int $id;
    public string $name;
    public ?Address $address = null; // nullable property
}

// Static analysis generics via docblocks (no runtime enforcement)
/** @template T */
class Collection {
    /** @var array<T> */
    private array $items = [];

    /** @param T $item */
    public function add(mixed $item): void {
        $this->items[] = $item;
    }

    /** @return T|null */
    public function first(): mixed {
        return $this->items[0] ?? null;
    }
}
```

> **Key difference:** Go types are always enforced at compile time with no declaration needed. PHP types are optional annotations enforced at runtime (with `declare(strict_types=1)`) or purely by static analysis tools like PHPStan/Psalm.

> **Sources:**
> - **PHP:** [PHP Manual — Type declarations](https://www.php.net/manual/en/language.types.declarations.php) · [PHP Manual — strict_types](https://www.php.net/manual/en/functions.arguments.php#functions.named-arguments)

---

## Custom Type

**Go:**
```go
// Distinct type (not just alias — not interchangeable with underlying type)
type Age int16

// Type alias (same type, interchangeable)
type MyInt = int

// Struct
type Person struct {
    Name string
    Age  Age
}

// Method on struct
func (p Person) GetAge() Age {
    return p.Age
}

// Interface (implicit — no "implements" keyword)
type Ager interface {
    GetAge() Age
}

func PrintAge(a Ager) {
    fmt.Println("Age:", a.GetAge())
}
```

**PHP:**
```php
<?php
// PHP has no distinct scalar types — use classes or enums instead

// Class as a custom type
class Age {
    public function __construct(
        private readonly int $value
    ) {
        if ($value < 0) throw new \InvalidArgumentException("Age cannot be negative");
    }

    public function getValue(): int { return $this->value; }
}

// DTO / Value Object pattern
class Person {
    public function __construct(
        public readonly string $name,
        public readonly Age $age,
    ) {}

    public function getAge(): Age { return $this->age; }
}

// Interface (explicit — must declare "implements")
interface Ageable {
    public function getAge(): Age;
}

class Employee implements Ageable {
    public function __construct(private Age $age) {}
    public function getAge(): Age { return $this->age; }
}

function printAge(Ageable $a): void {
    echo "Age: " . $a->getAge()->getValue() . PHP_EOL;
}
```

> **Key difference:** Go achieves polymorphism through implicit interface satisfaction (structural typing). PHP uses nominal typing — a class must explicitly declare `implements InterfaceName`. Go's `type Age int` creates a genuinely distinct type; PHP has no equivalent for scalars.

> **Sources:**
> - **Go:** [Go Spec — Type declarations](https://go.dev/ref/spec#Type_declarations) · [Effective Go — Interfaces](https://go.dev/doc/effective_go#interfaces)
> - **PHP:** [PHP Manual — Classes and Objects](https://www.php.net/manual/en/language.oop5.php) · [PHP Manual — Interfaces](https://www.php.net/manual/en/language.oop5.interfaces.php)

---

## Enum

**Go** — uses `iota` inside a `const` block; no first-class enum type:
```go
type OS int

const (
    MacOS   OS = iota // 0
    Windows           // 1
    Linux             // 2
)

// Bit flags
const (
    Read    = 1 << iota // 1
    Write               // 2
    Execute             // 4
)

// Stringer pattern (common idiom)
func (o OS) String() string {
    switch o {
    case MacOS:   return "macOS"
    case Windows: return "Windows"
    case Linux:   return "Linux"
    default:      return "Unknown"
    }
}
```

**PHP 8.1+** — first-class `enum`:
```php
<?php
// Pure enum (no backing type)
enum Status {
    case Active;
    case Inactive;
    case Pending;
}

$s = Status::Active;

// Backed enum — each case has a scalar value (string or int)
enum Color: string {
    case Red   = 'red';
    case Green = 'green';
    case Blue  = 'blue';
}

// From backing value
$c = Color::from('red');         // Color::Red — throws ValueError if not found
$c = Color::tryFrom('unknown');  // null — no exception

echo $c->value; // 'red'
echo $c->name;  // 'Red'

// All cases
$cases = Color::cases(); // array of Color instances

// Enums can implement interfaces
interface HasLabel {
    public function label(): string;
}

enum Suit: string implements HasLabel {
    case Hearts   = 'H';
    case Diamonds = 'D';
    case Clubs    = 'C';
    case Spades   = 'S';

    public function label(): string {
        return match($this) {
            Suit::Hearts   => 'Hearts ♥',
            Suit::Diamonds => 'Diamonds ♦',
            Suit::Clubs    => 'Clubs ♣',
            Suit::Spades   => 'Spades ♠',
        };
    }

    // Enums can have constants
    const DEFAULT = self::Hearts;
}

// Enums can be used in match expressions
$suit = Suit::Hearts;
$label = match($suit) {
    Suit::Hearts, Suit::Diamonds => 'Red',
    Suit::Clubs, Suit::Spades    => 'Black',
};

// Enums cannot be instantiated with new
// Enums cannot have regular (non-constant) properties
// Enums can use traits
```

> **Key difference:** PHP 8.1 enums are first-class types with methods, interface implementation, and backed values. Go has no native enum — it uses `iota` constants, which are just typed integers with no enforced exhaustiveness or associated methods.

> **Sources:**
> - **Go:** [Go Spec — Iota](https://go.dev/ref/spec#Iota) · [Go by Example — Constants](https://gobyexample.com/constants)
> - **PHP:** [PHP Manual — Enumerations](https://www.php.net/manual/en/language.enumerations.php) · [PHP 8.1 — Enums RFC](https://wiki.php.net/rfc/enumerations)

---

## Variable

**Go:**
```go
// Typed constant
const MaxSize int = 1024
const Pi = 3.14 // untyped, type inferred at use site

// Grouped constants
const (
    A = "foo"
    B = 42
)

// Variable declaration
var name string
name = "Alice"

// Short declaration (inside function only)
age := 30

// Multiple assignment
x, y := 1, 2
x, y = y, x // swap

// Blank identifier (discard)
_, err := os.Open("file.txt")
```

**PHP:**
```php
<?php
// Constants (global scope or class scope)
define('MAX_SIZE', 1024);        // runtime constant
const PI = 3.14;                 // compile-time constant (at file/namespace/class level)

// Variables — always start with $, no declaration needed
$name = 'Alice';
$age = 30;

// Multiple assignment
[$x, $y] = [1, 2];
[$x, $y] = [$y, $x]; // swap

// list() — older style (still valid)
list($a, $b) = [10, 20];

// Variable variables (avoid — hard to analyse statically)
$varName = 'hello';
$$varName = 'world'; // creates $hello = 'world'

// References (like pointers — share the same memory)
$a = 1;
$b = &$a;
$b = 99;
echo $a; // 99

// PHP has no blank identifier — unused variables are silently ignored
// Static variables persist across function calls
function counter(): int {
    static $count = 0;
    return ++$count;
}

// Typed variable declarations via class properties (PHP 7.4+)
class Foo {
    public int $id = 0;
    public string $name = '';
}
```

> **Key difference:** Go variables have block scope and unused variables cause compile errors. PHP variables are function-scoped (except inside closures, which need explicit `use`), never cause errors when unused, and can be created dynamically.

> **Sources:**
> - **Go:** [A Tour of Go — Variables](https://go.dev/tour/basics/8) · [Go Spec — Variable declarations](https://go.dev/ref/spec#Variable_declarations)
> - **PHP:** [PHP Manual — Variables](https://www.php.net/manual/en/language.variables.php) · [PHP Manual — Constants](https://www.php.net/manual/en/language.constants.php)

---

## Function / Method

**Go:**
```go
// Basic function
func greet(name string) string {
    return "Hello, " + name
}

// Multiple return values
func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("division by zero")
    }
    return a / b, nil
}

// Named return values + bare return
func minMax(a, b int) (min, max int) {
    if a < b {
        return a, b
    }
    return b, a
}

// Variadic
func sum(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}
sum(1, 2, 3)
nums := []int{1, 2, 3}
sum(nums...) // spread slice

// Method (receiver syntax)
type Rect struct{ Width, Height float64 }

func (r Rect) Area() float64       { return r.Width * r.Height }
func (r *Rect) Scale(factor float64) { r.Width *= factor; r.Height *= factor }
```

**PHP:**
```php
<?php
// Basic function
function greet(string $name): string {
    return "Hello, $name";
}

// Multiple return values — use array or value object
function divide(float $a, float $b): array {
    if ($b == 0) return [null, new \DivisionByZeroError()];
    return [$a / $b, null];
}
[$result, $err] = divide(10, 2);

// Named (via array destructuring or DTO)
function minMax(int $a, int $b): array {
    return $a < $b ? ['min' => $a, 'max' => $b] : ['min' => $b, 'max' => $a];
}
['min' => $min, 'max' => $max] = minMax(3, 7);

// Default parameters
function createUser(string $name, int $role = 1, bool $active = true): void { ... }

// Variadic
function sum(int ...$nums): int {
    return array_sum($nums);
}
sum(1, 2, 3);
$nums = [1, 2, 3];
sum(...$nums); // spread array

// Nullable parameter
function find(?int $id): ?User { ... }

// Union parameter type
function process(int|string $value): string { ... }

// Class method
class Rect {
    public function __construct(
        public float $width,
        public float $height,
    ) {}

    public function area(): float {
        return $this->width * $this->height;
    }

    public function scale(float $factor): void {
        $this->width  *= $factor;
        $this->height *= $factor;
    }

    // Static method
    public static function unit(): static {
        return new static(1.0, 1.0);
    }
}

// Returning $this for chaining
class Builder {
    private array $parts = [];

    public function add(string $part): static {
        $this->parts[] = $part;
        return $this;
    }

    public function build(): string {
        return implode(', ', $this->parts);
    }
}

$result = (new Builder())->add('a')->add('b')->build(); // "a, b"
```

> **Key difference:** Go has no classes — methods are attached to types via receiver syntax. PHP methods live inside classes with explicit `$this`. Go supports multiple return values natively; PHP returns arrays or objects. Go has no method chaining pattern built-in; PHP commonly uses it via `return $this`.

> **Sources:**
> - **Go:** [A Tour of Go — Functions](https://go.dev/tour/basics/4) · [Effective Go — Functions](https://go.dev/doc/effective_go#functions)
> - **PHP:** [PHP Manual — Functions](https://www.php.net/manual/en/language.functions.php) · [PHP Manual — Class methods](https://www.php.net/manual/en/language.oop5.basic.php)

---

## If Statement

Both languages use `if/else if/else`. PHP requires the condition in parentheses; Go does not.

**Go:**
```go
if x > 0 {
    fmt.Println("positive")
} else if x == 0 {
    fmt.Println("zero")
} else {
    fmt.Println("negative")
}

// Short init statement — variable scoped to the if block
if err := doSomething(); err != nil {
    log.Fatal(err)
}

// With assignment and check
if v, ok := m["key"]; ok {
    fmt.Println(v)
}
```

**PHP:**
```php
<?php
if ($x > 0) {
    echo "positive\n";
} elseif ($x === 0) {  // note: elseif (one word) or else if (two words) both work
    echo "zero\n";
} else {
    echo "negative\n";
}

// Ternary
$label = $x > 0 ? "positive" : "non-positive";

// Null coalescing (returns left side if not null, else right)
$name = $user->name ?? 'Guest';

// Null coalescing assignment (PHP 7.4+)
$data['key'] ??= 'default';

// Nullsafe operator (PHP 8.0) — short-circuits if null
$city = $user?->getAddress()?->getCity(); // null if any step is null

// No init-statement — PHP if cannot declare a variable in the condition
// Use assignment expression (rare, avoid):
if ($val = computeVal()) {  // truthy check, not recommended
    use($val);
}
```

> **Key difference:** Go's `if` allows an init statement (`if x := f(); x != nil`), scoping the variable to the block. PHP requires parentheses around the condition and has no init statement, but adds `??` (null coalesce) and `?->` (nullsafe) operators.

> **Sources:**
> - **Go:** [A Tour of Go — If](https://go.dev/tour/flowcontrol/5) · [Go Spec — If statements](https://go.dev/ref/spec#If_statements)
> - **PHP:** [PHP Manual — if](https://www.php.net/manual/en/control-structures.if.php) · [PHP Manual — Null coalescing](https://www.php.net/manual/en/migration70.new-features.php)

---

## Loops

**Go** — `for` is the only loop keyword:
```go
// C-style
for i := 0; i < 10; i++ {
    fmt.Println(i)
}

// While-style
for n < 1000 {
    n *= 2
}

// Infinite
for {
    break
}

// Range over slice (index, value)
for i, v := range []int{1, 2, 3} {
    fmt.Println(i, v)
}

// Range over map
for k, v := range m {
    fmt.Println(k, v)
}

// Range over string (yields rune, byte-index)
for i, r := range "héllo" {
    fmt.Printf("%d: %c\n", i, r)
}

// Range over channel
for msg := range ch {
    fmt.Println(msg)
}

// Discard index or value
for _, v := range s {}
for i := range s    {}
```

**PHP** — `for`, `while`, `do...while`, `foreach`:
```php
<?php
// C-style for
for ($i = 0; $i < 10; $i++) {
    echo $i . "\n";
}

// while
$n = 1;
while ($n < 1000) {
    $n *= 2;
}

// do...while (executes at least once)
do {
    $input = readline();
} while ($input !== 'quit');

// foreach over array
foreach ([1, 2, 3] as $v) {
    echo $v . "\n";
}

// foreach with key
foreach (['a' => 1, 'b' => 2] as $k => $v) {
    echo "$k: $v\n";
}

// foreach by reference — modifies original
foreach ($items as &$item) {
    $item *= 2;
}
unset($item); // IMPORTANT: unset reference after loop to avoid bugs

// foreach over object implementing Iterator
foreach ($collection as $item) { ... }

// break / continue with levels
foreach ($matrix as $row) {
    foreach ($row as $cell) {
        if ($cell === 0) continue 2; // continue the outer loop
        if ($cell < 0)  break 2;    // break both loops
    }
}
```

> **Key difference:** Go has one loop construct (`for`) that covers all cases. PHP has four (`for`, `while`, `do...while`, `foreach`). Go ranges are type-safe and work on arrays, slices, maps, strings, and channels. PHP `foreach` works on arrays and `Traversable` objects.

> **Sources:**
> - **Go:** [A Tour of Go — For](https://go.dev/tour/flowcontrol/1) · [Effective Go — For](https://go.dev/doc/effective_go#for)
> - **PHP:** [PHP Manual — for](https://www.php.net/manual/en/control-structures.for.php) · [PHP Manual — foreach](https://www.php.net/manual/en/control-structures.foreach.php)

---

## Switch & Match

**Go:**
```go
// Expression switch (no implicit fall-through)
switch os {
case "darwin":
    fmt.Println("macOS")
case "linux":
    fmt.Println("Linux")
default:
    fmt.Println("Other")
}

// Multiple values per case
switch c {
case 'a', 'e', 'i', 'o', 'u':
    fmt.Println("vowel")
}

// No-tag switch (acts like if-else chain)
switch {
case x < 0:
    fmt.Println("negative")
case x == 0:
    fmt.Println("zero")
default:
    fmt.Println("positive")
}

// Explicit fall-through
switch i {
case 0:
    foo()
    fallthrough
case 1:
    bar()
}

// Type switch
switch v := x.(type) {
case int:
    fmt.Println("int:", v)
case string:
    fmt.Println("string:", v)
}
```

**PHP — switch (old) and match (PHP 8.0+):**
```php
<?php
// switch — loose comparison (==), implicit fall-through
switch ($os) {
    case 'darwin':
        echo "macOS\n";
        break; // break is required to prevent fall-through
    case 'linux':
        echo "Linux\n";
        break;
    default:
        echo "Other\n";
}

// Fall-through in switch (omit break)
switch ($grade) {
    case 'A':
    case 'B':
        echo "Pass\n";
        break;
    case 'F':
        echo "Fail\n";
        break;
}

// match — strict comparison (===), no fall-through, exhaustive (throws UnhandledMatchError)
$label = match($os) {
    'darwin'           => 'macOS',
    'linux', 'ubuntu'  => 'Linux',  // multiple arms
    'win32', 'windows' => 'Windows',
    default            => 'Unknown',
};

// match with no subject (like if-else chain)
$result = match(true) {
    $x < 0  => 'negative',
    $x === 0 => 'zero',
    default  => 'positive',
};

// match is an expression — can be used anywhere
echo match($status) {
    Status::Active   => 'Active',
    Status::Inactive => 'Inactive',
};
```

| | Go `switch` | PHP `switch` | PHP `match` |
|---|---|---|---|
| Fall-through | No (explicit `fallthrough`) | Yes (need `break`) | No |
| Comparison | `==` | `==` (loose) | `===` (strict) |
| Expression | No | No | Yes |
| Exhaustive | No | No | Yes (throws if unhandled) |
| Multiple values per arm | Yes (`,`) | Yes (separate `case`) | Yes (`,`) |

> **Key difference:** PHP's `match` (8.0+) is Go-like in behavior — strict comparison, no fall-through, returns a value. PHP's `switch` is legacy and uses loose equality. Go's `switch` is not an expression.

> **Sources:**
> - **Go:** [A Tour of Go — Switch](https://go.dev/tour/flowcontrol/9) · [Go Spec — Switch statements](https://go.dev/ref/spec#Switch_statements)
> - **PHP:** [PHP Manual — switch](https://www.php.net/manual/en/control-structures.switch.php) · [PHP Manual — match](https://www.php.net/manual/en/control-structures.match.php)

---

## Class & Struct

**Go** — structs with methods, no classes:
```go
type Animal struct {
    Name string
    Age  int
}

// Constructor (by convention)
func NewAnimal(name string, age int) Animal {
    return Animal{Name: name, Age: age}
}

// Value receiver — does not mutate
func (a Animal) Speak() string {
    return a.Name + " speaks"
}

// Pointer receiver — mutates
func (a *Animal) Birthday() {
    a.Age++
}

// Embedding (composition, not inheritance)
type Dog struct {
    Animal          // embedded — fields and methods promoted
    Breed string
}

d := Dog{Animal: Animal{"Rex", 3}, Breed: "Labrador"}
fmt.Println(d.Speak()) // promoted method
fmt.Println(d.Name)    // promoted field
```

**PHP** — full OOP with classes, inheritance, abstract classes:
```php
<?php
class Animal {
    // Class constant
    public const KINGDOM = 'Animalia';

    public function __construct(
        protected string $name,
        protected int $age,
    ) {}

    public function speak(): string {
        return "$this->name speaks";
    }

    public function birthday(): void {
        $this->age++;
    }

    // Magic method: called when casting to string
    public function __toString(): string {
        return "{$this->name} (age {$this->age})";
    }

    // Clone hook
    public function __clone() {
        // deep copy logic here if needed
    }
}

// Inheritance
class Dog extends Animal {
    public function __construct(
        string $name,
        int $age,
        private string $breed,
    ) {
        parent::__construct($name, $age);
    }

    // Override
    public function speak(): string {
        return parent::speak() . ": Woof!";
    }
}

// Abstract class
abstract class Shape {
    abstract public function area(): float;

    // Concrete method available to all subclasses
    public function describe(): string {
        return "This shape has area " . $this->area();
    }
}

class Circle extends Shape {
    public function __construct(private float $radius) {}

    public function area(): float {
        return M_PI * $this->radius ** 2;
    }
}

// Final class / method
final class Singleton {
    private static ?self $instance = null;

    private function __construct() {}

    public static function getInstance(): static {
        return static::$instance ??= new static();
    }
}

// Anonymous class
$obj = new class('temp') extends Animal {
    public function speak(): string { return "anonymous speaks"; }
};
```

> **Key difference:** Go has no inheritance — composition via embedding. PHP has full class-based OOP: single inheritance, abstract classes, final classes, magic methods. Go "constructors" are just regular functions by convention; PHP uses `__construct`.

> **Sources:**
> - **Go:** [A Tour of Go — Structs](https://go.dev/tour/moretypes/2) · [Effective Go — Embedding](https://go.dev/doc/effective_go#embedding)
> - **PHP:** [PHP Manual — Classes and Objects](https://www.php.net/manual/en/language.oop5.php) · [PHP Manual — Abstract Classes](https://www.php.net/manual/en/language.oop5.abstract.php)

---

## Interface

**Go** — structural (implicit) typing: a type satisfies an interface if it has the required methods:
```go
type Stringer interface {
    String() string
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

// Interface composition
type ReadWriter interface {
    io.Reader
    io.Writer
}

// A type satisfies Stringer without any declaration
type Point struct{ X, Y int }

func (p Point) String() string {
    return fmt.Sprintf("(%d, %d)", p.X, p.Y)
}

// Works because Point has String() method
var s Stringer = Point{1, 2}
fmt.Println(s.String())

// Empty interface accepts any value
func printAny(v any) {
    fmt.Println(v)
}
```

**PHP** — nominal (explicit) typing: classes must declare `implements`:
```php
<?php
interface Stringable {
    public function __toString(): string;
}

interface Writable {
    public function write(string $data): int;
}

// Interface extending multiple interfaces
interface ReadWritable extends Readable, Writable {}

// Must explicitly declare implements
class Point implements Stringable {
    public function __construct(
        private int $x,
        private int $y,
    ) {}

    public function __toString(): string {
        return "({$this->x}, {$this->y})";
    }
}

$p = new Point(1, 2);
echo $p; // (1, 2)

// Type check
var_dump($p instanceof Stringable); // bool(true)

// Interface with constants
interface Colors {
    const RED = 'red';
    const BLUE = 'blue';
}

// A class can implement multiple interfaces
class MultiImpl implements InterfaceA, InterfaceB, InterfaceC { ... }
```

> **Key difference:** Go uses structural (duck) typing — no `implements` declaration needed. PHP uses nominal typing — `implements` is mandatory. A PHP class accidentally satisfying an interface does NOT automatically implement it. This means Go interfaces enable retroactive polymorphism; PHP does not.

> **Sources:**
> - **Go:** [Effective Go — Interfaces](https://go.dev/doc/effective_go#interfaces) · [Go Spec — Interface types](https://go.dev/ref/spec#Interface_types)
> - **PHP:** [PHP Manual — Interfaces](https://www.php.net/manual/en/language.oop5.interfaces.php)

---

## Traits (PHP only)

PHP traits are a code-reuse mechanism for single-inheritance languages. Go has no equivalent — use interfaces or embedding instead.

```php
<?php
trait Timestampable {
    private ?\DateTimeImmutable $createdAt = null;
    private ?\DateTimeImmutable $updatedAt = null;

    public function setCreatedAt(\DateTimeImmutable $dt): void {
        $this->createdAt = $dt;
    }

    public function touch(): void {
        $this->updatedAt = new \DateTimeImmutable();
    }

    public function getCreatedAt(): ?\DateTimeImmutable {
        return $this->createdAt;
    }
}

trait SoftDeletable {
    private bool $deleted = false;

    public function softDelete(): void { $this->deleted = true; }
    public function isDeleted(): bool  { return $this->deleted; }
}

// Use multiple traits in a class
class Post {
    use Timestampable, SoftDeletable;

    public function __construct(public string $title) {}
}

$post = new Post('Hello World');
$post->touch();
$post->softDelete();
var_dump($post->isDeleted()); // true

// Conflict resolution when two traits define the same method
trait A { public function hello(): string { return 'A'; } }
trait B { public function hello(): string { return 'B'; } }

class C {
    use A, B {
        A::hello insteadof B; // prefer A's hello
        B::hello as helloFromB; // keep B's hello under a different name
    }
}

$c = new C();
echo $c->hello();      // 'A'
echo $c->helloFromB(); // 'B'

// Abstract method in trait — enforces implementation by using class
trait Validatable {
    abstract protected function rules(): array;

    public function validate(array $data): bool {
        foreach ($this->rules() as $rule) {
            if (!$rule->check($data)) return false;
        }
        return true;
    }
}
```

> **Go equivalent:** Go's embedding achieves similar method promotion but is a first-class language feature tied to struct composition, not a separate code-reuse mechanism. Multiple embedding is possible but method conflicts are compile errors.

> **Sources:**
> - **PHP:** [PHP Manual — Traits](https://www.php.net/manual/en/language.oop5.traits.php)
> - **Go:** [Effective Go — Embedding](https://go.dev/doc/effective_go#embedding) *(for comparison)*

---

## Pointer & Reference

**Go** — explicit pointers with `*` (type) and `&` (address-of) and `*` (dereference):
```go
// Pointer declaration
var p *int

i := 42
p = &i          // address-of
fmt.Println(*p) // dereference: 42
*p = 100        // mutate through pointer
fmt.Println(i)  // 100

// new() allocates zeroed memory, returns pointer
p2 := new(int)
*p2 = 7

// Structs: field access auto-dereferences
type Point struct{ X, Y int }
pt := &Point{1, 2}
pt.X = 99 // equivalent to (*pt).X = 99

// nil pointer
var np *int
fmt.Println(np == nil) // true
// *np = 1             // panic: nil pointer dereference
```

**PHP** — no pointers; objects are always passed by reference handle; scalars copied by value; explicit `&` for reference:
```php
<?php
// Objects: always passed by "handle" (not full copy)
// Two variables can reference the same object
$a = new stdClass();
$a->x = 1;

$b = $a;       // $b holds the same object handle
$b->x = 99;
echo $a->x;    // 99 — same object!

$c = clone $a; // deep copy
$c->x = 0;
echo $a->x;    // 99 — unaffected

// Scalar references with &
$x = 10;
$y = &$x;  // $y is a reference to $x
$y = 99;
echo $x;   // 99

// Reference parameter
function increment(int &$val): void {
    $val++;
}
$n = 5;
increment($n);
echo $n; // 6

// Reference return
function &getRef(array &$arr, int $i): mixed {
    return $arr[$i];
}
$arr = [1, 2, 3];
$ref = &getRef($arr, 1);
$ref = 99;
echo $arr[1]; // 99

// PHP has no pointer arithmetic or raw memory access
// null (not nil) is the absence of a value
$p = null;
var_dump($p === null); // true
```

| | Go | PHP |
|---|---|---|
| Pointer type | `*T` | no explicit pointer type |
| Address of | `&x` | `&$x` (reference, not pointer) |
| Dereference | `*p` | automatic for objects |
| Null/nil | `nil` | `null` |
| Object passing | by value (pointer-to-struct common) | by object handle (reference-like) |
| Pointer arithmetic | No | No |

> **Key difference:** Go has true pointers — you control whether something is passed by value or by pointer. PHP has no pointers — scalars are copied by value, objects are passed by handle (effectively a reference), and you can explicitly create reference aliases with `&`.

> **Sources:**
> - **Go:** [A Tour of Go — Pointers](https://go.dev/tour/moretypes/1) · [Go Spec — Pointer types](https://go.dev/ref/spec#Pointer_types)
> - **PHP:** [PHP Manual — References](https://www.php.net/manual/en/language.references.php) · [PHP Manual — Objects and references](https://www.php.net/manual/en/language.oop5.references.php)

---

## Array & Slice

**Go:**
```go
// Fixed array (length is part of the type — rarely used directly)
var a [5]int
a := [3]int{1, 2, 3}
a := [...]int{1, 2, 3} // length inferred

// Slice (dynamic window over an array — the idiomatic type)
s := []int{1, 2, 3}
s := make([]int, 5)        // len=5, cap=5
s := make([]int, 0, 10)    // len=0, cap=10

// Append
s = append(s, 4, 5)
other := []int{6, 7}
s = append(s, other...) // spread

// Slicing (shares underlying array)
s2 := s[1:3]  // elements 1 and 2
s2 := s[:3]   // first 3
s2 := s[2:]   // from index 2

len(s)
cap(s)

// 2D slice
matrix := [][]int{
    {1, 2, 3},
    {4, 5, 6},
}

// Copy (does not share memory)
dst := make([]int, len(src))
copy(dst, src)
```

**PHP:**
```php
<?php
// PHP arrays are ordered maps — they serve as both arrays and dictionaries
// Integer-indexed array
$a = [1, 2, 3];
$a = array(1, 2, 3); // older syntax

// Append
$a[] = 4;
array_push($a, 5, 6);

// Access and mutation
echo $a[0]; // 1
$a[0] = 99;

// Slicing
$slice = array_slice($a, 1, 3);          // offset=1, length=3
$slice = array_slice($a, 1, 3, true);    // preserve_keys=true

// Spread
$merged = [...$a, ...$b];                // PHP 7.4+
$merged = array_merge($a, $b);           // older

// Length
count($a);

// 2D array
$matrix = [
    [1, 2, 3],
    [4, 5, 6],
];

// Useful array functions
sort($a);                           // sort in-place (reindexes)
usort($a, fn($x, $y) => $x - $y);  // custom sort
array_map(fn($x) => $x * 2, $a);   // map (returns new array)
array_filter($a, fn($x) => $x > 2);// filter
array_reduce($a, fn($carry, $x) => $carry + $x, 0); // reduce
in_array(3, $a);                    // membership test (loose by default)
in_array(3, $a, strict: true);      // strict type check
array_search(3, $a);                // key of first match or false

// Unpacking
[$first, $second, ...$rest] = $a;   // PHP 7.4+
[1 => $second] = $a;                // by key

// Copy (arrays are value types in PHP — assignment copies!)
$copy = $a;    // modifying $copy does NOT affect $a
$ref  = &$a;   // this shares the same array
```

> **Key difference:** Go separates fixed arrays (rarely used) from slices (the idiomatic type). PHP has a single `array` type that is dynamically sized, ordered, and hash-map backed. PHP arrays are **value types** — assignment copies; Go slices share underlying memory. PHP has rich built-in `array_*` functions; Go relies on the `slices` standard library package (1.21+) or loops.

> **Sources:**
> - **Go:** [A Tour of Go — Slices](https://go.dev/tour/moretypes/7) · [Effective Go — Arrays & Slices](https://go.dev/doc/effective_go#arrays)
> - **PHP:** [PHP Manual — Arrays](https://www.php.net/manual/en/language.types.array.php) · [PHP Manual — Array functions](https://www.php.net/manual/en/ref.array.php)

---

## Map / Associative Array

**Go:**
```go
// Create
m := make(map[string]int)
m := map[string]int{"a": 1, "b": 2} // literal

// Operations
m["key"] = 42
value := m["key"]       // returns zero value if absent
delete(m, "key")
clear(m)                // Go 1.21+

// Check existence
value, ok := m["key"]  // ok=false if absent

len(m)

// Iterate
for k, v := range m {
    fmt.Println(k, v)
}
```

**PHP:**
```php
<?php
// PHP's array IS an associative map (ordered)
$m = [];
$m = ['a' => 1, 'b' => 2];

// Operations
$m['key'] = 42;
$value = $m['key'];            // Notice if absent
$value = $m['key'] ?? null;    // safe access with null coalesce
unset($m['key']);

// Check existence
isset($m['key']);               // false if absent or null
array_key_exists('key', $m);   // false only if absent (true even if value is null)

count($m);

// Iterate
foreach ($m as $k => $v) {
    echo "$k: $v\n";
}

// Keys and values
$keys   = array_keys($m);
$values = array_values($m);

// Merge
$merged = array_merge($m1, $m2);      // later keys win
$merged = array_replace($m1, $m2);    // same but recursive variant available

// Flip keys/values
$flipped = array_flip($m);

// Sort by value (preserves keys)
asort($m);
arsort($m);        // descending

// Sort by key
ksort($m);
krsort($m);

// Filter by key/value
$filtered = array_filter($m, fn($v) => $v > 1);
$filtered = array_filter($m, fn($k) => str_starts_with($k, 'a'), ARRAY_FILTER_USE_KEY);

// Map over values
$doubled = array_map(fn($v) => $v * 2, $m);
```

> **Key difference:** Go maps are unordered hash tables — iteration order is deliberately randomized each run. PHP arrays maintain insertion order and can be sorted. PHP uses arrays for both list and map semantics — there is no separate map type. Go requires `map[K]V` with comparable keys; PHP array keys must be strings or integers.

> **Sources:**
> - **Go:** [A Tour of Go — Maps](https://go.dev/tour/moretypes/19) · [Effective Go — Maps](https://go.dev/doc/effective_go#maps)
> - **PHP:** [PHP Manual — Arrays](https://www.php.net/manual/en/language.types.array.php)

---

## Null Handling

**Go:**
```go
// nil is the zero value for pointers, interfaces, maps, slices, channels, funcs
var p *int      // nil
var s []int     // nil (also len=0)
var m map[string]int // nil (reads return zero value, writes panic)
var i interface{}    // nil

// Guard before use
if p != nil {
    fmt.Println(*p)
}

// Nil interface vs nil pointer — subtle distinction
var err error       // nil interface
var myErr *MyError  // typed nil pointer
err = myErr         // err != nil even though myErr is nil!

// Optional pattern using pointer
type Config struct {
    Timeout *time.Duration // nil means "use default"
}
```

**PHP:**
```php
<?php
// null is a first-class type value
$x = null;
var_dump($x === null);   // true
var_dump(isset($x));     // false
var_dump(is_null($x));   // true

// Nullable types — prefix with ?
function find(?int $id): ?User { ... }

// Null coalescing — returns right side if left is null or undefined
$name = $user['name'] ?? 'Guest';
$name = $user?->getName() ?? 'Guest'; // nullsafe + coalesce

// Null coalescing assignment
$config['timeout'] ??= 30;

// Nullsafe operator chain (PHP 8.0+)
$country = $user?->getAddress()?->getCountry()?->getName();
// Returns null at the first null instead of throwing

// isset vs null checks
$a = null;
isset($a);          // false — null counts as "not set"
array_key_exists('key', $arr) && $arr['key'] !== null; // precise null check

// Loose null comparison (avoid in PHP 8)
0 == null;   // true (loose)
0 === null;  // false (strict — use this)

// Empty check
empty($x); // true for null, 0, '', '0', [], false
```

> **Key difference:** PHP's `null` is a value any variable can hold (with nullable types `?T`). Go's `nil` is only valid for certain reference types (pointers, slices, maps, channels, funcs, interfaces) and is not a separate type. PHP adds `??` and `?->` operators to safely traverse nullable chains; Go requires explicit `!= nil` guards.

> **Sources:**
> - **Go:** [Go Spec — The zero value](https://go.dev/ref/spec#The_zero_value) · [Go FAQ — nil](https://go.dev/doc/faq#nil_error)
> - **PHP:** [PHP Manual — null](https://www.php.net/manual/en/language.types.null.php) · [PHP Manual — Nullsafe operator](https://www.php.net/manual/en/language.oop5.basic.php#language.oop5.basic.nullsafe)

---

## Error Handling

**Go** — errors are values implementing the `error` interface; multiple return values:
```go
// The error interface
type error interface {
    Error() string
}

// Functions return error as the last value
f, err := os.Open("file.txt")
if err != nil {
    log.Fatal(err)
}

// Custom error type
type NotFoundError struct {
    ID int
}
func (e *NotFoundError) Error() string {
    return fmt.Sprintf("item %d not found", e.ID)
}

// Wrapping errors (Go 1.13+)
if err != nil {
    return fmt.Errorf("reading config: %w", err)
}

// Unwrapping
var nfe *NotFoundError
if errors.As(err, &nfe) {
    fmt.Println("not found:", nfe.ID)
}
if errors.Is(err, os.ErrNotExist) {
    fmt.Println("file does not exist")
}

// Panic (unrecoverable error — avoid for normal control flow)
func mustPositive(n int) int {
    if n <= 0 {
        panic(fmt.Sprintf("expected positive, got %d", n))
    }
    return n
}

// Recover from panic
func safe() (err error) {
    defer func() {
        if r := recover(); r != nil {
            err = fmt.Errorf("recovered: %v", r)
        }
    }()
    // code that might panic
    return nil
}
```

**PHP** — exceptions with `try/catch/finally`:
```php
<?php
// Throw and catch exceptions
function openFile(string $path): resource {
    $f = @fopen($path, 'r');
    if ($f === false) {
        throw new \RuntimeException("Cannot open: $path");
    }
    return $f;
}

try {
    $f = openFile('data.txt');
    // ... use $f
} catch (\RuntimeException $e) {
    echo "Error: " . $e->getMessage() . "\n";
    echo $e->getTraceAsString();
} catch (\InvalidArgumentException|\TypeError $e) {
    // PHP 8.0+ multi-catch with |
    echo "Bad argument: " . $e->getMessage() . "\n";
} finally {
    // Always runs — cleanup here
    echo "Done\n";
}

// Custom exception hierarchy
class DomainException extends \RuntimeException {}
class NotFoundException extends DomainException {
    public function __construct(
        string $resource,
        int $id,
        \Throwable $previous = null
    ) {
        parent::__construct("$resource #$id not found", 404, $previous);
    }
}

// Re-throw with context (wrapping)
try {
    $user = $repo->find($id);
} catch (\PDOException $e) {
    throw new \RuntimeException("DB error fetching user $id", 0, $e);
}

// Access the chain
$e->getPrevious(); // the wrapped exception

// Error vs Exception hierarchy
// \Throwable (interface)
//   ├── \Error      — fatal engine errors (TypeError, ParseError, etc.)
//   └── \Exception  — application-level exceptions
//         └── \RuntimeException, \LogicException, etc.

try {
    $result = 1 / 0; // throws \DivisionByZeroError (an Error, not Exception)
} catch (\DivisionByZeroError $e) {
    echo "Division by zero\n";
} catch (\Throwable $e) {
    echo "Anything: " . $e->getMessage();
}

// Result pattern (popular with libraries like neverthrow)
// Not built-in, but achievable:
class Result {
    private function __construct(
        private readonly mixed $value,
        private readonly ?\Throwable $error,
    ) {}

    public static function ok(mixed $v): self    { return new self($v, null); }
    public static function err(\Throwable $e): self { return new self(null, $e); }
    public function isOk(): bool { return $this->error === null; }
    public function unwrap(): mixed {
        if ($this->error) throw $this->error;
        return $this->value;
    }
}
```

> **Key difference:** Go treats errors as ordinary return values — no exceptions, no stack unwinding. PHP uses exception-based error handling with `try/catch/finally`. Go's approach makes error paths explicit at every call site; PHP's exceptions propagate up the call stack automatically until caught. Go has `panic/recover` for truly exceptional situations.

> **Sources:**
> - **Go:** [Effective Go — Errors](https://go.dev/doc/effective_go#errors) · [Go Blog — Error handling and Go](https://go.dev/blog/error-handling-and-go) · [Go Blog — Working with Errors](https://go.dev/blog/go1.13-errors)
> - **PHP:** [PHP Manual — Exceptions](https://www.php.net/manual/en/language.exceptions.php) · [PHP Manual — Error handling](https://www.php.net/manual/en/book.errorfunc.php)

---

## Closures & Anonymous Functions

**Go** — closures automatically capture variables from enclosing scope by reference:
```go
// Anonymous function assigned to variable
double := func(x int) int { return x * 2 }
fmt.Println(double(5)) // 10

// Closure captures outer variable
func makeCounter() func() int {
    n := 0
    return func() int {
        n++
        return n
    }
}
counter := makeCounter()
fmt.Println(counter()) // 1
fmt.Println(counter()) // 2

// Immediately invoked
result := func(a, b int) int { return a + b }(3, 4) // 7

// Passing functions as values
func apply(nums []int, f func(int) int) []int {
    out := make([]int, len(nums))
    for i, n := range nums {
        out[i] = f(n)
    }
    return out
}
apply([]int{1, 2, 3}, func(n int) int { return n * 2 }) // [2 4 6]
```

**PHP** — `function` closures need explicit `use`, arrow functions capture automatically:
```php
<?php
// Anonymous function (must explicitly capture outer variables with use)
$multiplier = 3;
$triple = function(int $x) use ($multiplier): int {
    return $x * $multiplier;
};
echo $triple(5); // 15

// Capture by reference
$count = 0;
$increment = function() use (&$count): void {
    $count++;
};
$increment();
$increment();
echo $count; // 2

// Arrow function (PHP 7.4+) — captures enclosing scope automatically, by value
$multiplier = 4;
$quadruple = fn(int $x): int => $x * $multiplier; // no use() needed
echo $quadruple(5); // 20

// Arrow functions are single-expression only — no { } block
// Arrow functions capture by value — cannot mutate outer variables

// Higher-order functions
$nums = [1, 2, 3, 4, 5];
$doubled  = array_map(fn($x) => $x * 2, $nums);
$evens    = array_filter($nums, fn($x) => $x % 2 === 0);
$sum      = array_reduce($nums, fn($carry, $x) => $carry + $x, 0);

// Closure binding — attach a closure to an object
class Foo {
    private int $x = 10;
}

$getClosure = Closure::bind(
    fn() => $this->x,
    new Foo(),
    Foo::class
);
echo $getClosure(); // 10

// Closure::fromCallable — wrap any callable as a Closure
$closure = Closure::fromCallable('strlen');
echo $closure('hello'); // 5

// Immediately invoked
$result = (fn($a, $b) => $a + $b)(3, 4); // 7
```

| | Go | PHP `function` | PHP arrow `fn` |
|---|---|---|---|
| Syntax | `func(params) return { body }` | `function(params) use(...) { body }` | `fn(params) => expr` |
| Capture outer vars | Automatic (by ref) | Explicit `use($var)` | Automatic (by value) |
| Mutate outer vars | Yes (automatic) | Yes (with `use (&$var)`) | No |
| Multi-statement body | Yes | Yes | No (single expr only) |

> **Sources:**
> - **Go:** [A Tour of Go — Function closures](https://go.dev/tour/moretypes/25) · [Effective Go — Functions](https://go.dev/doc/effective_go#functions)
> - **PHP:** [PHP Manual — Anonymous functions](https://www.php.net/manual/en/functions.anonymous.php) · [PHP Manual — Arrow functions](https://www.php.net/manual/en/functions.arrow.php)

---

## Generics

**Go 1.18+** — type parameters with constraints:
```go
// Generic function
func Map[T, U any](s []T, f func(T) U) []U {
    result := make([]U, len(s))
    for i, v := range s {
        result[i] = f(v)
    }
    return result
}

// Constraint via interface
type Number interface {
    ~int | ~int8 | ~int16 | ~int32 | ~int64 |
    ~float32 | ~float64
}

func Sum[T Number](s []T) T {
    var total T
    for _, v := range s {
        total += v
    }
    return total
}

// Generic struct
type Stack[T any] struct {
    items []T
}

func (s *Stack[T]) Push(item T) { s.items = append(s.items, item) }
func (s *Stack[T]) Pop() (T, bool) {
    if len(s.items) == 0 {
        var zero T
        return zero, false
    }
    n := len(s.items) - 1
    item := s.items[n]
    s.items = s.items[:n]
    return item, true
}

s := Stack[int]{}
s.Push(1)
s.Push(2)
v, ok := s.Pop() // v=2, ok=true
```

**PHP** — no runtime generics; generics are doc-comment conventions enforced by static analysis (PHPStan/Psalm):
```php
<?php
/**
 * @template T
 */
class Collection {
    /** @var list<T> */
    private array $items = [];

    /**
     * @param T $item
     */
    public function add(mixed $item): void {
        $this->items[] = $item;
    }

    /**
     * @return T|null
     */
    public function first(): mixed {
        return $this->items[0] ?? null;
    }

    /**
     * @param callable(T): bool $predicate
     * @return static<T>
     */
    public function filter(callable $predicate): static {
        $new = clone $this;
        $new->items = array_values(array_filter($this->items, $predicate));
        return $new;
    }
}

/**
 * @template TKey of array-key
 * @template TValue
 * @param array<TKey, TValue> $arr
 * @param callable(TValue): bool $fn
 * @return array<TKey, TValue>
 */
function myFilter(array $arr, callable $fn): array {
    return array_filter($arr, $fn);
}

// PHPStan/Psalm will infer types statically:
/** @var Collection<User> */
$users = new Collection();
$users->add(new User());
$first = $users->first(); // inferred as User|null by static analyser

// No runtime enforcement — PHP cannot check generic type parameters at runtime
```

> **Key difference:** Go generics are a first-class language feature enforced at compile time. PHP generics exist only as doc-comment annotations processed by tools like PHPStan or Psalm — they have no runtime effect. PHP 8.x has no plans for native runtime generics.

> **Sources:**
> - **Go:** [Go Blog — An Introduction to Generics](https://go.dev/blog/intro-generics) · [Go Spec — Type parameters](https://go.dev/ref/spec#Type_parameter_declarations)
> - **PHP:** [PHPStan — Generics](https://phpstan.org/blog/generics-in-php-using-phpdocs) · [Psalm — Templated classes](https://psalm.dev/docs/annotating_code/templated_annotations/)

---

## Type Casting & Conversion

**Go** — explicit `T(x)` conversions; type assertions for interfaces:
```go
// Numeric conversion
var i int = 42
f := float64(i)   // int -> float64
u := uint32(f)    // float64 -> uint32 (truncates)

// String conversion
s := string(65)           // rune to string: "A"
s := string([]byte{72, 105}) // []byte to string: "Hi"
b := []byte("Hello")      // string to []byte

// strconv for string<->numeric
n, err := strconv.Atoi("42")        // string -> int
s := strconv.Itoa(42)               // int -> string
f, err := strconv.ParseFloat("3.14", 64)
s := strconv.FormatFloat(3.14, 'f', 2, 64)

// Type assertion on interface
var v any = "hello"
s, ok := v.(string)  // s="hello", ok=true
n, ok := v.(int)     // n=0, ok=false

// Type switch
switch t := v.(type) {
case int:    fmt.Println("int:", t)
case string: fmt.Println("string:", t)
}
```

**PHP:**
```php
<?php
// Explicit cast operators
$i = 42;
$f = (float) $i;     // 42.0
$s = (string) $i;    // "42"
$b = (bool) $i;      // true
$a = (array) $i;     // [42]

// Cast functions (equivalent)
$f = floatval($i);
$s = strval($i);
$i = intval("42px"); // 42 — stops at first non-numeric char
$b = boolval($i);

// settype() — mutates in place
$v = "42";
settype($v, 'integer');
var_dump($v); // int(42)

// Type checking functions
is_int($v);
is_float($v);
is_string($v);
is_bool($v);
is_null($v);
is_array($v);
is_object($v);
is_callable($v);
gettype($v);    // returns string: "integer", "string", etc.

// instanceof for objects
$obj instanceof MyClass;
$obj instanceof MyInterface;

// PHP 8 — get_debug_type() — precise type name
get_debug_type(new \stdClass()); // "stdClass"
get_debug_type(null);            // "null"
get_debug_type(1.5);             // "float"

// String to number (PHP 8 strict)
$n = (int) "42abc"; // 42 with a notice in PHP 8
$n = intval("42abc"); // 42 silently

// json_encode / json_decode
$json = json_encode(['a' => 1, 'b' => 2]);
$data = json_decode($json, associative: true); // array
$obj  = json_decode($json);                    // stdClass
```

> **Key difference:** Go has no implicit conversions — every conversion is explicit and type-safe. PHP has extensive implicit type juggling (coercions happen automatically), though `declare(strict_types=1)` prevents most of them. Go's `unsafe.Pointer` / bit reinterpretation has no PHP equivalent.

> **Sources:**
> - **Go:** [Go Spec — Conversions](https://go.dev/ref/spec#Conversions) · [Go Spec — Type assertions](https://go.dev/ref/spec#Type_assertions)
> - **PHP:** [PHP Manual — Type juggling](https://www.php.net/manual/en/language.types.type-juggling.php) · [PHP Manual — Type casting](https://www.php.net/manual/en/language.types.type-juggling.php#language.types.typecasting)

---

## String Operations

**Go:**
```go
import (
    "fmt"
    "strings"
    "unicode/utf8"
    "strconv"
)

s := "Hello, 世界"

len(s)                              // 13 (bytes, not characters)
utf8.RuneCountInString(s)           // 9 (rune/codepoint count)

// Iterate by rune
for i, r := range s {
    fmt.Printf("index=%d char=%c\n", i, r)
}

strings.Contains(s, "世界")          // true
strings.HasPrefix(s, "Hello")       // true
strings.HasSuffix(s, "界")           // true
strings.Index(s, "世")              // 7

strings.ToUpper(s)
strings.ToLower(s)
strings.TrimSpace("  hi  ")        // "hi"
strings.Trim("--foo--", "-")       // "foo"
strings.Replace(s, "l", "L", 1)    // first occurrence
strings.ReplaceAll(s, "l", "L")    // all occurrences
strings.Split("a,b,c", ",")        // ["a","b","c"]
strings.Join([]string{"a","b"}, "-") // "a-b"
strings.Count(s, "l")              // 3
strings.Repeat("ab", 3)            // "ababab"
strings.Fields("a b  c")           // ["a","b","c"] (any whitespace)
strings.ContainsAny(s, "aeiou")    // true

// Efficient building
var sb strings.Builder
sb.Grow(64)
fmt.Fprintf(&sb, "x=%d ", 10)
sb.WriteString("done")
result := sb.String()

// Formatted string
msg := fmt.Sprintf("x=%d, y=%.2f", 10, 3.14)

// Substring
sub := s[0:5] // "Hello" — shares underlying array
```

**PHP:**
```php
<?php
$s = "Hello, 世界";

strlen($s);                   // 13 (bytes)
mb_strlen($s);                // 9 (multibyte characters — use for Unicode)
mb_strlen($s, 'UTF-8');       // explicit encoding

// Always prefer mb_* functions for Unicode strings
mb_strtoupper($s);
mb_strtolower($s);
mb_substr($s, 0, 5);         // "Hello" — safe for multibyte
mb_strpos($s, '世');         // 7 (byte offset)

// Common string functions
str_contains($s, '世界');     // true (PHP 8.0+)
str_starts_with($s, 'Hello'); // true (PHP 8.0+)
str_ends_with($s, '界');      // true (PHP 8.0+)
strpos($s, 'l');              // 2 — first occurrence (byte offset)
strrpos($s, 'l');             // 4 — last occurrence
substr($s, 0, 5);             // "Hello" (byte-based — unsafe for multibyte)

strtoupper('hello');          // "HELLO" (ASCII only)
strtolower('WORLD');          // "world"

trim('  hi  ');               // "hi"
ltrim('  hi  ');              // "hi  "
rtrim('  hi  ');              // "  hi"
trim('--foo--', '-');         // "foo"

str_replace('l', 'L', $s);                 // replace all
str_replace(['a','b'], ['A','B'], $s);      // multiple replacements
preg_replace('/\d+/', '#', $s);            // regex replace

explode(',', 'a,b,c');        // ['a','b','c']
implode('-', ['a','b','c']);  // 'a-b-c'
join('-', ['a','b','c']);     // alias of implode

str_repeat('ab', 3);          // 'ababab'
str_word_count($s);           // word count
preg_split('/\s+/', $s);      // split on whitespace

sprintf("x=%d, y=%.2f", 10, 3.14); // formatted string
number_format(1234567.891, 2, '.', ','); // "1,234,567.89"

// String building (interpolation — easiest in PHP)
$name = 'World';
$greeting = "Hello, $name!";         // double quotes — interpolates
$greeting = 'Hello, ' . $name . '!'; // concatenation
$greeting = <<<EOT                    // heredoc (interpolates)
Hello, $name!
EOT;

// Padding
str_pad('42', 5, '0', STR_PAD_LEFT); // "00042"
str_pad('hi', 6, '-', STR_PAD_BOTH); // "--hi--"

// Check empty
empty($s);     // true for '', '0', null, 0, false, []
$s === '';     // strictly empty string
strlen($s) === 0;
```

> **Key difference:** PHP strings are byte sequences — all standard string functions (`strlen`, `substr`, `strpos`) operate on bytes. For Unicode/multibyte content, use the `mb_*` functions. Go strings are also byte sequences but `range` over a string yields runes natively, and the `unicode/utf8` package provides codepoint helpers. PHP has powerful built-in string functions and native interpolation; Go uses `fmt.Sprintf` or `strings.Builder`.

> **Sources:**
> - **Go:** [Go Blog — Strings, bytes, runes](https://go.dev/blog/strings) · [pkg.go.dev/strings](https://pkg.go.dev/strings)
> - **PHP:** [PHP Manual — String functions](https://www.php.net/manual/en/ref.strings.php) · [PHP Manual — Multibyte string](https://www.php.net/manual/en/book.mbstring.php)

---

## Concurrency

### Go — Goroutines & Channels

Go concurrency is built into the language: the `go` keyword, channel types (`chan`), and `select`:
```go
// Goroutine — lightweight M:N green thread (a few KB of stack, millions possible)
go someFunction()
go func(msg string) {
    fmt.Println(msg)
}("hello")

// Unbuffered channel — send and receive both block until the other side is ready
ch := make(chan int)
go func() { ch <- 42 }()
v := <-ch // 42

// Buffered channel — send doesn't block until buffer is full
buf := make(chan string, 3)
buf <- "a"; buf <- "b"; buf <- "c"

// Range over channel until closed
jobs := make(chan int, 5)
for j := 1; j <= 5; j++ { jobs <- j }
close(jobs)
for j := range jobs { fmt.Println(j) }

// select — multiplex channels
select {
case msg := <-ch1:
    fmt.Println("ch1:", msg)
case ch2 <- data:
    fmt.Println("sent to ch2")
case <-time.After(1 * time.Second):
    fmt.Println("timeout")
default:
    fmt.Println("non-blocking")
}

// WaitGroup — wait for multiple goroutines
var wg sync.WaitGroup
for i := 0; i < 5; i++ {
    wg.Add(1)
    go func(n int) {
        defer wg.Done()
        fmt.Println("goroutine", n)
    }(i)
}
wg.Wait()

// Mutex — protect shared state
var mu sync.Mutex
var count int
go func() {
    mu.Lock()
    count++
    mu.Unlock()
}()

// context — cancellation and deadlines
ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()

go func() {
    select {
    case <-ctx.Done():
        fmt.Println("cancelled:", ctx.Err())
    }
}()
```

### PHP — Fibers (8.1+), Async Libraries

PHP is traditionally single-threaded per request. True concurrency requires external libraries or extensions:
```php
<?php
// Fibers (PHP 8.1+) — cooperative multitasking (not true parallelism)
// A Fiber can suspend itself and yield control to the caller

$fiber = new Fiber(function(): void {
    $value = Fiber::suspend('first suspension');
    echo "Resumed with: $value\n";     // "hello"
    Fiber::suspend('second suspension');
    echo "Fiber finishing\n";
});

$result = $fiber->start();             // run until first suspend
echo "Fiber yielded: $result\n";       // "first suspension"

$result = $fiber->resume('hello');     // resume, pass value in
echo "Fiber yielded: $result\n";       // "second suspension"

$fiber->resume();                      // finish the fiber
var_dump($fiber->isTerminated());      // true

// Fibers are the building block for async frameworks — they don't run in parallel

// ReactPHP — event loop based async (like Node.js)
// composer require react/event-loop react/promise
use React\EventLoop\Loop;
use React\Promise\Deferred;

$loop = Loop::get();
$deferred = new Deferred();

$loop->addTimer(1.0, function() use ($deferred) {
    $deferred->resolve('done after 1s');
});

$deferred->promise()->then(function(string $result) {
    echo $result . PHP_EOL;
});

$loop->run();

// Amp — async/await style (v3, uses Fibers internally)
// composer require amphp/amp
use Amp\Future;
use function Amp\async;
use function Amp\delay;

$future1 = async(function(): string {
    delay(1);   // non-blocking sleep
    return 'first';
});
$future2 = async(function(): string {
    delay(0.5);
    return 'second';
});

[$r1, $r2] = Future\await([$future1, $future2]); // runs concurrently

// Swoole / OpenSwoole — true coroutines with C extension
// composer require swoole/ide-helper
Swoole\Coroutine\run(function() {
    $results = [];
    $wg = new Swoole\Coroutine\WaitGroup();

    $wg->add();
    Swoole\Coroutine::create(function() use (&$results, $wg) {
        Swoole\Coroutine::sleep(0.1);
        $results[] = 'first';
        $wg->done();
    });

    $wg->add();
    Swoole\Coroutine::create(function() use (&$results, $wg) {
        $results[] = 'second';
        $wg->done();
    });

    $wg->wait();
    var_dump($results);
});

// pcntl_fork — process-based parallelism (Unix only)
$pid = pcntl_fork();
if ($pid === 0) {
    // child process
    echo "Child\n";
    exit(0);
} else {
    // parent process
    pcntl_waitpid($pid, $status);
    echo "Parent\n";
}

// parallel extension — true thread parallelism (PHP 8.x, ZTS build)
// composer require ext-parallel
$runtime = new \parallel\Runtime();
$future  = $runtime->run(function(): int {
    return array_sum(range(1, 1_000_000));
});
echo $future->value(); // 500000500000
```

> **Key difference:** Go goroutines are first-class language features — extremely cheap (a few KB), M:N scheduled, with channels for communication. PHP is fundamentally single-threaded per request; true concurrency requires extensions (Swoole, `parallel`) or process forking. PHP 8.1 Fibers enable cooperative multitasking (used by ReactPHP, Amp) but are not parallel — only one Fiber runs at a time.

> **Sources:**
> - **Go:** [A Tour of Go — Goroutines](https://go.dev/tour/concurrency/1) · [Effective Go — Concurrency](https://go.dev/doc/effective_go#concurrency)
> - **PHP:** [PHP Manual — Fibers](https://www.php.net/manual/en/language.fibers.php) · [ReactPHP](https://reactphp.org/) · [Amp](https://amphp.org/) · [Swoole](https://openswoole.com/)

---

## Memory Management

**Go** — garbage-collected with escape analysis:
```go
// Heap allocation — GC handles deallocation
p := new(int)      // *int, zero value 0
s := make([]int, 5) // slice on heap

// Stack allocation — compiler decides (escape analysis)
func local() int {
    x := 42       // likely stays on stack
    return x
}
func heap() *int {
    x := 42
    return &x     // x escapes to heap (address outlives function)
}

// GC tuning
import "runtime/debug"
debug.SetGCPercent(50)           // more aggressive GC
debug.SetMemoryLimit(512 << 20)  // 512 MiB soft limit
runtime.GC()                     // force a collection cycle

// sync.Pool — reuse objects to reduce GC pressure
var pool = sync.Pool{
    New: func() any { return make([]byte, 0, 1024) },
}
buf := pool.Get().([]byte)
// ... use buf ...
pool.Put(buf[:0])
```

**PHP** — reference counting with cycle collector:
```php
<?php
// PHP uses reference counting — objects freed when refcount reaches 0
$a = new stdClass();  // refcount=1
$b = $a;              // refcount=2 (both point to same object)
unset($a);            // refcount=1
unset($b);            // refcount=0 — freed immediately

// Circular references require the cycle collector
class Node {
    public ?Node $next = null;
}
$n1 = new Node();
$n2 = new Node();
$n1->next = $n2;
$n2->next = $n1; // cycle!
unset($n1, $n2); // refcount never reaches 0 — cycle collector must clean up

// Force cycle collection
gc_collect_cycles();
gc_disable(); // disable cycle collector for performance-critical sections
gc_enable();

// Memory info
$bytes = memory_get_usage();        // current usage
$peak  = memory_get_peak_usage();   // peak usage
$bytes = memory_get_usage(true);    // real allocation (from OS)

// Memory limit (php.ini or at runtime)
ini_set('memory_limit', '256M');

// Explicit cleanup
unset($largeArray);    // immediately decrements refcount
$arr = null;           // equivalent to unset for variables

// Generators — lazy evaluation, avoids loading everything into memory
function readLines(string $file): Generator {
    $f = fopen($file, 'r');
    try {
        while ($line = fgets($f)) {
            yield trim($line);
        }
    } finally {
        fclose($f);
    }
}

foreach (readLines('big.log') as $line) {
    process($line); // only one line in memory at a time
}

// WeakReference — reference that doesn't prevent GC
$obj = new stdClass();
$weak = WeakReference::create($obj);
unset($obj);
var_dump($weak->get()); // NULL — object was freed
```

> **Key difference:** Go uses a concurrent tri-color mark-and-sweep GC; allocation is automatic, deallocation is automatic, and you never call `free`. PHP uses reference counting with a cycle collector — most objects are freed immediately when their refcount hits zero, making memory management more predictable frame-to-frame but susceptible to cycles.

> **Sources:**
> - **Go:** [Go Blog — Getting to Go: The Journey of Go's Garbage Collector](https://go.dev/blog/ismmkeynote) · [Effective Go — Allocation with new & make](https://go.dev/doc/effective_go#allocation_new)
> - **PHP:** [PHP Manual — Garbage Collection](https://www.php.net/manual/en/features.gc.php) · [PHP Manual — Reference Counting Basics](https://www.php.net/manual/en/features.gc.refcounting-basics.php)

---

## Spread Operator & Variadic

**Go:**
```go
// Variadic function
func sum(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}

sum(1, 2, 3)        // direct call
nums := []int{1, 2, 3}
sum(nums...)        // spread slice — only works as the last argument
```

**PHP:**
```php
<?php
// Variadic function
function sum(int ...$nums): int {
    return array_sum($nums);
}

sum(1, 2, 3);
$nums = [1, 2, 3];
sum(...$nums);           // spread array

// Spread in array literals (PHP 7.4+)
$a = [1, 2, 3];
$b = [0, ...$a, 4, 5];  // [0, 1, 2, 3, 4, 5]

// Spread with string keys (PHP 8.1+)
$defaults = ['color' => 'red', 'size' => 'M'];
$overrides = ['color' => 'blue'];
$merged = [...$defaults, ...$overrides]; // ['color' => 'blue', 'size' => 'M']

// Spread for named arguments
function createTag(string $tag, string $class, string $content): string {
    return "<$tag class=\"$class\">$content</$tag>";
}
$args = ['class' => 'highlight', 'content' => 'Hello', 'tag' => 'span'];
echo createTag(...$args); // named argument spread
```

---

## Named Arguments

**Go** — no named arguments for function calls; struct literal fields are named:
```go
// Struct initialization uses field names (closest to named args)
http.Server{
    Addr:         ":8080",
    ReadTimeout:  5 * time.Second,
    WriteTimeout: 10 * time.Second,
}

// Functions always use positional arguments
fmt.Printf("%s is %d years old", name, age)
```

**PHP 8.0+** — named arguments for any function or method call:
```php
<?php
// Built-in functions
$arr = [3, 1, 4, 1, 5];
$result = array_slice(array: $arr, offset: 1, length: 3, preserve_keys: true);

// User-defined functions
function createUser(string $name, int $age = 18, bool $admin = false): array {
    return compact('name', 'age', 'admin');
}

createUser(name: 'Alice', admin: true);       // skip $age, use default
createUser(age: 25, name: 'Bob');             // any order
createUser('Charlie', admin: true);           // mix positional and named

// Named arguments with constructors
$dt = new DateTimeImmutable(datetime: '2024-01-01', timezone: new DateTimeZone('UTC'));

// Spread named arguments
$args = ['name' => 'Alice', 'admin' => true];
createUser(...$args);
```

> **Sources:**
> - **PHP:** [PHP Manual — Named arguments](https://www.php.net/manual/en/functions.arguments.php#functions.named-arguments) · [PHP 8.0 Migration](https://www.php.net/manual/en/migration80.new-features.php)

---

## Attributes & Struct Tags

**Go** — struct field tags (string metadata, used by reflection-based libraries):
```go
type User struct {
    ID       int    `json:"id"            db:"user_id"`
    Name     string `json:"name"          db:"full_name"`
    Password string `json:"-"             db:"password_hash"` // omit from JSON
    Email    string `json:"email,omitempty"`
    Age      int    `json:"age,omitempty" validate:"min=0,max=150"`
}

// Tags are strings — parsed at runtime via reflect
import "reflect"
t := reflect.TypeOf(User{})
field, _ := t.FieldByName("Name")
fmt.Println(field.Tag.Get("json")) // "name"
fmt.Println(field.Tag.Get("db"))   // "full_name"
```

**PHP 8.0+** — native Attributes (formerly "annotations"):
```php
<?php
// Define a custom attribute
#[Attribute(Attribute::TARGET_CLASS | Attribute::TARGET_METHOD)]
class Route {
    public function __construct(
        public readonly string $path,
        public readonly string $method = 'GET',
    ) {}
}

#[Attribute(Attribute::TARGET_PROPERTY)]
class Column {
    public function __construct(
        public readonly string $name,
        public readonly ?string $type = null,
    ) {}
}

// Use attributes on a class
#[Route('/users', method: 'GET')]
class UserController {
    #[Column(name: 'user_id', type: 'int')]
    private int $id;

    #[Route('/users/{id}', method: 'GET')]
    public function show(int $id): Response { ... }
}

// Read attributes at runtime via reflection
$ref = new ReflectionClass(UserController::class);

foreach ($ref->getAttributes(Route::class) as $attr) {
    $route = $attr->newInstance(); // instantiates the Route attribute
    echo $route->path;   // '/users'
    echo $route->method; // 'GET'
}

foreach ($ref->getMethods() as $method) {
    foreach ($method->getAttributes(Route::class) as $attr) {
        $route = $attr->newInstance();
        echo "{$method->getName()}: {$route->method} {$route->path}\n";
    }
}

// Built-in PHP attributes
class MyService {
    #[\Override]          // PHP 8.3 — warns if method doesn't override parent
    public function handle(): void {}
}

#[\AllowDynamicProperties] // PHP 8.2 — opt-in to dynamic properties
class LegacyModel {}

function deprecated(): void {
    // No built-in Deprecated attribute yet — use docblock @deprecated
}
```

> **Key difference:** Go struct tags are plain strings embedded in struct definitions, parsed by libraries at runtime via reflection — no compile-time semantics. PHP Attributes are first-class types instantiated at runtime, validated by the engine, and usable on classes, methods, properties, functions, constants, and parameters.

> **Sources:**
> - **Go:** [Go Spec — Struct types](https://go.dev/ref/spec#Struct_types) · [pkg.go.dev/reflect#StructTag](https://pkg.go.dev/reflect#StructTag)
> - **PHP:** [PHP Manual — Attributes](https://www.php.net/manual/en/language.attributes.php)

---

## First-Class Callables (PHP 8.1)

PHP 8.1 introduced syntax to create a `Closure` from any callable without wrapping it manually:

```php
<?php
// Before PHP 8.1 — verbose wrapping
$fn = Closure::fromCallable('strlen');
$fn = fn(string $s) => strlen($s);

// PHP 8.1+ — first-class callable syntax with ...
$fn = strlen(...);          // Closure wrapping strlen
echo $fn('hello');          // 5

$fn = strtoupper(...);
$arr = array_map(strtoupper(...), ['hello', 'world']); // ['HELLO', 'WORLD']

// Works for methods too
class Math {
    public function double(int $x): int { return $x * 2; }
    public static function triple(int $x): int { return $x * 3; }
}
$obj = new Math();
$double = $obj->double(...);        // bound method closure
$triple = Math::triple(...);        // static method closure

echo $double(5);  // 10
echo $triple(5);  // 15

// Passing method as callback
$nums = [1, 2, 3];
$result = array_map($obj->double(...), $nums); // [2, 4, 6]
```

**Go equivalent:**
```go
// Go functions are already first-class values
fn := strings.ToUpper         // function value
fmt.Println(fn("hello"))      // HELLO

// Method values are bound to their receiver
obj := MyStruct{}
method := obj.Double          // bound method value
fmt.Println(method(5))        // 10
```

> **Sources:**
> - **PHP:** [PHP Manual — First class callable syntax](https://www.php.net/manual/en/functions.first_class_callable_syntax.php)
> - **Go:** [Go Spec — Method values](https://go.dev/ref/spec#Method_values)

---

## Readonly Properties (PHP 8.1+)

PHP 8.1 added `readonly` properties — written once (on initialization), immutable thereafter. PHP 8.2 added `readonly` classes.

```php
<?php
// readonly property — can only be initialized once, from its declaring class
class Point {
    public readonly float $x;
    public readonly float $y;

    public function __construct(float $x, float $y) {
        $this->x = $x;
        $this->y = $y;
    }
}

$p = new Point(1.0, 2.0);
echo $p->x;   // 1.0
$p->x = 3.0;  // Error: Cannot modify readonly property

// Readonly + constructor promotion (common pattern)
class User {
    public function __construct(
        public readonly int $id,
        public readonly string $name,
        public readonly string $email,
    ) {}
}

$user = new User(1, 'Alice', 'alice@example.com');
echo $user->name; // Alice

// PHP 8.2 — readonly class (all promoted/declared properties are readonly)
readonly class Coordinate {
    public function __construct(
        public float $lat,
        public float $lng,
    ) {}
}

// Cloning with changes — use with() (PHP 8.4) or clone + initialization trick
$moved = clone $user;
// Directly re-assigning a readonly in a cloned object is not allowed
// Use a named constructor pattern:
class Money {
    public function __construct(
        public readonly int $amount,
        public readonly string $currency,
    ) {}

    public function withAmount(int $amount): static {
        return new static($amount, $this->currency);
    }
}
```

**Go equivalent — immutable value types:**
```go
// Go has no readonly keyword, but value semantics + unexported fields achieve this
type Point struct {
    x, y float64 // unexported — only accessible within package
}

func NewPoint(x, y float64) Point {
    return Point{x: x, y: y}
}

func (p Point) X() float64 { return p.x }
func (p Point) Y() float64 { return p.y }

// Or just use a plain struct and don't mutate it (convention)
// Go 1.20+ has atomic.Pointer etc. for synchronized reads
```

> **Sources:**
> - **PHP:** [PHP Manual — Readonly properties](https://www.php.net/manual/en/language.oop5.properties.php#language.oop5.properties.readonly-properties) · [PHP 8.2 — Readonly classes](https://wiki.php.net/rfc/readonly_classes)

---

## Constructor Promotion (PHP 8.0+)

PHP 8.0 shorthand for declaring and assigning constructor parameters as class properties:

```php
<?php
// Before PHP 8.0 — verbose
class OldUser {
    private int $id;
    private string $name;
    private string $email;

    public function __construct(int $id, string $name, string $email) {
        $this->id    = $id;
        $this->name  = $name;
        $this->email = $email;
    }
}

// PHP 8.0+ constructor promotion — same result, less code
class User {
    public function __construct(
        private int $id,
        private string $name,
        private string $email = 'unknown@example.com', // default values work
    ) {}

    public function getId(): int    { return $this->id; }
    public function getName(): string { return $this->name; }
}

// Can mix promoted and non-promoted
class ProductService {
    private array $cache = [];

    public function __construct(
        private readonly ProductRepository $repo, // promoted
        private readonly Logger $logger,          // promoted
        int $cacheSize = 100,                     // NOT promoted — no visibility keyword
    ) {
        $this->cache = array_fill(0, $cacheSize, null);
    }
}
```

**Go equivalent:**
```go
// Go has no constructor promotion — use a constructor function
type User struct {
    id    int
    name  string
    email string
}

func NewUser(id int, name, email string) User {
    return User{id: id, name: name, email: email}
}
```

> **Sources:**
> - **PHP:** [PHP Manual — Constructor promotion](https://www.php.net/manual/en/language.oop5.decon.php#language.oop5.decon.constructor.promotion)

---

## Quick Reference: Go vs PHP at a Glance

| Feature | Go | PHP 8.x |
|---|---|---|
| Typing | Static, strong | Dynamic, optional strong with `declare(strict_types=1)` |
| Compilation | Compiled to native binary | Interpreted (JIT in 8.x with opcache) |
| OOP | No classes; structs + methods | Full OOP (classes, traits, abstract, final) |
| Inheritance | No; composition via embedding | Single inheritance + interfaces + traits |
| Interfaces | Structural (implicit) | Nominal (explicit `implements`) |
| Generics | Yes (Go 1.18+) | Doc-comment only (PHPStan/Psalm) |
| Error handling | Return values | Exceptions |
| Concurrency | Goroutines + channels (built-in) | Fibers (cooperative), Swoole (coroutines), pcntl (processes) |
| Memory | Garbage collected (GC) | Reference counting + cycle collector |
| Null safety | `nil` for reference types | `null` type, `??`, `?->` operators |
| Closures | Auto-capture by reference | Explicit `use()` or auto-capture `fn` (by value) |
| Enums | `iota` constants | First-class `enum` (PHP 8.1+) |
| Package system | One package per directory | Namespaces (PSR-4 via Composer) |
| Unused imports | Compile error | Silent (no error) |
| String indexing | Byte offset | Byte offset (`mb_*` for Unicode) |
| Pointers | Yes (`*T`, `&x`, `*p`) | No; `&$ref` for references |
| Attributes/Tags | Struct field tags (strings) | Native `#[Attribute]` (PHP 8.0+) |
| Named arguments | No (only struct literals) | Yes (`fn(name: value)`) |
| Readonly fields | No keyword; convention/unexported | `readonly` property/class (PHP 8.1+) |
# go-php-comparaison
