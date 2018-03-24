# Run A Script On Boot
This is a simple way of having a script run on startup:

1. Create a file for your startup script and save your script in it.
    ```
    sudo vim /etc/init.d/<NAME_OF_SCRIPT>
    ```
    *Here is an example script:*
    ```
    #! /bin/sh
    # /etc/init.d/noip 

    ### BEGIN INIT INFO
    # Provides:          noip
    # Required-Start:    $remote_fs $syslog
    # Required-Stop:     $remote_fs $syslog
    # Default-Start:     2 3 4 5
    # Default-Stop:      0 1 6
    # Short-Description: Simple script to start a program at boot
    # Description:       A simple script from www.stuffaboutcode.com which will start / stop a program a boot / shutdown.
    ### END INIT INFO

    # If you want a command to always run, put it here

    # Carry out specific functions when asked to by the system
    case "$1" in
    start)
        echo "Starting noip"
        # run application you want to start
        /usr/local/bin/noip2
        ;;
    stop)
        echo "Stopping noip"
        # kill application you want to stop
        killall noip2
        ;;
    *)
        echo "Usage: /etc/init.d/noip {start|stop}"
        exit 1
        ;;
    esac

    exit 0
    ```

2. Make the script executable
    ```
    sudo chmod 755 /etc/init.d/<NAME_OF_SCRIPT>
    ```
    *Test starting the script:*
    ```
    sudo /etc/init.d/<NAME_OF_SCRIPT> start
    ```
    *Test stopping the script:*
    ```
    sudo /etc/init.d/<NAME_OF_SCRIPT> stop
    ```

3. Register the script to be run at start-up
    ```
    sudo update-rc.d <NAME_OF_SCRIPT> defaults
    ```
    *If you ever want to remove the script from start-up, run the following command:*
    ```
    sudo update-rc -f <NAME_OF_SCRIPT> remove
    ```

# Run a script on login
This is a simple way of having a script run on startup:
1. Create a file for your login script and save your script in `/etc/profile.d/`
    ```
    sudo vim /etc/profile.d/<NAME_OF_SCRIPT>
    ```
    *Here is an example script:*
    ```
    #!/bin/sh
    sudo mount -t vboxsf -o rw,uid=1000,gid=1000 share ~/host
    ```

2. Make the script executable
    ```
    sudo chmod 755 /etc/profile.d/<NAME_OF_SCRIPT>
    ```

# Check System Utilization
Check Top Processes
```
$ top
```
- *Press `shift+e` to change KB to MB in memory section*
- *Press `q` to quit*

Check Memory Utilization
```
$ free -m
```

