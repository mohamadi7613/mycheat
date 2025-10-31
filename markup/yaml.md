# YAML

+ Use spaces only

### Links
https://yamlvalidator.com


### 1. key-value
+ a space between key and value
+ Case-sensitive

```yml
string: "Hello"     # or Hello
float: 3.14
null_value: null    # or ~
```

### 2. array or list

```yml
shopping_list:           # list is ordered collection
  - eggs                  # array of string
  - milk
  - bread
colors: [red, green, blue]        # in one line
```

### Dictionary

```yml
person:                   # use an euqal space for keys (no need for tab)
  name: Alice              # Dictionary is unordered 
  age: 30
```

### List of Dictionary

```yml
users:
  - name: John
    role: admin
  - name: Jane
    role: user
```


### Dictionary of lists

```yml
departments:
  engineering:
    - Alice
    - Bob
  marketing:
    - Carol
    - Dave
```



### Multiline strings

```yml
description: |
  This is a multi-line
  block of text.
  It preserves newlines.

short_note: >
  This is a long string
  folded into a single line.
```

### links(anchors and aliases)
```yml
defaults: &default_settings
  retries: 3
  timeout: 30

prod:
  <<: *default_settings
  timeout: 60
```


