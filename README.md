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

## Reference

The following syntax is supported:

- `{% ... %}`: This is for nelua expressions and will be replaced as is
- `{{ ... }}`: This is for html escaped values and will be wrapped with `tp:escape()`
- `{{- ... }}`: This is for html unescaped values and will be wrapped with `tp:write()`
- `{# ... #}`: This is for comments, anything in these will not be added to the final output


### Functions

#### nelt.compile_file(file_path: string): (boolean, string)

Takes a file and compiles into into the nelua code you will require for your template

#### nelt.Template:write(s: string): void

Writes the string `s` to the internal buffer of the template without html escaping it

#### nelt.Template:escape(s: string): void

Writes the string `s` to the internal buffer of the template while html escaping it

#### nelt.Template:tostring(): string

Returns a view of the current buffer but doesn't clear it

#### nelt.Template:destory(): void

Clears the buffer and resets the structure to a zeroed state

### Types

#### nelt.Template

```lua
local nelt.Template = @record{
  buf: stringbuilder
}
```

### Note

The file [nelt-compile.nelua](./nelt-compile.nelua) is an example on how to compile all templates in a directory

Simply replace the string in the `get_files` macro call to use where you'd like

## Acknowledgement

The idea for this project originally stemmed from [this](https://www.omarpolo.com/post/template.html) blog post
