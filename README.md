# Fish tangler for literate programming

This is a **function** for fish shell that takes ***markdown*** text like this and converts it into a runnable fish script (ands runs it) from the terminal. This follows conventions of literate programming. A programmign paradigm in which the code is embedded within the documentation with the reasoning for it. If you want to know more about literate programming [check this out](https://en.wikipedia.org/wiki/Literate_programming)

Run your markdown fish code with 

```
fishlit ~/yourcode.fish.md
```
The next iteration will parse any code in any language. 

```fish
function delitcode
  set keep false
  set code (cat $argv)
  for line in $code
    if test $keep = false
      if test $line = '```fish'
        echo "# Stars code"
        set keep true
        continue
      else
        echo "# "$line
        continue
      end
    end

    if test $keep = true
      if test $line = '```'
        echo "# Finishes code"
        set keep false
      else
        echo $line
      end
    end
  end
end

function fishlit
  for line in (delitcode $argv)
    fish -c $line
  end
end
```