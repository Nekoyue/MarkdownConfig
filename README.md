# Markdown Config (Archived)
[![Kotlin](https://img.shields.io/badge/Kotlin-1.3.70--eap--274-blue)](https://kotlinlang.org)
[![License](https://img.shields.io/github/license/Nekoyue/MarkdownConfig.svg)](https://github.com/Nekoyue/MarkdownConfig/blob/master/LICENSE)

I realized MarkdownConf will just reimplement [YAML](https://en.wikipedia.org/wiki/YAML). Repository is archived now for reference.

## Overview 
Markdown Config is a [Markdown](https://guides.github.com/features/mastering-markdown/) language parser for configuration files base on [Kotlin](https://kotlinlang.org)!

The code style will focus on readability and usability.

Markdown Config is using [Kotlin Multiplatform](https://kotlinlang.org/docs/reference/multiplatform.html) and currently supports JVM and JavaScript.

## Scheme & Syntax
The type of individual values will always be `String` on JVM platform, or `dynamic` on JavaScript.

In this document, `[value1, value2, ...]` represents a list and `{key1=value1, key2=value2, ...=...}` represents a map.

### Keys (properties)
The following markup will be parsed as either `Map<String, String>` on JVM platform, or `Map<String, dynamic` on JavaScript.
```
key: value
``` 

For boolean values:
 - `- [x] key` is equivalent to `key: true`. `true` is a `String`.
 - `- [ ] key` is equivalent to `key: false`. `false` is a `String`.

Special cases:
- `key with space: value with space` -> `{"key with space"="value with space"}`
- `multiple-colons: :::` -> `{"multiple-colons"=":::"}` : The text before the first colon will be the key.
- `no-space-after-colon:value` -> `{"no-space-after-colon"="value"}` : Markdown Config will still parse this correctly, however it's not encouraged.
- `key: "string"` -> `{"key"="\"string\""}` : The quotation mark will become part of the value. Don't include it if unnecessary.

### Comments
Any line started with `>` or any italic text (surround by `*` or `_`) will be the comment.

```> This is a comment```

```key: value *This is another comment*```

### Lists
The following markup will be parsed as a list named `Tasks` with value `["Build", "Debug", "Release"]`

Start a line with any of `-` `*` or numbers have the same effect.

The size of heading (number of `#` before the key) depends on its parents heading. It will be described later.
```
#### Tasks
- Build
- Run
- Release
```

#### Maps
Similarly, you can create a map named `Commands` with value `{"Build"="gradle build", "Run"="gradle run", "Release"="git push"}` using following markup.

```
#### Commands
- Build: gradle build
- Run: gradle run
- Release: git push
```


#### Multidimensional list
This example is complicated, an explanation is below.
```
#### Commands
- Build: gradle build
- Run: gradle run
    - Debug: true
- Release: *This colon is optional*
    - GitHub: git push
    - AppStore: xcrun altool
        - Beta: true
        - Tester: *This colon is optional*
            - A
            - B
            - C
```

The map `Commands` with multi types is parsed as:

```
{
    "Build"="gradle build", 
    "Run"={
        "Run"="gradle run", 
        "Debug"="true"
    }, 
    "Release"={
        "GitHub"="git push", 
        "AppStore"={
            "AppStore"="xcrun altool", 
            "Beta"="true", 
            "Tester"=["A", "B", "C"]
    }
}
```

### Table
This table will be parsed as a list of map named `Deatail`. The value will be `[{"Task"="commonMain", "Require Test"="True", "Time Limit"="1 second"}, {"Task"="jvmMain", "Require Test"="True", "Time Limit"="2 seconds"}, {"Task"="jsMain", "Require Test"="False"}]`

Note that a key with missing value will not be included in the resultant map.
```
#### Detail
| Task       | Require Test | Time Limit |
| ---------- | ------------ | ---------- |
| commonMain | True         | 1 second   |
| jvmMain    | True         | 2 seconds  |
| jsMain     | False        |            |
```

### Structure
Structure is used to categorize keys. 
```
# H1
## H2
### H3
#### H4
##### H5
###### H6
```


### Other markdown features haven't utilized
- Normal text without colon
- Numbered list `1. text`
- Images `![text](url)`
- Link `[text](url)`
- Inline code ` `` ` and Code block ``` `````` ```
- Bold `**text**` or `__text__`
- Strikethrough `~~Text~~`
- Horizontal rule `---` `***` or `___`

## Plan
- [ ] Implement the scheme 
- [ ] Multiplatform support
- [ ] Use [data class](https://kotlinlang.org/docs/reference/data-classes.html) as custom types.