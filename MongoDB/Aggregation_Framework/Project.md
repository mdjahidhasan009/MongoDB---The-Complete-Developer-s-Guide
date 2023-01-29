# Project

# Working with Project Stage

The project works in the same way as the projection works in the `find()` method

```cpp
"gender" : "male",
	"name" : {
		"title" : "mr",
		"first" : "harvey",
		"last" : "chambers"
	},
```

Full list to all want to convert the name into one document project does not group multiple documents together, it's just transforms every single document

```cpp
> db.persons.aggregate([
    { 
				$project: { 
						_id: 0, 
						gender: 1, 
						fullName: { 
								$concat: ['$name.first', ' ','$name.last'] 
						} 
				} 
		}
]).pretty()
**Output**
{ "gender" : "male", "fullName" : "victor pedersen" }
{ "gender" : "male", "fullName" : "gideon van drongelen" }
{ "gender" : "male", "fullName" : "harvey chambers" }
{ "gender" : "female", "fullName" : "پریا پارسا" }
{ "gender" : "female", "fullName" : "maeva wilson" }
{ "gender" : "male", "fullName" : "elijah lewis" }
{ "gender" : "female", "fullName" : "olav oehme" }
{ "gender" : "female", "fullName" : "madeleine till" }
{ "gender" : "male", "fullName" : "carl jacobs" }
{ "gender" : "male", "fullName" : "isolino viana" }
{ "gender" : "female", "fullName" : "louise graham" }
{ "gender" : "female", "fullName" : "mestan kaplangı" }
{ "gender" : "female", "fullName" : "katie welch" }
{ "gender" : "female", "fullName" : "sandra lorenzo" }
{ "gender" : "male", "fullName" : "بنیامین سالاری" }
{ "gender" : "female", "fullName" : "andreia arnaud" }
{ "gender" : "female", "fullName" : "shona kemperman" }
{ "gender" : "male", "fullName" : "zachary lo" }
{ "gender" : "female", "fullName" : "anne ruiz" }
{ "gender" : "female", "fullName" : "gonca alnıaçık" }
Type "it" for more
```

Now want to first and last name start with the uppercase letter

```cpp
db.persons.aggregate([
{
	$project: {
		_id: 0,
		gender: 1,
		fullName: {
			$concat: [{ $toUpper: '$name.first'}, ' ', { $toUpper: '$name.last'}]
		}
	}
}
]).pretty()
**Output**
{ "gender" : "male", "fullName" : "VICTOR PEDERSEN" }
{ "gender" : "male", "fullName" : "GIDEON VAN DRONGELEN" }
{ "gender" : "male", "fullName" : "HARVEY CHAMBERS" }
{ "gender" : "female", "fullName" : "پریا پارسا" }
{ "gender" : "female", "fullName" : "MAEVA WILSON" }
{ "gender" : "male", "fullName" : "ELIJAH LEWIS" }
{ "gender" : "female", "fullName" : "OLAV OEHME" }
{ "gender" : "female", "fullName" : "MADELEINE TILL" }
{ "gender" : "male", "fullName" : "CARL JACOBS" }
{ "gender" : "male", "fullName" : "ISOLINO VIANA" }
{ "gender" : "female", "fullName" : "LOUISE GRAHAM" }
{ "gender" : "female", "fullName" : "MESTAN KAPLANGı" }
{ "gender" : "female", "fullName" : "KATIE WELCH" }
{ "gender" : "female", "fullName" : "SANDRA LORENZO" }
{ "gender" : "male", "fullName" : "بنیامین سالاری" }
{ "gender" : "female", "fullName" : "ANDREIA ARNAUD" }
{ "gender" : "female", "fullName" : "SHONA KEMPERMAN" }
{ "gender" : "male", "fullName" : "ZACHARY LO" }
{ "gender" : "female", "fullName" : "ANNE RUIZ" }
{ "gender" : "female", "fullName" : "GONCA ALNıAçıK" }
Type "it" for more
```

