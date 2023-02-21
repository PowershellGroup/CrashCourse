# PowerShell-Course

This readme contains the content for the entire course.

whats missing?

- [ ] better task for new-aduser
- [ ] short explanation of wildcards

## Prerequisits

The following tools must be installed before the course starts.

- Visual Studio Code - https://code.visualstudio.com/
- GitHub Desktop - https://desktop.github.com/ (unless you allready know git.)
- Powershell 7 - https://github.com/PowerShell/PowerShell#get-powershell

The following should be done before the course starts.

- Create a Github account https://github.com/
- setup my predictive intellisense powershell config

### My Predictive intellisense powerhell config

Predictive intellisense does this.

![predictive intellisense sample](/assets/images/2022-07-04-10-43-20.png)

uses your previously typed in commands to predict what you want to type in next. Big help in learning.

here is the config

```powershell
# these are my predictive intellisense settings. 
# grabbed the essentials from the link below.
# https://devblogs.microsoft.com/powershell/announcing-psreadline-2-1-with-predictive-intellisense/
Set-PSReadLineOption -PredictionSource History
Set-PSReadLineOption -HistorySearchCursorMovesToEnd
Set-PSReadLineKeyHandler -Key UpArrow -Function HistorySearchBackward
Set-PSReadLineKeyHandler -Key DownArrow -Function HistorySearchForward
Set-PSReadLineKeyHandler -Chord "Ctrl+f" -Function ForwardWord
Set-PSReadLineKeyHandler -Chord Shift+Tab -Function MenuComplete
Set-PSReadLineKeyHandler -Chord Ctrl+b -Function BackwardWord
```

after installing VSCode, Powershell 7+ and Githubdesktop. 

open powershell 7(not windows powershell),
type `code $profile`. hit enter. ![picture showing powershell window with "code $profile"](/assets/images/2023-01-25-10-55-35.png)

 wait for visual studio code to launch and paste in my predictive intellisense config. save this file and it should be good to go.

the powershell profile loads every time you open powershell so this is a nice place to store things you often need in powershell.

#### `code $profile` not launching visual studio code?

this can happen if your system image is lacking shell components. the following solution pasted into a powershell window run as admin should solve it

```powershell
Get-WindowsCapability -Name "*ShellComponents*" -Online | Add-WindowsCapability -Online
```

### History pitfalls

Powershell has a few logs

#### Get-History

holds the log for the current session. This history is deleted when the session ends. window is closed forexample.

https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_history

#### Predictive Intellisense

Tracks commands used in all PowerShell Sessions. This history in not deleted when the session ends. its stored in a central file per host. normally on windows systems it can be opened and altered like this

```powershell
code $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine
```

Predictive intellisense logs has a built in filter where it tries to avoid storing the following.

- password
- asplaintext
- token
- apikey
- secret

```powershell
# when running this in powershell
PS C:\Users> $password = "supersecretpassword"
PS C:\Users> $password2 = read-host
supersecretpassword
PS C:\Users> $password3 = read-host -AsSecureString
*******************
PS C:\Users> $password3 | ConvertFrom-SecureString -AsPlainText
supercecretpassword
PS C:\Users> $secret = "stufffoundhere"
PS C:\Users> $stuff = "secret"
PS C:\Users> $stuff2 = "somestuff"

# the following is stored in $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine
$password2 = read-host
$password3 = read-host -AsSecureString
$stuff2 = "somestuff"
# filter is doing its thing
```

If you store sensitive information. be sure that its not laying around in your command history.

more about psreadline history is found here.
https://learn.microsoft.com/en-us/powershell/module/psreadline/about/about_psreadline

## Tools

### Visual Studio Code

Visual Studio Code vs Notepad++. VSC is just better.

- Official editor for powershell. Powershell ISE deprecated
- Github integration

<https://code.visualstudio.com/>

try the keybindings. spesifically Ctrl+shift+P and alt+click
<https://code.visualstudio.com/docs/getstarted/keybindings>
<https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf>

### Github

- version control.
- sharing and collaboration.

<https://github.com/>

### Powershell 7

<https://github.com/PowerShell/PowerShell>

- crossplatform
- predictive intellisense
- multithreading

