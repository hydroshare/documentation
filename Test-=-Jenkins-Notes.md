# some useful scripts for nodes:
`println "git config --list".execute().text`

`println "git --version".execute().text`

`println System.getenv()`

`println "wget -qO- {url}".execute().text ` 



***
# Sandbox and Master have multiple versions of python
Default python call and pip call will be version 2.6.6

* python2.7  pip2.7
* python3.4 pip3.4

If you want an item from the a main python environment put 
`pip install {name} -I `
in the shell script of your virtual environment

***
# master
If you run git on master, use the MASTER

master slave (node-master>configure>advanced) also has environment variables for http.proxy and https.proxy

