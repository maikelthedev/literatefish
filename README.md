# Fish function for literate programming

This is a **function** for fish shell that takes ***markdown*** text like this and converts it into a runnable fish script (ands runs it) from the terminal. This follows conventions of literate programming. A programming paradigm in which the code is embedded within the documentation with the reasoning for it. If you want to know more about literate programming [check this out](https://en.wikipedia.org/wiki/Literate_programming)

Run your markdown fish code with:

```
litrun [FILE] INTERPRETER
```
For example:
```
litrun ~/yourcode.md fish
```
The second argument not only dictates the interpreter to run it with but also the code in the markdown to run. This means you can have multiple codebases in the same markdown file. Example:

```
#Some title
This is code in Python
` ` `python
whatever
` ` `
Then you could have some JavaScript code like
` ` `node
console.log('hello from node')
` ` `
```
This is the code, if you save it in your {CONFIG}/fish/function/ as litrun.fish you'd be able to run it from anywhere on your system

```fish
function getcommentchar
  set lang (echo $argv)
  switch $lang
  case python bash fish
    echo "#"
  case node
    echo "//"
  end
end

function delitcode
  set keep false
  set runner $argv[2]
  set code (cat $argv[1])
  set commentchar (getcommentchar $runner)
  for line in $code
    if test $keep = false
      if test $line = "```$runner"
        echo $commentchar"Stars code"
        set keep true
        continue
      else
        echo $commentchar$line
        continue
      end
    end

    if test $keep = true
      if test $line = '```'
        echo $commentchar"Finishes code"
        set keep false
      else
        echo $line
      end
    end
  end
end

function litrun
  set processed (delitcode $argv)
  set runner $argv[2]
  for line in $processed; echo $line; end | $runner
end
```

Now: this whole file can be parsed using the same function it describes. If you already have the function installed you could update it to a newer version with:

```bash
set conf (if test $XDG_CONFIG_HOME; echo $XDG_CONFIG_HOME;else;echo "~/.config";end)
delitcode README.md fish >> $conf/fish/functions/litrun.fish
```