```powershell
# slow 1 by 1
1..10 | foreach-object { test-connection 195.88.55.$_ -count 1 -quiet}
# all 10 at the same time!
1..10 | foreach-object -ThrottleLimit 10 -Parallel { test-connection 195.88.55.$_ -count 1 -quiet}
```

- just better than powershell 5

## Common commands

|CMD|Powershell|Powershell Alias|
|-|-|-|
|ping|Test-NetConnection / Test-Connection |tnc|
|cd|Set-Location|sl, cd, chdir|
|dir|Get-ChildItem|gci,dir,ls|
|ipconfig|Get-NetIPConfiguration|gip|
|tracert|Test-NetConnection -traceroute|tnc -tr|
|shutdown|Stop-Computer / Restart-Computer||
|type|get-content|cat, gc, type|

All powershell functions are built using verb-noun

Verb = get, set, stop, remove, test, invoke...

Noun = cat, london, chair, cleanliness, assignment, computer...

if you are creating your own functions. look at the list approved verbs <https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/approved-verbs-for-windows-powershell-commands?view=powershell-7.2>

with the verb-noun information we can quickly get more commands

```powershell
Get-Command -Verb "get" -Noun "loc*"
Get-Command -Noun "location"
```

### Task 1

explore the commands in table above. ping some website

test out predictive intellisense with shift+tab. Try it with

- `Get-NetIPConfiguration -`
- `Test-Connection -`
- try out `Test-Connection -Quiet -TargetPath "<Your Favorite site>"`

## Variables and data types

variable is a way to store something for easy reuse later on.

you have built inn variables like $ErrorActionPreference

```powershell
# this should display all variables
Get-Variable
```

and user defined ones that you make yourself.

```powershell
$myGivenName = "David"
$mySurname = "Etternavn"
$myGivenName + " " + $MySurname
$var1 = "-1"
$var2 = 2
$sum = $var1 + $var2
$sum

$var2.GetType()


[int32]$var1 + $var2

# you also have more datatypes like these or more
$var1 = $true
$var2 = 10L
$var2 = [int64]"10"
```

Enviromental variables are also quite usefull
`$env:` and shift tab will displaythem.
$env:username and $env:userprofile when writing scripts makes them easier to use for multiple users.

### Task 2

explore the different enviromental variables.
use them with for example set-location and get-childitem

## Basic text manipulation

### single quotes and double quotes

single quotes is pure text. double quotes can expand variables inside it

```powershell
$language = 'Powershell'
$color = 'purple'
$sentence = "$language the powershell mascot has $color hair"
$sentence2 = '$language the powershell mascot has $color hair'

# you dont have to store it as a variable to run it. this also works fine
"$language the powershell mascot has $color hair"
```

### here string

represented with `@"  "@` block with either singlequote or double quote. Since anything inside it is displayed. the ending block has to be without tab or spaces and on a new line to work. otherwise it wont terminate the here string

```powershell
$date = Get-Date
$herestring = @"
This is a string
        Today's date is : $date
fancy ascii art or whatever
                     `. ___
                    __,' __`.                _..----....____
        __...--.'``;.   ,.   ;``--..__     .'    ,-._    _.-'
  _..-''-------'   `'   `'   `'     O ``-''._   (,;') _,'
,'________________                          \`-._`-','
 `._              ```````````------...___   '-.._'-:
    ```--.._      ,.                     ````--...__\-.
            `.--. `-`                       ____    |  |`
              `. `.                       ,'`````.  ;  ;`
                `._`.        __________   `.      \'__/`
                   `-:._____/______/___/____`.     \  `
                               |       `._    `.    \
                               `._________`-.   `.   `.___
                                             SSt  `------'`
"@
```

### Command execution

with command execution $() you can run big chunks of code inside your text if you wanted to or just singular commands.

```powershell
$date = Get-Date
$string = "today is $date"

# the above could also be written as
$string = "today is $(Get-Date)"
```

### TASK 3

store some ascii art as a here-string
try shoving in a variable with your name someplace and also running any powershell command you like using command execution.

more on this topic here

<https://docs.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-string-substitutions?view=powershell-7.2>

## Objects and properties

```powershell
$var = "stuff written here", "more stuff written here"

