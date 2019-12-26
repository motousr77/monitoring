## Linux Cli Commands and Bash scripts
#### Several useful bash commands
~~~sh
# create many files by for-loop and touch
for i in {1..99}; do echo -e "file number $i\n  next line" > "file_$i.txt"; done

# how to rename extensions in many files
for i in *.txt; do mv "$i" "${i%.txt}.yml"; done
~~~
