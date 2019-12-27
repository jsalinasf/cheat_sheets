# How to Enable syntax highlighting for yaml files in gnu nano

## All the credits goes to [ourcodeworld] (https://ourcodeworld.com/articles/read/796/how-to-enable-syntax-highlighting-for-yaml-yml-files-in-gnu-nano)

1. List available Nano Syntax Highlight Files
As first step, discover which languages are available in nano to highlight its syntax with the following command:
	ls /usr/share/nano/

1. Create YAML Nano Syntax Highlighting File
In order to provide syntax highlighting to your file, if the default file doesn't exist, you need to create the syntax highlighting file for this language. This file is the yaml.nanorc file and you need to create it in the mentioned directory. Run nano to create the file:
	sudo nano /usr/share/nano/yaml.nanorc

1. Paste the following code

	# Supports `YAML` files
	syntax "YAML" "\.ya?ml$"
	header "^(---|===)" "%YAML"

	## Keys
	color magenta "^\s*[\$A-Za-z0-9_-]+\:"
	color brightmagenta "^\s*@[\$A-Za-z0-9_-]+\:"

	## Values
	color white ":\s.+$"
	## Booleans
	icolor brightcyan " (y|yes|n|no|true|false|on|off)$"
	## Numbers
	color brightred " [[:digit:]]+(\.[[:digit:]]+)?"
	## Arrays
	color red "\[" "\]" ":\s+[|>]" "^\s*- "
	## Reserved
	color green "(^| )!!(binary|bool|float|int|map|null|omap|seq|set|str) "

	## Comments
	color brightwhite "#.*$"

	## Errors
	color ,red ":\w.+$"
	color ,red ":'.+$"
	color ,red ":".+$"
	color ,red "\s+$"

	## Non closed quote
	color ,red "['\"][^['\"]]*$"

	## Closed quotes
	color yellow "['\"].*['\"]"

	## Equal sign
	color brightgreen ":( |$)"

1. Create Test Yaml File to see results
As final step, you need to test wheter the highlight works or not. Proceed to create a test file with nano and write some YAML on it, for example: