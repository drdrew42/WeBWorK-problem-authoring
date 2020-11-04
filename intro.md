# Fundamentals (not covered)
## Perl 101
* comments begin with `#`
  * on their own lines, or at the end of a line of code
* all "lines" of code end with `;`
  * one line of code can be spread across multiple newlines
  * blocks of code `{}` from conditionals (`if`) or loops (`for` or `while`) don't require semicolon after the close brace
* variables `$variableName`
  * start with `$`
  * use them to hold
    * numbers `$var1 = 1;`
    * strings (text) `$var2 = 'Hello world!';`
    * strings with single-quotes are 'literal' -- no variable substitution
    * strings with double-quotes will replace variables with their respective values

## Perl 201 
* `$variables` are scalar - single-valued
  * that value might be a reference to a more complex data structure
* arrays are ordered lists: `@arrayVariable = (1,2,3);`
  * scalars from array: `$arrayVariable[0] + $arrayVariable[2] == 4` would be `true` 
* hashes are key-value pairs:
  * assign key-value pairs: `%hashVariable = (key => 'value');`
  * scalar from hash: `$hashVariable{key} == 'value';` would be `true`
* caveats for arrays and hashes: [they can be defined as scalars, in which case, the method for accessing their values changes slightly.](https://perlmaven.com/dereference-hash-array)
  * technically, my recommendations above are more accurately classified as 'lists' rather than 'arrays' or 'hashes' (don't @ me).
* For the adventurous: [Learn Perl in about 2 hours 30 minutes](https://qntm.org/perl_en)


# Introduction
* WeBWorK has never stopped evolving (~25 years of unceasing growth!)
* The OPL reflects the state of problem development from an increasingly outdated model
  * OPL problems DO implement best practices for tagging/metadata
* Editing existing library problems is a good start, but be aware of these limitations
* Major steps in evolution:
  * MathObjects / Contexts
  * PGML
* Problem layout
  * Tagging/metadata
  * Problem setup
  * Problem text (+ hints/solutions)

# Setting up your problem

## Perl for randomization
* **bread and butter:** `$parameter = random(0,9,1);`
  * random(_min_, _max_, [_step=1_]);
  * set your bounds for the randomization
  * "step" is optional, and defaults to 1
  * possible results: `min, min+step, min+2*step, ...`
    * if _max - min_ is not a multiple of _step_, then _max_ is not a possible result of the randomization
    * e.g. `random(0,1.5,0.2)`


## Math Objects - what are they?
* [Reference](https://webwork.maa.org/wiki/Introduction_to_MathObjects)
* "sophisticated" variables
  * they have methods and properties
    * methods are functions (actions)
    * properties are static (configurations)
* they know things about themselves
  * how their representation might be "reduced"
  * actions such as variable substitution/evaluation or their derivative with respect to a given variable
  * their representation in TeX
  * how their comparison should be evaluated (answer checker)


## Contexts - what are they?
* [Reference](https://webwork.maa.org/wiki/Introduction_to_Contexts)
* settings for the MathObjects in their 'scope' (you may use multiple contexts when building a problem)
  * [variables](https://webwork.maa.org/wiki/Introduction_to_Contexts#Variables), their type and domain 
    * `x => [Real, limits=>[0,2]]`
  * [reduction rules](https://webwork.maa.org/wiki/Reduction_rules_for_MathObject_Formulas)-- things like:
    * '1\*x+(-1)*y' reduces to `x-y`
    * '(-x)+y' reduces to `y-x` 
    * '(-x)-y' reduces to `-(x+y)`
  * [constants](https://webwork.maa.org/wiki/Introduction_to_Contexts#Constants) (_pi_ or _e_)
  * [strings](https://webwork.maa.org/wiki/Introduction_to_Contexts#Strings) (_NONE_ or _DNE_)
  * options (aka '[flags](https://webwork.maa.org/wiki/Context_flags)')
    * reduceConstants (display `3/4` or `0.75`?)
    * reduceConstantFunctions (display `\sin(\pi)` or `0`?)
  * functions / operations (enable/disable)
* [context controls the 'default' answer-checking settings](https://webwork.maa.org/wiki/Answer_Checkers_and_the_Context#Disabling_Functions)
  * tolerance
  * acceptable operators and/or functions
* context determines the available MathObject classes

## "Common" Context/MO combos (no add'l loadMacros req'd)
* [Reference](https://webwork.maa.org/wiki/Category:MathObject_Classes)
* String -- can be implemented in (almost?) any context
* List -- can be constructed from any MathObjects already implemented in your problem
* Numeric
  * Real
  * Formula
* LimitedNumeric (Real numbers, but with operations & functions disabled)
  * e.g. `1` is okay, but `sqrt(1)` and `cos(0)` are not
* Complex
* Point (dimension depends on number of variables set in context)
* Vector (dimension depends on number of variables set in context)
* Matrix
* Interval
  * Set
  * Union


## Advanced Contexts (include MOs + require loadMacros)
* [Reference](https://webwork.maa.org/wiki/Specialized_contexts)
* Fraction
* Limited:
  * LimitedPolynomial
  * LimitedComplex
  * LimitedPowers
  * etc...
* [modify](https://webwork.maa.org/wiki/Modifying_Contexts_(advanced)) existing contexts with your own configuration settings

## Parsers: may or may not implement their own specific context
* [Reference](https://webwork.maa.org/wiki/Specialized_parsers)
* Assignment (x = ...)
* FormulaUpToConstant: f(x) + C (or almost any letter for C)
* Implicit Equation (implicit plane for linear)
* PopUp & RadioButtons (for multiple choice)

# Problem TEXT

## PGML
* [Reference](https://webwork.maa.org/wiki/Introduction_to_PGML)
* BEGIN_PGML / END_PGML
  * won't execute code in this block (without 
* mostly-standard markdown
  * bold
  * italics
  * numbered lists
  * indent / newline
* variable references `[$var]`
* TeX math-mode 
  * inline: ``[`\frac{1}{2}`]``
  * display-style inline: `[``\frac{1}{2}``]`
  * display-style (double-dollar): `[```\frac{1}{2}```]`
  * MathObjects know how to TeX themselves: ``[`[$formula]`]``
* executing code `[@code@]`
* embedding more complex objects - `[stuff]*`, `[stuff]**`, or `[stuff]***`
  * are you generating HTML or PGML in bracketed code?
  * as Mike says, just keep adding asterisks until it works ;P
* answer blanks: `[_]{$answerVariable}`

## Answer checkers
* this is a **DEEP** rabbit hole
* providing an answer as a MathObject makes this "just work"
* `$showPartialCorrectAnswers = 1;` (or `0`)
  * must be set anywhere _outside_ of PGML blocks
  * itemized correct/incorrect
  * itemized feedback
  * partial credit
* [Answer-checker options](https://webwork.maa.org/wiki/Answer_Checker_Options_(MathObjects)) [_]{$answerObject->cmp(...options)}

## PGML Hints/Solutions
* BEGIN_PGML_HINT / END
  * $showHint = number; (how many attempts before hints are shown?)
  * as with showPartialCorrectAnswers, must be set outside of PGML blocks
* BEGIN_PGML_SOLUTION / END
  * appears to student _after_ the 'solutions date'
