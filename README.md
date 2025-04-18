# nelt (NELua Templates)

Nelt is a super simple templating language that compiles to nelua code

## Quick Start

Create a template file that returns a function
This function must take `tp: *nelt.Template` as it's first argument, else you won't be able to get anything from the file

```text
-- file.nelt
{[ a comment ]}
{# local nelt = require "path.to.nelt" #}
{# local function test(tp: *nelt.Template, str: string) #}
  {{ "<escaped val>" }}
  text
  {{ str }}
  {{- "<unescaped val>" }}
{# end #}
{# return test #}
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
-- &lt;escaped val&gt;texthello<unescaped val>
```

## Reference

The following syntax is supported:

- `{# ... #}`: This is for nelua expressions and will be replaced as is
- `{{- ... }}`: This is for unescaped values and will be wrapped with `tp:write()`
- `{{ ... }}`: This is for escaped values and will be wrapped with `tp:escape()`
- `{[ ... ]}`: This is for comments, anything in these will not be added to the final output


### Function

#### nelt.compile_file(file_path: string): (boolean, string)

Takes a file and compiles into into the nelua code you will require for you template

### Note

The file [nelt-compile.nelua](./nelt-compile.nelua) is an example on how to compile all templates in a directory

Simply replace the string in the `get_files` macro call to use where you'd like
