# pytable

A simple program for generating small tables.
Mostly a wrapper around the excellent `tabulate` package.

# Usage

```
pytable [-h] [--float format[,format...]] [--int format[,format...]]
               [--format TABLEFMT]
               <name>=<expr> [<name>=<expr> ...]
```

`pytable` expects 1 or more arguments with the form `<name>=<expr>` where '<name>' is the column name
and `<expr>` is a python expression for generating column values.

`pytable` will read lines from `stdin`, guess their type (either `int`, `float` or `str` as a
fallback), and store the parsed value in the special variable called `_`.

e.g.
```
seq 1 10 | pytable a=_ b=_+1
```

yields
```
  a    b
---  ---
  1    2
  2    3
  3    4
  4    5
  5    6
  6    7
  7    8
  8    9
  9   10
 10   11
```


expressions are evaluated in the order they appear in the argument list, this means that you can
reference the values of other columns as long as you define the column first.

This works
```
pytable a=_ b=_+1
```

this does not 

```
pytable b=_+1 a=_
```

Column names with a leading underscore ("_") can be referenced in other columns, but don't appear in
the final output


```
seq 10 | pytable a=_+1 _secret=2*a b=_secret/7
```

```
  a         b
---  --------
  2  0.571429
  3  0.857143
  4  1.14286
  5  1.42857
  6  1.71429
  7  2
  8  2.28571
  9  2.57143
 10  2.85714
 11  3.14286
 ```

Expressions have access to python's `math` module

```
seq 1 10 | pytable a=_ b=log10(a) c=log(a) d=log2(a)
```

and you can fully qualify names to disambiguate

```
seq 1 10 | pytable a=_ log10=math.log10(a) ln=log(a) log2=math.log2(a)
```