# check these out
$var | Get-Member
Get-Member -InputObject $var
```

### task 4

- run the `var =` command above, try a $var.count, $var.length
- test-netconnection and test-connection your favorite website, and save them as a variable.
- try get-member and access the different properties using $var.InsertPropertyName
- try `$variable | select-object address` on both test-connection and test-netconnection variables.

## Arrays,Arraylists and hashtables

### array

creation of an empty array

```powershell
$data = @()

# check the count to see if its empty or not.
$data.Count
```

creation of an array with values in one go

```powershell
$data = @('Zero', 'One', 'Two', 'Three')

# checking its contents.
$data.count
$data

# or multiline since its easier to read
$data = @(
    'Zero'
    'One'
    'Two'
    'Three'
)
```

comma separated lines can also create an array and work most of the time.

```powershell
$data = 'Zero', 'One', 'Two', 'Three'
```

to access individual items use brackets []. remember that the first item is [0]

```powershell
#  
$data[0]
$data[3]

# negative values go from the end. so last item is [-1]
$data[-1]

# multiple also possible
$data[3, 0, 3]

# or range with the .. operator
$data[1..3]

# or reverse
$data[3..1]
```

updating values and adding to an array

```powershell
# updating a value
$data[1] = "fifty"

# adding to an array
$data += "four"

```

more info on arrays <https://docs.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-arrays?view=powershell-7.2>

### Arraylist

arrays are slow to manipulate when large. adding to an array doesnt append the data, it breaks down the array and stores it again.

Arraylists handles adding items quickly. I personally almost always use arraylists.

```powershell
# creating an array and adding to it.
$myarray = [System.Collections.ArrayList]::new()
[void]$myArray.Add('Value')
$somevalue ="othervalue"
[void]$myArray.Add($somevalue)

# and removing from array
$everyExistingFolder.Remove('Value')

```

`[void]` is used to supress the return code when adding values. try it without.

### Pscustomobject

```powershell
$myObject = [PSCustomObject]@{
    Name     = 'Kevin'
    Language = 'PowerShell'
    State    = 'Texas'
}

# can be accessed like a normal object
$myObject.Name

# adding new properties to an object
$myObject | Add-Member -MemberType NoteProperty -Name 'ID' -Value 'KevinMarquette'

# and looking at it
$myObject.ID

# removing a property 
$myObject.PSObject.Properties.Remove("ID")

# when using objects in strings. funny thing can happen
"hello $myobject" 
# this above would print as hello @{ Name=Kevin; Language=PowerShell; State=Texas }
# $() is how you expand on something on an expression in a string 
"hello $($myobject.name)"
```

more info on pscustomobjects <https://docs.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-pscustomobject?view=powershell-7.2>


### Hashtable

Super fast but has its limits. If you encounter preformance issues. look into converting your arrays or arraylists if possible to hashtables. you can possibly make things that take hours run in minutes.

```powershell
$htable = @{ EmpName="Charlie"; City="New York"; EmpID="001" }
$htable2 = @{
    Key1 = "value1"
    Key2 = "value2"
    Key3 = "value3"
}

# adding 
$htable2.Add("Key4","value4")
# getting
$htable2 
#or 
$htable2.key4
```

more about hashtables here.
https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_hash_tables

### Task 5

- Create an array and an arraylist.
- Add a psCustomObject to each, add one text string and one number(make sure its stored as an int)
- Access individual items in the array and array list.
- Run .gettype() on the array/arraylist and the individual items.

## piping

Pipe `|` is usefull for oneliners but generaly slower than other methods, using it in the command line is fine. using it in scripts if you hit performance issues might be something to consider removing.

```powershell
'wuauserv' | Get-Service 
'wuauserv' | Get-Service | Stop-Service
'wuauserv' | Get-Service 
'wuauserv' | Get-Service | start-service
# you can also get the content of a text file and pipe it along

```

### task 6

- find 3 services, store them in a text file and try piping it to get-service.

```powershell
Get-Content -Path $env:USERPROFILE\Services.txt | Get-Service
```

## Math

```powershell
# powershell supports order of operation.
2*3+4/3
2*(3+4)/3
# you can do math with variables
$var = 1
$var = $var + 3
# ++ means +1 in most languages. 
$var ++
# -- means -1 in most languages
$var --
# += means add whatever comes after
$var += 4

