## Linux Cli Commands and Bash scripts
#### Several useful bash commands
~~~sh
# create several files by for-loop and touch
for i in {1..99}; do echo -e "file number $i\n  next line" > "file_$i.txt"; done

# how rename extensions of several files
for i in *.txt; do mv "$i" "${i%.txt}.yml"; done
~~~
