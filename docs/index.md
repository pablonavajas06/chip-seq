# ChIP-seq practical session

Running all analyses is computationally intensive and despite the power of the current laptops, jobs should be run on high-performance clusters (HPC).

## log in `gaia`

[`gaia`](https://hpc.uni.lu/systems/gaia/) is one of the [HPC of the UNI](https://hpc.uni.lu).

### connect to the frontend

To connect to it, you need an account and an authorized ssh key. Actually, a pair of keys, one public and one private.
The public key is sent over when connecting to the remote and compared to the authorized private key.
A match allows the sender to log in. No password required.

After the setting up of your account, the following should work if you are using mac or GNU/Linux:

```
ssh gaia-cluster
```

Otherwise, on Windows, right-click on `pageant` in the system tray and load a saved session `gaia`. In the terminal, log as your username, such as `student01`.

You should see the following prompt of the gaia frontend:

```
===============================================================================
 /!\ NEVER COMPILE OR RUN YOUR PROGRAMS FROM THIS FRONTEND !
     First reserve your nodes (using oarsub(1))
Linux access.gaia-cluster.uni.lux 3.2.0-4-amd64 unknown
 16:45:49 up 126 days,  1:28, 34 users,  load average: 0.96, 1.58, 1.73
0 16:45:49 your_login@access(gaia-cluster) ~ $
```


Note that you are on the `access` frontend.

The frontend is meant for browsing / transferring your files only and you **MUST** connect to a node for any computational work 
using the utility `oarsub` described [here](https://hpc.uni.lu/users/docs/oar.html). This program managed the queuing system and dispatch jobs among the resources according to the demands.

Software are organized into **modules** that provide you with the binaries but also all the environment required for their running processes.


### TMUX

log in to a remote computer is great, all computation, heat generation is happening elsewhere but this comes with a price: disconnection.  
This happens all the time. The way to get around it, is to have a `screen` system that store your terminal, commands, environment in which 
you can easily detach and re-attach.

Two systems exist, `screen` and `tmux`. Both work well, but `tmux` has a nicer interface IMHO.

a short tutorial is [accessible here.](https://www.sitepoint.com/tmux-a-simple-start/)

Briefly, on the **access** frontend, start a `tmux` instance with

```
tmux
```

to detach (press CTRL and B together, release then use the next key):

```
CTRL + B, then D
```

to re-attach:

```
tmux attach
```

or the alias

```
tmux at
```


#### some useful commands

once in an instance,

- **create** a new tab

```
CTRL + B, then C
```

- move to the **next** tab

```
CTRL + B, then N
```

- move to the **previous** tab

```
CTRL + B, then P
```

- **rename** to the current tab

```
CTRL + B, then ,
```

then type the new name

#### Quit

```
exit
```

in all tabs kills the `tmux` session


Of note, `tmux` instances live until the frontend is rebooted.

### connect to a node

Connecting to a computing node is anyway required to use modules.

You need to book ressources by specifying how many cores, optionaly if they are a same node, and a walltime clock. A job can never get extended.

For Thursday:

```
oarsub -I -t inner=3950979 -l nodes=1,walltime=9
oarsub -I -t inner=3950994 -l nodes=1,walltime=9
oarsub -I -t inner=3950995 -l nodes=1,walltime=9
```

For Friday:

```
oarsub -I -t inner=3950983 -l nodes=1,walltime=9
oarsub -I -t inner=3950992 -l nodes=1,walltime=9
oarsub -I -t inner=3950993 -l nodes=1,walltime=9
```

Without entering into the details of [submitting a job](https://hpc.uni.lu/users/docs/oar.html#request-hierarchical-resources-with-oarsub),
here is the explanation for the above command:

- `-I` is for interactive, default is passive
- `-t inner=xxx`, connect to a `container`, specific for today because we booked resources for the course
- `-l` define the resources you need. The less you ask for, the more high up you are in the queue. A `node` is usually composed of 12 or 24 `cores`, 
so 12 tasks could be run in parallel. `walltime` define for how long in hours your job will last.

Once logged in, the prompt changes for:

```
09:14:48 your_login@gaia-66(gaia-cluster)[OAR3511326->717]
```

where you see the node you are logged to (here `gaia-66`), the job ID (3511326) and the time in minutes before your job will be killed (717 minutes).

## monitoring the resources used

On a shared cluster, you have to take of **three** things:

1. memory usage
2. cores used
3. disk space

### memory

Each node has
On an interactive session, use the command `htop` to see if the memory is not full. If the system is swapping (using hard drives for memory storage)
it becomes super slow and eventually stalled.

For passive sessions, you can use [ganglia](https://hpc.uni.lu/gaia/ganglia/) to check out the nodes you are using.

### cores

even if you book 10 cores, nothing will prevent you from starting 100 jobs. They will run but then tasks are distributed on the available resources.
In this example, each task will use 1/10th of a core, then runs very slowly.  
On an interactive session, use the command `htop` to see if a process is correctly using close to 100% of a core.

### disk space

Like on your local machine, you need to check how much data you used.
Using a command line, you could use

#### disk usage

```
du -sh ~
```

to display your disk usage (`du`) for your home folder (`~`). In a form readable by human (`-h`)

#### disk free

```
df -h
```

disk free scans all disks mounted. Could takes time to display the global usage. 
Please worry if only few Mb are available on the disk you are planning to write to.


### closing connection

When you are done, you can kill yourself your job by either doing `CTRL + D` or typing `exit`.
That will free your booked ressources for others. Once done, you will still logged on the frontend and normally inside a `tmux`.
The best is to detach from the `tmux` instance and log off from the gaia frontend using  `CTRL + D` or typing `exit`.