# you can round three different ways
[Math]::Round(2.5)
[Math]::Round(3.5)
[Math]::Floor(2.5)
[Math]::Ceiling(2.5)

# round in the math class uses bankers rounding. rounding even numbers down and odd numbers up. so pay attention to that. 
# why is bankers rounding a thing?

2.5 + 3.5
# this should be 6

[Math]::Round(2.5) + [Math]::Round(3.5)
# rounding even and odd each way gives 6

[Math]::Floor(2.5) + [Math]::Floor(3.5)
# rounding down gives 5

[Math]::Ceiling(2.5) + [Math]::Ceiling(3.5)
# rounding up gives 7

# when you round with [int]2.5 it defaults to using [math]::round()
[int]2.5
[int]3.5
# if you want to preserve decimals you need to store it as a double
(3.5).GetType() # should tell you what this is
[double]3.5

# more on rounding in general here https://en.wikipedia.org/wiki/Rounding

# pi!
[math]::pi
```

more about math here

https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_arithmetic_operators

## operators

There are plenty but the logical and comparison operators are the most used ones.

### Comparison operators

- `1 -eq 1`
- `1 -ne 1`
- `(0..10) -contains 8`
- `"burgers are awesome" -like "\*are\*"`
- `"burger" -match "burger"`
- `1 -gt 2`
- `1 -lt 2`
- `3 -ge 3`

etc.

### logical operators

- `1 -eq 1 -and 1 -eq 2`
- `1 -eq 1 -or 1 -eq 2`
- `1 -eq 1 -xor 1 -eq 2`
- `-not(1 -eq 1)`

`-xor` is exclusive or.

![exclusive or table](/assets/images/2022-07-04-16-36-32.png)

More about operators here
<https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_operators?view=powershell-7.2>
or `get-help about_operators`

### task 7

Try running the operators listed above.

## controll flow

### if

```powershell
if (1 -eq 1) { "hurray, its true" }
# or more complex
if (Test-Connection -count 1 -quiet nrk.no) { "NRK is online" }
```

### if not

```powershell
if (-not ( 1 -eq 2)) { $true }
# or 
if (!( 1 -eq 2)) { $true }
```

### else and else if

```powershell
if (Test-Connection -Count 1 -Quiet nrk.no) { "NRK is online" }
else { "NRK is not responding" }
```

```powershell
if (Test-Connection -Count 1 -Quiet nrk.no) { "NRK is online" }
elseif (Test-Connection bbc.com -Count 1 -Quiet) { "bbc is online" }
elseif (1 -eq 3) { "can have many more" }
else { "NRK is not responding" }
```

### switch

switch is like an if statement. but much simpler when it comes to checking multiple conditions.

```powershell
switch (3) {
    1 { "It is one." }
    2 { "It is two." }
    3 { "It is three." }
    4 { "It is four." }
}

# or multiple, or text.
switch (4, 2, "five") {
    1 { "It is one." }
    2 { "It is two." }
    3 { "It is three." }
    4 { "It is four." }
    3 { "Three again." }
    "five" { "It is five." }
}
```

more about switches <https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_switch?view=powershell-7.2>

### Task 8

- Write something using controll flow that tells you if the current time is after lunch(11:00 local time)
- Write something using controll flow that tells you if you have:
  - good ping (less than 30)
  - bad ping (more than 100)
  - ok ping (somewhere inbetween good and bad)

Remember that switch is a thing.

### loops

#### For loop

Good for doing something X amount of times, every other time,
basic scripting you probably wont need for loop that much, but if you draw graphics with code its more usable.

```powershell
# <Init> intialises the counter
# <Condition> for continuing and doing your thing is this.
# <Repeat> adding to your conter, usually $i++ (adds 1) or even $i+=2 (adds two)
for (<Init>; <Condition>; <Repeat>){
    <Statement list>
}

# A simple for loop would look like this

for ($i = 0; $i -lt 100; $i++) {
    Write-Host "im doing something 100 times, this is #$i"
}

# or you can count the number of things in an array

