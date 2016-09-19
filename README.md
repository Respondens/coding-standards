# Coding standards

This document is meant as a base for all languages. However, for CSS & Javascript we use the [Trello CSS Style Guide](https://github.com/trello/trellisheets/blob/master/styleguide.md) and the [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript) respectively. Therefor, most examples are in php / mysql.

- [Principles](#principles)
	- [Calmth](#calmth)
	- [Consistency](#consistency)
	- [Minimal difference](#minimal-difference)
- [Flow of code](#flow-of-code)
	- [Less levels of indenting: calmth](#less-levels-of-indenting-calmth)
	- [Early return: calmth](#early-return-calmth)
	- [Don't use else: calmth](#dont-use-else-calmth)
	- [Contain logic: calmth & consistency](#contain-logic-calmth--consistency)
	- [Don't take shortcuts: calmth](#dont-take-shortcuts-calmth)
	- [Collections instead of arrays: consistency](#collections-instead-of-arrays-consistency)
	- [Tell, don't ask: consistency](#tell-dont-ask-consistency)
- [Indents](#indents)
	- [Empty lines: calmth](#empty-lines-calmth)
	- [Right align SQL: calmth](#right-align-sql-calmth)
	- [Tabs vs spaces](#tabs-vs-spaces)
- [Punctuation](#punctuation)
	- [Curly braces: consistency](#curly-braces-consistency)
	- [Follow-up control statements: calmth](#follow-up-control-statements-calmth)
	- [Spaces around syntax: calmth & consistency](#spaces-around-syntax-calmth--consistency)
	- [Short array syntax: calmth & consistency](#short-array-syntax-calmth--consistency)
	- [Quotes: consistency](#quotes-consistency)
- [Minimal difference](#minimal-difference)
	- [End of array](#end-of-array)
	- [End of file](#end-of-file)
	- [SQL statements](#sql-statements)


# Principles

The following standards are based on three principles:

### Calmth

To make reading the code calm.

For example, putting an `elseif` on a own line after the closing curly brace of the `if`.

``` php
// bad
if ($calmth === true) {
    // do something complex
} elseif ($something === complex) {
    // something else
}

// good
if ($calmth === true) {
    // do something complex
}
elseif ($something === complex) {
    // something else
}
```

### Consistency

As said in the introduction, choose the same rule, no matter of the context.

For example, placing curly braces on the same line for every statement or structure.

``` php
// bad
class User
{
    function __construct()
    {
        if ($same === false) {

// good
class User {
    function __construct() {
        if ($same === false) {
```

### Minimal difference

Making sure that a diff doesn't mark lines changed if they didn't change functionally.

For example, adding a comma after the last element in an array. When adding an extra item at the end, you shouldn't need to add a comma to the previous line.

``` php
// bad
$options = [
    'one' => 1,
    'two' => 2
];

// good
$options = [
    'one' => 1,
    'two' => 2,
];
```


# Flow of code

*A lot of these rules come from Object Calisthenics, see http://www.slideshare.net/rdohms/your-code-sucks-lets-fix-it-15471808, http://programmers.stackexchange.com/a/206867/24853. PHP CS fixer can automatically fix a few of these, see https://github.com/object-calisthenics/phpcs-calisthenics-rules.*

### Less levels of indenting: calmth

Keep indenting levels inside methods small, preferable to a single level. I.e. not an if inside a `foreach` inside a `foreach`. Keeping the indenting level small makes reading a method calm for the eye and less complex for the mind. You can use early returns, move logic to separate methods, etc.

*NB, this is rule #1 from Object Calisthenics: Only one level of indentation.*

``` php
// bad
foreach ($array => $value) {
    $validKeys = [];
    foreach ($value['keys'] as $key) {
        if (isValid($key)) {
            $validKeys[] = $key;
        }
    }
}

// good
foreach ($array => $value) {
    $validKeys = array_filter($value['keys'], 'isValid');
}
```

### Early return: calmth

The happy flow should be un-indented as much as possible. This means using early returns for validation or exceptions.

``` php
// bad
if (isValid($input)) {
    $output = 'foo';
}
else {
    throw new exception(...);
}

return $output;

// good
if (isValid($input) === false) {
    throw new exception(...);
}

$output = 'foo';

return $output;
```

### Don't use else: calmth

Try to use `else` as less as possible. Instead use early returns or default + edge case flows.

*NB, this is rule #2 from Object Calisthenics: Do not use "else" keyword*

``` php
// bad
if ($context === 'edge case') {
    $option = 'different';
}
else {
    $option = 'normal';
}

return $option;

// good
$option = 'normal';
if ($context === 'edge case') {
    $option = 'different';
}

return $option;
```

### Contain logic: calmth & consistency

Put logic in methods or variable names to make processing that logic more readable. Also, by moving the logic to separate methods you make it more consistent as it won't change in other implementations.

*NB, this is rule #3 from Object Calisthenics: Wrap primitives*

*NB, this is rule #4 from Object Calisthenics: Use only one object operator per line*

One guideline that can help is to keep class and method length small. This helps to move logic to its own contained place and re-use it.

*NB, this is rule #6 from Object Calisthenics: Keep your classes small*

``` php
// bad
if (strpos($value, 'a'))
// good
$startAt = strpos($value, 'a');
if ($startAt)

// bad
if ($object->value === 'a')
// good
if ($object->isA() === true)

// bad
if ($object->sub()->key === $input)
// good
$sub = $object->sub();
$subKey = $sub->key;
if ($subKey === $input)

// bad
for ($i=0; $i<count($array); $i++)
// good
$length = count($array);
for ($i=0; $i<$length; $i++)
```

### Don't take shortcuts: calmth

It is okay to be lengthy if it becomes more readable. Don't abbreviate or try to put everything in a single line of code, write out the steps you take to get somewhere. That makes it easy to improve upon the separate parts. Hungarian notation can also be done better in the value itself (see [Contain logic](#Contain-logic)) or by actually writing it out.

*NB, this is rule #5 from Object Calisthenics: Do not abbreviate*

``` php
// bad
$qry = "SELECT ...";
// good
$query = "SELECT ...";

// bad
$tr = executeQuery($qry);
// good
$tableRow = executeQuery($query);

// bad
$sValue = json_encode($aValue);
// good
$json = json_encode($array);

// bad
$out = trim(substr($in, strpos($in, 'a')));
// good
$startAt = strpos($in, 'a');
if ($startAt === false) {
    // handle edge case
}

$type = substr($in, $startAt);
$cleanType = trim($type);
```

### Collections instead of arrays: consistency

Make handling items easier by using collections (like `\ArrayObject`) instead of arrays. Then it is easy to make sure all elements are of the same type.

*NB, this is rule #8 from Object Calisthenics: Use first-class collections*

### Tell, don't ask: consistency

It can help to think "Tell, don't ask". Use methods which perform an action to let the object handle the implementation itself. Instead of using the properties (or getters) of an object to make decisions in other unrelated code. This is related to [Contain logic](#contain-logic).

*NB, this is rule #9 from Object Calisthenics: No getters/setters/properties, or, Tell don't ask*

``` php
// bad
$final = array('success', 'fail');
if (in_array($object->getStatus(), $final)) {
    // handle edge case
}

// good
$object->status = 'success';
if ($object->hasFinalStatus()) {
    // handle edge case
}

$object->markAsSuccess();
```


# Indents

### Empty lines: calmth

Empty lines should indent to the same level of the surrounding code. This makes your cursor not jump around when you step through the code.

### Right align SQL: calmth

SQL statements should be right aligned.

``` sql
-- bad
SELECT *
FROM `table`
JOIN `other`
ON `other`.`id` = `table`.`fk`
WHERE `column` = 'foo'
OR `column` = 'bar';

-- good
SELECT *
  FROM `table`
  JOIN `other`
    ON `other`.`id` = `table`.`fk`
 WHERE `column` = 'foo'
    OR `column` = 'bar'
;
```

### Tabs vs spaces

*There is no principle governing this choice. We chose tabs over spaces, however, the other way around is also fine according to our principles.*

Tabs make it possible that everyone can choose the indent length they feel comfortable at. Even more so, you can change the indent length depending on difficulty of the task.

If you keep tabs everywhere, there is never a difference in length as that is just a formatting on display instead of part of the code.


# Punctuation

### Curly braces: consistency

Curly braces should be on the same as the opening statement. For all cases of curly braces. See also the example in the principles chapter.

- `class x {`
- `function x(...) {`
- `if (...) {`
- `elseif (...) {`
- `switch (...) {`

### Follow-up control statements: calmth

Follow-up control statements `elseif` and `else` should be on their own line to keep things readable. This prevents multiple lines following each other when they actually don't have anything to do with each other. It gives you a reading pause when continuing to the next case. See also the example in the principles chapter.

### Spaces around syntax: calmth & consistency

Add spaces between keywords and punctuation as you would in normal English language.

- `if ($a === true) {`
- `foreach ($array as $index => $value) {`
- `function x($a, $b) {`
- `$a = 'foo';`
- `for ($i = 0; $i < $count; $i++) {`

### Short array syntax: calmth & consistency

Use the short array syntax as it reads calmer and as it is consistent with our javascript.

``` php
// bad
$options = array(
    'one' => 1,
    'two' => 2,
);

// good
$options = [
    'one' => 1,
    'two' => 2,
];
```

### Quotes: consistency

- Use single quotes as the base for everything.
- Use concatenation instead of variable parsing inside double quotes.
- Use double quotes for SQL statements as single quotes are so common inside SQL.
- Use double quotes also for non-SQL strings with a lot of single quotes.
- Prefer constants like `PHP_EOL` instead of a newline character inside double quotes.


# Minimal difference

### End of array

Add a comma after the last element in an array. When adding an extra item at the end, you shouldn't need to add a comma to the previous line.

See also the example in the principles chapter.

### End of file

Add an empty newline as the last newline. This gives nicer readable git diffs and patch files.

### SQL statements

Write SQL so you don't have to change lines when just adding other lines. Thus add AND, OR, etc. statements at the start of a line. Also, close a SQL query with a semicolon on its own line. When adding a new statement to the query, you don't want to get a diff on the line before if the semicolon was removed there.

``` sql
-- bad
SELECT *
  FROM `table`
 WHERE `column` = 'foo' AND
       `column` = 'bar';

-- good
SELECT *
  FROM `table`
 WHERE `column` = 'foo'
   AND `column` = 'bar'
;
```
