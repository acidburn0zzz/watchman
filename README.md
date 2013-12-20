watchman
========

My half-assed service manager.
Documentation pending.

Releases
--------
I don't really do releases. Don't expect them often.
The master branch should be stable enough to use at this point though, I don't merge untested shit (very often).

Services
--------
A service is a script in the watchman's init.d directory.
By default it's /etc/watchman/init.d for system services and $HOME/.watchman/init.d for user ones.

Groups
------
Groups are directories in the watchman's config dir (/etc/watchman/groups or $HOME/.watchman/groups) with links to service files.
Any action performed on a group is performed on all of the services in it.
Watchman's exit code will be non-zero if any of the actions fail.

Variables
---------
There are some variables you can use to alter a service's behaviour.
All of them are optional. Yes, I mean all of them.

	$service_command	# The service's executable. Watchman will try to find the service in $PATH by the service name, so yes, even this is optional.
	$service_args		# Arguments to $service_command
	$service_username	# The user to run the service as
	$service_workdir	# The directory to cd to before running $service_command
	$service_pidfile	# The service's pid file. Will not be overwritten by watchman. By specifying this, you basically tell watchman that the service mantains it's pidfile itself.
	$service_logfile	# The file with $service_command's stdout/stderr.
	$service_actions	# Array. Available service actions. Should not be overriden, only added to. If you need to remove an action, just do unset $action.

Functions
---------

Standart functions:

	start()		# Start the service. Calls watchman.start().
	stop()		# Stop the service. Calls watchman.stop().
	restart()	# Restart the service. Calls stop(), then start().
	reload()	# Reload the service's configuration. Calls watchman.reload, which sends SIGHUP to the service.
	status()	# Show the service's status. Calls watchman.status().
	watch()		# Watch the service. Starts the service if needed and calls watchman.watch().
	depends()	# Starts the specified services. Calls watchman.depends.

Internal functions:

	watchman.msg <message>						# Prints a message in the format of: “[watchman] $message”.
	watchman.err <error message>				# Prints a message in the format of: “[watchman] (error) $message” to stderr.
	watchman.die <exitcode>						# Exit.
	watchman.depends <services list>			# Starts the specified services, returns zero only of all services start successfully.

	watchman.service_usage						# This function is called when no action is given. Shows available functions. See $service_actions

	watchman.start								# Starts the service
	watchman.stop								# Stops the service, waits for the PID to die.
	watchman.status								# Shows the status of the service.
	watchman.reload								# Sends SIGHUP to the service.
	watchman.watch								# Checks in a loop if the service is running. Restarts it if needed.
	watchman.pid_wait							# Wait for the service pid to die.