$list = "Finn", "Jake", "Princess bubblegum", "Steve"
for ($i = 0; $i -lt $list.Count; $i++) {
    Write-Host "$($list[$i]) is a cool person"
}
```

#### Foreach

Probably the easiest to use.

```powershell
$list = "Finn", "Jake", "Princess bubblegum", "Steve"
foreach ($person in $list) {
    write-host "$person is a cool person"
}
```

#### foreach-object

this uses the pipeline which is seen as slower.

```powershell
$list = "Finn", "Jake", "Princess bubblegum", "Steve"
$list | ForEach-Object {"$_ is a cool person"}
```

There is one way this can be faster than all other loops in some circumstances. any one remember?

#### Foreach method

the fastest loop and my favorite.

```powershell
$list = "Finn", "Jake", "Princess bubblegum", "Steve"
$list.ForEach({ "$_ is a cool person" })
```

there might be some faster but then your probably a c# nerd using .net stuff.

#### While

```powershell
while($val -ne 3){
    $val++
    Write-Host $val
}
```

#### do while and do until

```powershell
# do while
do { <statement list> } while (<condition>)
# do until
do { <statement list> } until (<condition>)


$x = 1,2,78,0
do { $count++; $a++; } while ($x[$a] -ne 0)

$x = 1,2,78,0
do { $count++; $a++; } until ($x[$a] -eq 0)

as you can see -ne (not equal) and -eq (equal) is changed.

```

#### importing data

If its json, a webpage. excel file, its mostly the same way, for this session i will take clipboard, csv and a webpage.

```powershell
# this imports a CSV. you can find this in 
# https://github.com/PowershellGroup/CrashCourse/blob/main/assets/resources/ssb-surnames-norway.csv

# you can download it manually and import it using import-csv
$Surnames = import-csv /resources/ssb-surnames-norway.csv

# or you could download it directly and convert it from csv in powershell
$surnames = Invoke-Restmethod -uri "https://raw.githubusercontent.com/PowershellGroup/CrashCourse/main/assets/resources/ssb-surnames-norway.csv" | ConvertFrom-Csv -Delimiter ";"
# result would the same

# or you could copy the text into clipboard with ctrl+c
$surnames = Get-Clipboard | ConvertFrom-Csv -Delimiter ";"
# using here string  with copy paste also works
$surnames = @"
surname;amount
Aa;242
Aabel;254
Aaberg;364
"@ | ConvertFrom-Csv -Delimiter ";"

```

#### Sorting

```powershell
# sorting alphabetically by surname
$surname | Sort-Object Surname
# sorting amount
$surname | Sort-Object amount
# here it might not give you what you wanted if the amount is string value. declaring it as int works.
$surname | Sort-Object {[int]$_.amount}

