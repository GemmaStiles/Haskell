Chapter 9: Lists
Lists
Lists are a way for us to refer to and process a collection of values
They are also an infinite series of values, usually generated by a function, which allows them to acts as a stream datatype
The list datatype
The list datatype in Haskell is defined like this:

What this actually means:

[1] The datatype with the type constructor [ ]
[2] Takes a single type constructor argument 'a'
[3] At the term level can be constructed via
[4] nullary constructor [ ]
[5] or it can be constructed by
[6] data constructor ( : ) which is a product of a value of the type a we mentioned in the type constructor and a value of type [a], that is, "more list"
The cons constructor ( : ) is an infix data constructor
Since it takes two arguments, it is a product of those two arguments
It goes between the two arguments
Pattern matching on lists
We can do pattern matching on data constructors and the data constructors for lists are no exception
Here we are matching on the first argument to the infix constructor, ignoring the rest of the list and returning the value:

We can do the opposite of this as well:

Both myHead and myTail have refutable patterns and no fallback, so if we try to pass in an empty list as an argument they can't pattern match
Could get around this by including a base case - myTail [ ] = [ ]
A better way to handle this situation would be with a datatype called Maybe - full explanation coming later
datatype for Maybe has two potential values:

The is to make the failure case explicit, as a program gets longer and more complex this becomes more useful
Rewriting the myTail function to use Maybe would look like this:

List syntactic sugar
Haskell has some syntactic sugar to accomodate the use of lists
Instead of writing:

Haskell lets us write:

When we talk about lists we often talk about them in terms of "cons cells" and spines
The syntactic sugar obscures the underlying construction
The cons cells are the lists datatype's second data constructor, a : [a] - the result of recursively prepending a value to "more list"
The cons cell is a conceptual space that values may inhabit.....????
The spine is the connective structure that holds the cons cells together and in place
This nests the con cells rather than ordering them in a right-to-left row
Using ranges to construct lists
There are several ways to construct lists, the simplest is with ranges
The basic syntax is to make a list that has the element you want to start the list from followed by two dots followed by the value you want as the final element in the list
Examples

Be aware that enumFromTo must have it's first argument lower than the second, otherwise it will just return an empty list
Extracting portions of lists
Common functions for extracting portions of a list and dividing lists into parts:

The higher-order functions takeWhile and dropWhile are a bit different

With both of these, if you hit something that doesn't match the condition it stops running the function

List comprehensions
List comprehensions are  a means of generating a new list form a list or lists
This stems from the concept of set comprehensions in mathematics, and the syntax is similar
They must have at lest one list, called the generator, that gives the input for the comprehension, that is provides the set of items from which the new list will be constructed
They may have conditions to determine which elements are drawn from from the list and / or functions applied to those elements
Example

[1] This is the output function that will apply to the members of the list we indicate
[2] The pipe here designates the separation between the output function and the input
[3] The input set: a generator list and a variable that represent the elements that will be drawn from the list - this says "from a list of numbers from 1-10, take (←) each element as an input to the output function"

This will produce a list that includes the square of every number from 1 to 10
Adding predicates
List comprehensions can optionally take predicates that limit the elements drawn from the generator list
The predicates must evaluate to Bool values
The items drawn from the list and passed to the output function will only be those that met the True case in the predicate
Example

Here we are specifying that the only elements to take from the generator list as x are those that, when divided by 2, have a remainder or zero
We can write list comprehensions that have multiple generators
The rightmost generator will be exhausted first, then the second rightmost, and so on
The two input get separated by a comma

We could put a condition on that as well, add another comma and write the predicate

Can use a list comprehension as the generator for another list comprehension

List comprehensions with Strings
Strings are lists so list comprehension can also be used with strings
There is a standard function called elem that tells you whether an element is in a list or not - it evaluates to a Bool so it is useful in list comprehensions:

We can write a list comprehension to remove all lowercase letters from a string, our condition is that we only want to take x from our generator list when meets the condition that it is an element of the list of capital letters

This could be generalised to accept different strings as inputs
We can do this by naming a function that will take one argument and use that as the generator string for our list comprehension (the function argument and the generator string will need to be the same thing)

We use xs for our function argument to indicate to ourselves that it's a list and that the x is plural
It is idiomatic to use a "plural" variable for list arguments, but not necessary
Spines and non-strict evaluation
When we talk about data structures in Haskell, particularly lists, sequences and tress, we talk about them having a spine
This is the connective structure that ties the collection of values together
In the case of a list the spine is usually textually represented by the recursive cons ( : ) operators
Given the data [1, 2, 3] we get a list that looks like:

The problem with the 1: (2 : (3 : [ ] )) representation is that it makes it seem like the value 1 exists "before the cons ( : ) cell that contains it
It is actually the cons cell that contains the values
You can evaluate cons cells independently of what they contain
It is possible to evaluate just the spine of the list without evaluating the individual values
It is also possible to evaluate only part of the spine of a list and not the rest of it
Evaluation of the example above proceeds down the spine
Construction of the list when that is necessary however proceeds up the spine
The spine is the recursive series of cons constructors and the underscores represent the values contained by the cons cells that we are ignoring and not evaluating:

Spine can be used in reference to data structures that aren't just lists
Using GHCI's :sprint command
There is a command called sprint in GHCi which allows us to print variables and see what has been evaluated already
The underscore represents expressions that haven't been evaluated yet
:sprint does have some behavioural quirks
An example of a defined list using enumFromTo and then asking for the state of blah to see if it has been evaluated:

The above indicates that it is totally unevaluated
Now we will take one value from blah and evaluate it by forcing GHCi to print the expression:

This shows an evaluates cons cell and the first value 'a'
We can then take two values:

We can keep going and evaluate the list one value at a time:

The length function is only strict in the spine, meaning it only forces evaluation of the spine of a list, not the actual values
When we use length on blah, :sprint will behave as though we had forced evaluation of the values as well:

This is one of the quirks of how GHCi evaluates code
Spines are evaluated independently of values
Values in Haskell get reduced to weak head normal form by default
'Normal form' (NF) means the expression is fully evaluated
'Weak head normal form'  (WHNF) means the expression is only evaluated as far as is necessary to reach a data constructor
Examples

Transforming lists of values
Because Haskell uses non-strict evaluation, we tend to use higher order functions for transforming data rather than manually recursing over and over
A common use case would be wanting to return a list with a function applied uniformly to all values within the list
We can use the map or fmap functions - map can only be used with [ ], fmap is defined in a typeclass named Functor and can be applied to data other than lists
Some examples using map and fmap:

The types of map and fmap are:

Page 300 -> 303 = brain melty
Filtering lists of values
filter takes a list an input and returns a new list consisting solely of the values in the input list that meet a certain condition

filter has the following definition:

Filtering takes a function that returns a Bool value, maps that function over a list, then returns a new list of all the values that met the condition
Important to note it builds a new list, it does not mutate the existing list
filter can handle many types of arguments, the following example does the same things as filter even but with anonymous function syntax:

List comprehensions are another way of filtering lists as well:

Zipping lists
Zipping lists together is a means of combining values from multiple lists into a single list
There are functions that allow us to do that, lets look at zip:

One thing with zip is that it will stop as soon as one of the lists runs out of values
It will return an empty list if either of the lists are empty
We can use unzip to recover the lists as they were before they were zipped:

Information can be lost in this process (zip stops on the shortest list)
We can also use zipWith to apply a function to the values of two lists in parallel
Example

[1] A function with two arguments - the type variables of the arguments and result align with the type variable in the lists
[2] The first input list
[3] The second input list
[4] The output list created from applying the function to the values in the input lists