`$substrCP` ⇒ Substring part
0 ⇒ means starting index
1 ⇒ means how much character(length)

```cpp
> db.persons.aggregate([
    {
        $project: {
            _id: 0,
            gender: 1,
            fullName: {
                $concat: [
                    { $toUpper: { $substrCP: ['$name.first', 0, 1] } },
                    ' ',
                    { $toUpper: { $substrCP: ['$name.last', 0, 1] } }
                ]
            }
        }
    }
]).pretty()
**Output**
{ "gender" : "male", "fullName" : "V P" }
{ "gender" : "male", "fullName" : "G V" }
{ "gender" : "male", "fullName" : "H C" }
{ "gender" : "female", "fullName" : "پ پ" }
{ "gender" : "female", "fullName" : "M W" }
{ "gender" : "male", "fullName" : "E L" }
{ "gender" : "female", "fullName" : "O O" }
{ "gender" : "female", "fullName" : "M T" }
{ "gender" : "male", "fullName" : "C J" }
{ "gender" : "male", "fullName" : "I V" }
{ "gender" : "female", "fullName" : "L G" }
{ "gender" : "female", "fullName" : "M K" }
{ "gender" : "female", "fullName" : "K W" }
{ "gender" : "female", "fullName" : "S L" }
{ "gender" : "male", "fullName" : "ب س" }
{ "gender" : "female", "fullName" : "A A" }
{ "gender" : "female", "fullName" : "S K" }
{ "gender" : "male", "fullName" : "Z L" }
{ "gender" : "female", "fullName" : "A R" }
{ "gender" : "female", "fullName" : "G A" }
Type "it" for more
```

The final output

```cpp
> db.persons.aggregate([
    {
        $project: {
            _id: 0,
            gender: 1,
            fullName: {
                $concat: [
                    { $toUpper: { $substrCP: ['$name.first', 0, 1] } },
                    { 
												$substrCP: [
														'$name.first', 
														1, 
														{ $subtract: [{ $strLenCP: '$name.first' }, 1] }
												] 
										},
                    ' ',
                    { $toUpper: { $substrCP: ['$name.last', 0, 1] } },
                    { 
												$substrCP: [
														'$name.last', 
														1, 
														{ $subtract: [{ $strLenCP: '$name.last' }, 1] }
												] 
										},
                ]
            }
        }
    }
]).pretty()
**Output**
{ "gender" : "male", "fullName" : "Victor Pedersen" }
{ "gender" : "male", "fullName" : "Gideon Van drongelen" }
{ "gender" : "male", "fullName" : "Harvey Chambers" }
{ "gender" : "female", "fullName" : "پریا پارسا" }
{ "gender" : "female", "fullName" : "Maeva Wilson" }
{ "gender" : "male", "fullName" : "Elijah Lewis" }
{ "gender" : "female", "fullName" : "Olav Oehme" }
{ "gender" : "female", "fullName" : "Madeleine Till" }
{ "gender" : "male", "fullName" : "Carl Jacobs" }
{ "gender" : "male", "fullName" : "Isolino Viana" }
{ "gender" : "female", "fullName" : "Louise Graham" }
{ "gender" : "female", "fullName" : "Mestan Kaplangı" }
{ "gender" : "female", "fullName" : "Katie Welch" }
{ "gender" : "female", "fullName" : "Sandra Lorenzo" }
{ "gender" : "male", "fullName" : "بنیامین سالاری" }
{ "gender" : "female", "fullName" : "Andreia Arnaud" }
{ "gender" : "female", "fullName" : "Shona Kemperman" }
{ "gender" : "male", "fullName" : "Zachary Lo" }
{ "gender" : "female", "fullName" : "Anne Ruiz" }
{ "gender" : "female", "fullName" : "Gonca Alnıaçık" }
Type "it" for more
```

[Turning the Location Into a geoJSON Object ](Project/Turning_the_Location_Into_a_geoJSON_Object.md)

[Understanding the ISO Week Year Operator](Project/Understanding_the_ISO_Week_Year_Operator.md)