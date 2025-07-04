# nelt (NELua Templates)

Nelt is a super simple templating language with [jinja](https://github.com/pallets/jinja) inspired syntax that compiles to nelua code for you to run

## Quick Start

Create a template file that returns a function
This function must take `tp: *nelt.Template` as it's first argument, else you won't be able to get anything from the file

```lua
-- file.nelt
{# a comment #}
{% local nelt = require "path.to.nelt" %}
{% local function test(tp: *nelt.Template, str: string) %}
  {{ "<escaped val>" }}
  text
  {{ str }}
  {{- "<unescaped val>" }}
{% end %}
{% return test %}
```

Compile files before trying to require them in main program

```lua
-- compile.nelua
local nelt = require "path.to.nelt"

local ok, err = nelt.compile_file("path/to/file.nelt")) -- will create a file called `file-nelt.nelua` in the same directory as the file
assert(ok, err)
```

Finally require and run the template

```lua
-- main.nelua
local nelt = require "path.to.nelt"

local file = require "file-nelt"

local tp: nelt.Template
file(&tp, "hello")
print(tp:tostring())
-- &lt;escaped val&gt;textnhello<unescaped val>
```

For more complex examples, check the [examples](./examples) folder

## Reference

The following syntax is supported:

- `{% ... %}`: This is for nelua expressions and will be placed as is into the generated code
- `{{ ... }}`: This is for html escaped values and will be wrapped with `tp:escape()`
- `{{- ... }}`: This is for html unescaped values and will be wrapped with `tp:write()`
- `{# ... #}`: This is for comments, anything in these will not be added to the generated code

### nelt module

```lua
local nelt = @record{}
```

### nelt.Template

Contains the methods used to generate the template
- `buf`: Buffer where generated code is appended to
- `data`: Arbitray data that can be set by the user and used within a template

```lua
local nelt.Template = @record{
  buf: stringbuilder,
  data: pointer
}
```

### nelt.Template:destroy

Clears and destroys the internal buffer and sets data to a nilptr

```lua
function nelt.Template:destroy()
```

### nelt.Template:write

Appends `v` to the template buffer as is

```lua
function nelt.Template:write(v: auto)
```

### nelt.Template:escape

Html Escapes `v` then appends it to the template buffer

```lua
function nelt.Template:escape(v: auto)
```

### nelt.Template:tostring

Returns the content of the template buffer and clears it

```lua
function nelt.Template:tostring(): string
```

### nelt.Template:view

Returns a view to the template buffer

```lua
function nelt.Template:view(): string
```

### nelt.compile

Returns generated code from `text`

```lua
function nelt.compile(text: string): string
```

### nelt.compile_file

Reads the file at `file_path` and generates code at the same location
Returns a none empty string on error

```lua
function nelt.compile_file(file_path: string): string
```

---

### Note

The file [nelt-compile.nelua](./nelt-compile.nelua) is an example on how to compile all templates in a directory

Simply replace the string in the `get_files` macro call to use where you'd like

## Acknowledgement

The idea for this project originally stemmed from [this](https://www.omarpolo.com/post/template.html) blog post
