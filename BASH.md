## BASH

New BASH stuff - 

	file:test.sh
	#! /bin/sh
	echo '$#' $#
	echo '$@' $@
	echo '$?' $?
	
	*If you run the above script as*
	
	> ./test.sh 1 2 3
	
	You get output:
	$#  3
	$@  1 2 3
	$?  0
	
	*You passed 3 parameters to your script.*
	
	$# = number of arguments. Answer is 3
	$@ = what parameters were passed. Answer is 1 2 3
	$? = was last command successful. Answer is 0 which means 'yes'
	
Make the terminal dumb - ie: no cursor/no input - client is dumb.
	export TERM=${TERM:-dumb}