# Filter Factory Demo

## [LIVE DEMO 🔗](https://concat-filter-demo.s1.elias.dev/) 

This demo was made for [vkb.de](https://vkb.de).

made by [elias](https://elias.dev) contact: info[at]elias.dev

---

## Requirements
The customer required arbitrary filter combination, including "grouping" and negating conditions on health data. 

The reason for this arbitrariness is the user needs to be able to specify exactly which
condition fall into his area of responsibility.
For example, show me all injuries on a specific
body part which is not an infection and exclude injuries specific to pregnant people ......

## Additional Recommendation

Because of the quickly growing complexity for the end user, of what are basically bitwise operations. 
I recommend to implement a natural language to filter function.

For example:
```
Show me all bone related injuries which are not 
infections and exclude injuries specific to pregnant people.
```
This could be translated to:
```
data.contains("bone") and not data.contains("infection") and not data.contains("pregnant")
```


## Approach

Since the data format is **unknown** to me, `string` as input type was chosen and one "*Leaf Condition*" specifically to `string` is implemented.
Its important to note that the current approach is extendable to support any arbitrary type of data or objects including SQL data input (with an arbitrary amount of columns and column types), which is likely to be the given data.

#### Current Filter Types
| Filter | Description | In Code | 
| ----- | ----- | ------ |
| Contains | Checks if a string contains a specific substring | `.contains({{parameter}})` |
| And | Concatenates an arbitray amount of "*leaf condition*" which all need to be true | `{{condition 1}} & {{condition 2}} & ... {{condition n}}` |
| Or | Concatenates an arbitray amount of "*leaf condition*" where at least one need to be true |  `{{condition 1}} \| {{condition 2}} \| ... {{condition n}}` |
| Not | Negates the child condition | `!{{condition}}`



The Filter structure is implemented as a tree, where each component implements a common interface. 


### Example:
![img1.png](mdres%2Fimg1.png)

Each component listens on its own states and propagates the state up to the parent.


## Example

Given this Data:
```
Husten, Hals, Nase, Oberkörper
Kaputter Fingernagel, Finger, Hand, Arm
Gebrochener Zeh, Fuß, Bein
Kopfschmerzen, Gehirn, Kopf
Bauchschmerzen, Magen, Bauch
Rückenschmerzen, Wirbelsäule, Rücken
```

We want to apply filter conditions which can be represented in pseudo code as: 
```
data.contains("Finger" or "Bein" or "Hals") and data.contains("Arm" or "Nase" or "Ohr")
```

This can be represented in UI as:

![img2.png](mdres%2Fimg2.png)

This will result in this output: 
```
Husten, Hals, Nase, Oberkörper
Kaputter Fingernagel, Finger, Hand, Arm
```


