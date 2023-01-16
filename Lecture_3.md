# Working with Processes in Linux

### top command
In order to see the process information in Linux, `top` command can be used  
* To see per CPU processes: press `1` while `top` is running
* To see the coloring: press `z` while `top` is running
* To highlight running processes: presss `y` while `top` is running (press `b` to highlight or bold)
* To sort by CPU time: press `P` while top is running
* To sort by Memory: press `M` while top is running

`top` has some flags that can help in getting some information:

* To see a specific process info: `top -p 1984` (where `1984` is the process ID)
* To see processes from a specific user: `top -U raziiqbal`
	
### ps command
Another utility is `ps` used for processes in linux
* To see current running processes for your user: `ps`
* To see all the running processes: `ps -e`
* For more info on all the processes: `ps -ef`
* To see processes from a specific user: `ps -U raziiqbal`
* To search a process by name: `ps -fC bash`
* To see a specific process info: `ps -p 2885` (where `2885` is the process ID)

### pidof command
Another command to find the process id by name is pidof: `pidof bash`

### kill command
To end a process by id: `kill 4200`

### Combining commands
Commands can be combined: ``` kill `pidof firefox` ```
