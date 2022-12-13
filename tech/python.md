
# Types

## String

### Concat string
Binary `+` operator is used for *string concatenation*.
```py
hi = "Hi"
there = "There"
result_str = hi + " " + there
print(result_str)
```
### Convert to string

```py
n = 17
s = str(num) 
print(s) // "17"
```

## Dictionary

## JSON
### Parse JSON to Dictionary
```py
import json

repo_json =  '{ "user":"Max-Starling", repo: "Notes" }'

# Parse repo_json
repo_dict = json.loads(repo_json)

# Access a dictionary field
print(repo_dict["user"])
```
### Convert to JSON
```py
import json

repo_dict = {
  "user":"Max-Starling"
  repo: "Notes"
}

# Convert to JSON
repo_json = json.dumps(x)

# Print JSON
print(repo_json)
```
### Intends in JSON
You can set number of indents in JSON
```py
json.dumps(x, indent=4)
```
### JSON Types mapping table

| Python Type  | JSON   |
| ------- | ------ |
| dict    | Object |
| list, typle	  | Array  |
| str	    | String |
| int, float |	Number |
| True	| true |
| False	| false |
| None	| null |
