# Bash cheat sheet with examples and explanation how things works.

## Indice
1.[Variables](#variable-declaration)

2.[Comments](#comments)

3.[Arrays](#arrays)

4.[Conditional execution](#making-conditions)

5.[Switch in bash](#switch-in-bash)

6.[Loops](#loops)

7.[Arguments](#arguments)

8.[Functions](#functions)

9.[Program options](#program-options)


## Variable declaration 

### Variable declaration is super easy, the only caveat is that should not contains spaces between the varaible name and the value

#### Correct way
```bash
foo='bar'
```

#### Wrong way
```bash
foo = 'bar'
foo= 'bar'
foo ='bar'
```

Or you can use ```""```
```bash
foo="bar"
```

If it's just a single word it's not required to use ```'' or ""```

```bash
foo=bar
```

When use ```'' ""```
> ```''``` its used for exact match but ```""``` its used for string interpolation

##### Example
```bash
message='world'
echo "Hello $message"
#Output: Hello world

echo 'Hello $message'
#Output: Hello $message
```

#### Caveat in values.

Bash dosen't diferentiate between ```true``` or ```false``` 
so if you declare a variable with this value it's treated as a string.In the others sections you'll gonna learn how to do
[Conditionals](#conditionals)

### Accesing the value of the variable

```bash
foo=bar

echo ${foo}
echo $foo
echo "$foo"

echo '$foo'  # Exact string => '$foo'
echo "${foo} !" # => bar !
```
## Comments

### Single line comment
```bash
# This is a comment
```

### Multiline comment
```bash
: '
    Multine comment
    You can cooment here to
'
```


## Arrays
Arrays in bash are dynamic.For declare one you only need to put the elemenets inside a ```()``` because this is a constructor.The spaces are interpreted as separators don't use ```,``` for that.

#### Example
```bash
elements=('foo'  'bar')

boys=('foo' 'bar' 2025)
```

### For access to the elements you should always use the sintax ```${array[index]}```.

#### Why ?

If you try to use ```$array[index]``` can cause unexpeted behavior becouse ```$array[index]``` access to the first argument.

#### Example:
```bash
val=(21 'foo' 'bar')

echo $val[0]

# Output: 21[0]

# Look at this weird behavior

echo $val[2]

# Output: 21[2]
```

Working with arrays and strings should be the same deal becouse a string it's just an array of characters but not.Bash take a diffrent aproach you should olways specify the start and end position ```${STR:position:length}``` if the length it's ommited it's gonna take the rest of the string.

```bash
foo='bar'

echo "${foo:0}"

#Output: bar


foo='bar'

echo "${foo:0:1}" 

#Output: b
```



#### Accessing the array elements
```bash
names=('mark' 'anthony')
echo ${name[0]}
# Output: mark


#String example
foo='bar'

echo "${foo:0:1}" 

#Output: b
```

#### Last element
```bash
names=('mark' 'anthony')
echo ${name[-1]}
# Output: anthony


foo='bar'
echo ${foo: -1} #The space it's requiered

#Output: r
```
You should put an space between ```:``` and ```-1``` because bash interprete this as an expansion parameter for asign a default value if ```foo``` it's not defined.

```bash
echo ${foo:-default}

#Output: default
````


#### All elements
```bash
names=('mark' 'anthony')
echo ${name[@]}
# Output: mark anthony

# Or you can use the alternative syntax
echo ${name[*]}
# Output: mark anthony

# For an string
foo='bar'
echo $foo
```

#### Number of elements
```bash
names=('mark' 'anthony')
echo ${#names[*]}
# Output: 2

name='amrk'
echo ${#name}
#Output: 4
```

#### Length of the first element
```bash
names=("mark" "anthony")
echo ${#names}
# Output: 4
```

#### Length of the nth element
```bash
names=("mark" "anthony")
echo ${#names[1]}
# Output: 7
```

#### Range
```bash
names=("mark" "anthony" "george")
echo ${names[*]:1:2}
# Output: anthony george

# In strings
name='fooz'
echo ${name:1:2}
#Output: oo
```

#### Keys or index
> ``` ${!names[*]} ```

```bash
names=("mark" "anthony" "george")
for i in ${!names[*]}; do
    echo $i
done
# Output:
#    0
#    1
#    2

#For strings
message='hello world'
length=${#message}

for ((i = 0; i < length;i++));do
    echo ${message:i:1}
done
#Output:
# h
# e
# l
# l
# o

# w
# o
# r
# l
# d

#Short syntax
message='hello world'

for ((i = 0; i < ${#message};i++));do
    echo ${message:i:1}
done

```

#### Append values
```bash
names=("mark" "anthony" "george")
names+=("marian")

echo ${names[*]}
# Output: mark anthony george marian


# In strings
foo='bar'

foo+="-more-text"

echo $foo
#Output: bar-more-text
```

#### Append multiple values values
```bash
names=("mark" "anthony" "george")
names+=("marian" 22 "user")

echo ${names[*]}

# Output: mark anthony george marian 22 user
```

#### Delete value
```bash
names=("mark" "anthony" "george")
names+=("marian")

unset names[0]

echo ${names[*]}

# Output: anthony george marian
```

## Making conditions
Conditions are expresions that returns 0 or 1 in Unix systems, you should interprate this like ```true``` or ```false```.Bash do not evaluate conditional expresions like other programming lenguages.Intead it's check the status code of the commands.
> ``` 0 means true/successs ```

> ``` Non-cero (1-255) means false/failure ```

For see the practical examples you'll gonna use the ```if``` flow that allows conditional execution of commands.
### ``` if ```

```bash
if COMMAND_OUTPUT ; then
    # expresions or commands
fi

#or
if COMMAND_OUTPUT ; then
    # expresions or commands
elif COMMAND_OUTPUT;then ...
elif COMMAND_OUTPUT;then ...

fi
```


### The difference between ```[]``` and ```[[]]```

- ```[ ]```  is actually an alias for the ```test``` command
- ```[[ ]] ``` is a bash keyword with more features like regular expresions,etc;

#### Example with ```[ ]```
```bash
if [ 1 -eq 1 ];then
    echo "valid condition"
fi
# Output: valid condition
```

#### Example with ```[[ ]]```
```bash
var="~/.config/nvim/init.lua"

if [[ $var == *.lua ]];then
    echo "it's a lua file"
fi
```

#### Example with a command
```bash
if ping -c 1 googe.com; then
    echo "google service it's running"
else
    echo "google it's not available"
fi
```
Bash allow you multiple commands into the same condition

```bash
if condition1 && condition2;then ...
if condition1 || condition2;then ...

```

### Why should always use ```[[ ]] ``` for conditions for modern software

- ```[[]] ``` it's generally more faster that ```[]``` becouse it's builtin.

- ```[]``` can fail if variable it's empty or have white spaces.

```bash
if [ $input == "text input" ];then
    echo "hello world"
fi
#Output: ./test.sh: line 3: [text: command not found
```

Empty variable
```bash
input=""

if [ $input == "text input" ];then
    echo "hello world"
fi
#Output: ./test.sh: line 3: [: missing `]'
```
- More intuitive logical operators, ```[[]]``` allows ```&&``` and ```||``` directly. ```[]``` requires ```-a``` for ```&&``` and ```-o``` for ```||``` .


#### When use ```[]```
- For compatibility with ```sh```
- Some embeded systems


### Comparing numbers

- [[ NUM1 -eq NUM2 ]] Equal
- [[ NUM1 -ne NUM2 ]] Not equal
- [[ NUM1 -lt NUM2 ]] Less than
- [[ NUM1 -le NUM2 ]] Less than or equal
- [[ NUM1 -gt NUM2 ]] Greater than
- [[ NUM1 -ge NUM2 ]] Greater than or equal


##### Using ```(())```
- (( NUM1 != NUM2 )) Not equal
- (( NUM1 == NUM2 )) Equal
- (( NUM1 < NUM2 )) Less than
- (( NUM1 <= NUM2 )) Less than or equal
- (( NUM1 > NUM2 )) Greater than
- (( NUM1 >= NUM2 )) Greater than or equal


##### Example
```bash
input1=1
input2=3

if [[ input1 -lt input2 ]];then
    echo "first number is less"
fi
```
For arithmetic expresion it's more recomended to use ```(())``` becouse:
- It's more easy to work with.
- Allows variable assignacion ```(( x = 5 ))```
- More operands ```++, --,  +=, -=```

```bash
input1=1

if (( input1 == 1 ));then
    echo "Equal"
fi

```


### String conditions

- [[ -z STR ]] The string is empty
- [[ -n STR ]] The string is not empty
- [[ STR1 == STR2 ]] Equal
- [[ STR1 != STR2 ]] Not equal
- [[ STR1 < STR2 ]] Less than(ASCII)
- [[ STR1 > STR2 ]] Greater than(ASCII)
- [[ STR1 =~ STR2 ]] Regex

```bash
name="foo"

if [[ -z "$name" ]];then
    echo "the string it's empty"
elif [[ -n "$name" ]];then
    echo "the string its not empty $name"
else
    echo "this don't gonna happend"
fi

```
Why use the ```$variable``` surronded with ```""``` becouse if the string have more than one word it's gonna lead unexpeted behaviors.

#### Example
```bash
num1=1
num2=2

name="foo"

if (( num1 == 1 )) && (( num2 == 2 )) &&
    [[ -n "$name" && ${#name} -eq 3 ]];then
    echo "valid condition"
fi
```


## Switch in bash
In bash there is not a ```switch``` structure like others programming lengiages but you can archive the same result with ```case```

```bash
case $VAR in
    pattern )
        # Some comands or definitions
    ;; # End of the pattern
    pattern1 | pattern2) # Or operator
        ...
    ;;
    *)
        # Default command
    ;;
esac
```

#### Example
```bash
num=1

case $num in
    1 )
        echo "the number is one"
    ;;
    2 | 10)
        echo "it's in range 2-10"
    ;;
    *)
        echo "unknow number"
    ;;
esac
```

#### Example with patterns
```bash
cfg_file='config.json'

case $cfg_file in 
    *.json)
        echo "configuration file"
    ;; 
    *.png|*.jpeg|*.jpg)
        echo "image file"
    ;;
    *)
        echo "unknow file type"
    ;;
esac
#Output: configuration file
```

## Loops

### Basic for loop
```bash
for i in ~/.config/*;do
    echo $i
done
# Output: print all the content of the directory
```

### C-like for loop
```bash
for ((i = 0; i < 100;i++));do
    echo $i
done
# Output:
# 0
# 1
# ...
# 99
```

### Range loop
```bash
for i in {2..6};do
    echo $i
done
# Output:
# 2
# 3
# 4
# 5
# 6

```

### With autoincrement and autodecrement
```bash
i=0
while (( i < 100 ));do
    echo $i
    (( i ++ ))
done
# Output:
# 0
# 1
# ...
# 99

i=100
while (( i >= 0 ));do
    echo $i
    (( i -- ))
done
# Output:
# 100
# 99
# ...
# 1
# 0
```

### With continue and break
```bash
for (( i = 0; i < 100;i++));do
    if (( i >= 20 && i <= 50 ));then
        continue;
    fi
    if (( i == 90 ));then
        break;
    fi
    echo "valid range $i"
done
#Output:
# 0
# 1
# ...
# 19
# 51
# ...
# 88
# 89
```

### Forever
```bash
while true;do
    echo "forever"
done

# Shorthand
while :;do
    echo "forever"
done
```

## Arguments

You can access to the arguments of your program just like [variable declaration](#accesing-the-value-of-the-variable) but with the position of the argument

> ./script.sh foo bar steve
```bash
echo $1 $2 $3

# foo bar steve

# The argument $0 is the name of the script
echo $0
# ./script.sh
```

### Number of arguments
> ./script.sh foo bar 

```bash
echo $#

# It's gonna print 2
```

### Process id of the shell


> ./script.sh foo bar 

```bash
echo $$

# It's gonna print some number Example: 2711
```

#### All arguments


> ./script.sh foo bar 

```bash
echo $*

# foo bar
```

#### All arguments starting from first


> ./script.sh foo bar 

```bash
echo $@

# foo bar
```

## Functions
Functions are way to reuse code in your programs.If the program become larger using functions is a way to tackle the complexity.

### Function declaration
```bash
my_func() {

}
```

### Varaible declaration and the use of ```local```
When you declare a variable in a function with out the keyworl local the variable become available to the rest of the script and can cause unexpected behaviors and name collisions.
```bash
func () {
    i=100
}

func # this is how you made a function call

func args1 arg2 # Arguments

echo $i
#Output: 100
```

### Functions arguments works the same as [program arguments](#arguments) except for ```$0``` that access to the name of the script

```bash
func () {
  local i=100
  echo $1
}

func 'user'
#Output: user
```


## Program options
Program options change the default behavior of some funtions in bash this should be defined at the begging of the program.

```bash
# Used to exit upon errors, avoiding cascading errors
set -o errexit

# Unveils hidden failures
set -o pipefail

# Expose unset variavles
set -o nounset

#No matching glog are removed
# '*.foo' => ''
set -o nullglob


# Allow ** for recursive matches
set -o globstart

```