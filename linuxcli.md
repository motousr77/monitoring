## Linux Cli Commands and Bash scripts
#### Several useful bash commands
~~~sh
# ^ create many files by for-loop and touch
for i in {1..99}; do echo -e "file number $i\n  next line" > "file_$i.txt"; done

# ^ how to rename extensions in many files
for i in *.txt; do mv "$i" "${i%.txt}.yml"; done

# ^ how to run many instance in docker
for i in  {1..8}; do docker run -d --name="tomcat_$i" tomcat; done

# ^ how to stop instances below
for i in  {1..8}; do docker stop "tomcat_$i"; done

# ^ and remove it
for i in  {1..8}; do docker rm "tomcat_$i"; done

# ^ docker actions for all containers
docker rm | stop | pause | unpause | start | restart $(docker ps -aq)

# ^ how to LOAD CPU
dd if=/dev/zero of=/dev/null

# ^ or randomize and bzip ...
dd if=/dev/urandom | bzip2 -9 > /dev/null

# ^ or say YEC cpu many-many times
yes > /dev/null
~~~