```

#### where

usefull to quickly find a subset of something.

```powershell
# this should display all surnames that start with F and have a greater amount than 900
# with method
$selection = $surname.where({$_.surname -like "f*" -and $_.amount -gt 900})
# and with function
$selection = $surnames | Where-Object -FilterScript {$_.surname -like "f*" -and $_.amount -gt 900}
```

### TASK 9

In the where above. notice a problem with it?
make the filter work. the correct amount displayed should be 17

$store the selection as a variable and use .count on it to see if you are correct.

## Functions

Take a look at all the approved verbs here:
https://learn.microsoft.com/en-us/powershell/scripting/developer/cmdlet/approved-verbs-for-windows-powershell-commands?view=powershell-7.2

You're able to use automatic variables in functions like:
- [$PSBoundParameters](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_automatic_variables?view=powershell-7.3#psboundparameters)
- [$PSCmdlet](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_automatic_variables?view=powershell-7.3#pscmdlet)

You can have 2 different types, basic- and advanced-functions.

Basic functions would just have the bare minimum in order to create a function.

```powershell
function Verb-Noun {
    param (
        [string]$Param1,
        [int]$Param2
    )

    #CODE
}
```

Advanced functions are a little more complex, but have much more functionality. Therefore we most likely would always want to create an advanced function. Here's a basic version of an advanced function.

```powershell
function Verb-Noun {
    [CmdletBinding()]
    param (
        [string] $Param1,
        [int] $Param2
    )
    
    begin {
        # This code runs at the beginning, once. and is not mandatory
    }
    
    process {
        # this code, if you pipe data to the function runs each time.
    }
    
    end {
        # this code runs at the end. and is not mandatory.
    }
}
```

you can create a funcition that accepts values from the pipeline. forexample you pipe users into the function.

1. Begin part you might create some arrays, get some more data, do things you only need once.
2. process then does something per item piped into the function.
3. end runs at the end. maybe posts a completed list of something somewhere, creates an excel file and ships an email someplace.

The advantage of using an advanced function is that it has a lot of built-in functionality. You can e.g. create a function that has parameters that are exclusive from one another.
You can see more of this in the [Get-MrfkUserInfo](https://github.com/NorskNoobing/NN.MrfkCommands/blob/main/source/Public/Get-MrfkUserInfo.ps1) function.

```powershell
function Verb-Noun {
    [CmdletBinding(DefaultParameterSetName="username")]
    param (
        [Parameter(ParameterSetName="username")][string]$Username,
        [Parameter(ParameterSetName="displayname")][string]$DisplayName,
        [Parameter(ParameterSetName="mobilephone")][string]$MobilePhone
    )

    process {
        switch ($PsCmdlet.ParameterSetName) {
            "username" {
                $filter = "SamAccountName -like `"$Username`""
            }

            "displayname" {
                $filter = "DisplayName -like `"$DisplayName`""
            }

            "mobilephone" {
                $filter = "MobilePhone -like `"$MobilePhone`""
            }
        }

        Get-ADUser -Filter $filter
    }
}
```

You could merge a collection of functions into a module, which you can use to easily import functions across multiple users or devices, and update to new versions of the functions. This would atleast be better than having a bunch of custom functions in your `$PROFILE`. You can take [NN.MrfkCommands](https://github.com/NorskNoobing/NN.MrfkCommands) as an example (if one is needed).

## Creating a function

lets create a simple function first.

```powershell
function Test-NRKConnection {
    param (
        $count = 4
    )
    test-connection -targetname nrk.no -count $count
}
```

function above when run, pings NRK.NO for 4 counts. the count 4 is set as default in this instance.
if you run `Test-NRKConnection -count 5` it will override the 4 with 5.

a little more

```powershell
function Test-NRKConnection {
    param (
        $count = 1
    )
    $test = test-connection -targetname nrk.no -quiet -count $count
if($test){ Write-Output "NRK is Online"} else{Write-Output"NRK is Offline"}
}
```

### TASK 10

Create a function that pings some webpage and does something with that information.

## API

### what is an api?

APIs can handle Create, Read, Update and Delete operations.

A webhook is a lightweight API that powers one-way data sharing triggered by events, such as you pushing a button.

Almost all APIS use JSON to structure data.

```json
{
    "glossary": {
        "title": "example glossary",
        "GlossDiv": {
            "title": "S",
            "GlossList": {
                "GlossEntry": {
                    "ID": "SGML",
                    "SortAs": "SGML",
                    "GlossTerm": "Standard Generalized Markup Language",
                    "Acronym": "SGML",
                    "Abbrev": "ISO 8879:1986",
                    "GlossDef": {
                        "para": "A meta-markup language, used to create markup languages such as DocBook",
                        "GlossSeeAlso": ["GML", "XML"]
                    },
                    "GlossSee": "markup"
                }
            }
        }
    }
}
```

using an actual API. You have two easy to use ways to get webcontent using powershell. `Invoke-Webrequest` and `Invoke-RestMethod`.
```powershell
#flat easy json file to grasp

$ip = "1.1.1.1"
# take a look at content below. 
$content = Invoke-Webrequest -uri "https://ipinfo.io/$ip/json"
# run $content, and $content.content and 
$content.content | ConvertFrom-Json
# $content | convertfrom-json also works.
# or you could just do this 
Invoke-RestMethod -uri "https://ipinfo.io/$ip/json"

# Difference between Invoke-Webrequest and Invoke-RestMethod is that invoke restmethod has convertfrom-json built inn. I almost never use invoke-webrequest.

# this weather one has alot more depth with forecast every hour etc. so getting the weather right now requires some more 
$lat = "34.05"
$lon = "-118.24"
$weather = Invoke-RestMethod -uri "https://api.met.no/weatherapi/locationforecast/2.0/compact?&lat=$lat&lon=$lon"

$weather.properties.timeseries[0].data.instant.details
```

### TASK 11

1. Create a function that tests a given IP against ipinfo.io

Using it should be something like this.
`Get-IpInfo -ip "1.1.1.1"`

2. Create a function that gets the weather of lat/long position.

Using it should be something like this.
`Get-Weather -lat "34.05" -long -118.24"`

BONUS TASK

3. Create a function that uses IPinfo function to get LAT/LONG.
then uses that LAT/Long to get the weather using the Weather function.

Using it should be something like this.
`Get-WeatherIPInfo -ip "1.1.1.1"`

## Logging

Powershell scripts can be written without any logging, and thats fine for oneshots. but if something breaks it can be hard to figure out.

logging could be as simple as adding the following code a bunch of time.

```powershell
#setting log message
$message = "Log Message to record someplace"
#creating a log object, could have more fields like severity etc
$line = [pscustomobject]@{
    'DateTime' = (Get-Date)
    'Message'  = $Message
}
# exporting log object to csv file, -append means add to end. not overwrite.
$line | Export-Csv -Path $LogFilePath -Append -NoTypeInformation
```

Or you could write a function that you use in your scripts.

### Task 12

- Create a log function, either using code above or writting something different.
Requirements is that it stores it someplace still accessible after you close your PS window.
- Use the log function in one of your functions in task 11.
  - it should log if the page you are trying to query is offline
  - it should log if you get data from the page.

## Modules

You can run this function in order to get a list of all the functions in the module.

```powershell
Get-Command -Module "MODULENAME"
```

### ImportExcel module

https://github.com/dfinke/ImportExcel

```powershell
Import-Excel -Path "PATH"
```

```powershell
Export-Excel -Path "PATH"
```

### ActiveDirectory module

https://learn.microsoft.com/en-us/powershell/module/activedirectory

The AD-module is installed by default on `wintools04`.
Most commonly used are the Get commands.

To view all get commands you can run the following command.

```powershell
Get-Command -Module ActiveDirectory -Verb Get
```

You can run the following functions to get either users or groups from AD.

```powershell
Get-ADUser "USERNAME"
```

https://learn.microsoft.com/en-us/powershell/module/activedirectory/get-aduser

```powershell
Get-ADGroup "GROUPNAME"
```

```powershell
Get-ADComputer "COMPUTERNAME"
```

#### Active Directory Administrative Center

comes installed with Remote Server Administration Tools, which by default is installed on windows servers.

if you use this instead of `Active Directory Users and Computers` you can look at the powershell command history. any changes you apply with the GUI launches a powershell command.

![screenshot of active directory administrative center powershell](assets/images/2023-02-07-09-11-48.png)


### ConfigurationManager module

MECM module to find deployed computers, who has logged on, their ip etc.

You can import the module by running the following on `wintools04`.

```powershell
Import-Module ConfigurationManager
Set-Location ps1:
```

You'll have to set the psdrive to ps1 in order to make all the functions work.

If you want to get the computerinfo of a specific computer you can run the following.

```powershell
Get-CMDevice -Name "LT-SADM-001"
```

### TASK 13

Do this in a test enviroment.

- Make a change to a AD user using Active Directory Administrative Center.
- Find the command it used and use it to change it again to something else using PowerShell.
- Find an aduser using accountname  with powershell.
- Find an aduser using first half of firstname and last half of lastname.
- Create a csv file with a list of 3 users you want to create. create samaccountnames from those names. feed them into new-aduser and see if you manage to create users.

## Production enviroments

- Spend time testing code and think about what can go wrong.
- Get before set. Be sure you are manipulating the correct data.
- make it display code in text format before you do bulk changes.

```powershell
# try the following on your domain
$users = Get-Aduser -Filter *
foreach ( $user in $users){
    "Set-Aduser -user $user -homepage 'testpage.com'"
}
# should display as text only and you then visually see what your code does
```

- log files. setup logging if possible.
- hostname, are you on the correct box? running `hostname` might help
- correct credentials? running whoami might help.
- Visually different windows. My admin windows are red on my host.
![admin shell with red background](assets/images/2023-02-20-16-35-28.png)

## Self Learning

- Install [PSkoans](https://github.com/vexx32/PSKoans#prerequisites) by following the link. Go through installation of prereqs and the module. Solve the first Koan.
  - Interactive powershell learning. try doing 1 PSKoan at work everyday.
- Try to do stuff with powershell instead of GUI for daily tasks. Create a folder, txt file. get AD user information.