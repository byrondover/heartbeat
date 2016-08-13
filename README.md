# Heartbeat

Monitor health of microservices, and take configurable actions whenever those services change state.

## Requirements

* [Python 2.7](https://www.python.org/downloads)
* [python-daemon](https://pypi.python.org/pypi/python-daemon)
* [requests](https://pypi.python.org/pypi/requests)

## Configuration

### General Configuration

Defines Heartbeat daemon runtime configuration.

* user - Run the daemon as this user. This user must have sufficient privileges
    to run the promotion and demotion actions. If None, this defaults to the
    user launching the heartbeat daemon.
* group - Run the daemon as this group. If None, defaults to the login group of
    the user launching the heartbeat daemon.
* logfile - Log file location. Uses working directory by default. See
    'loglevel' for more details.
* loglevel - From most to least verbose, valid log levels are DEBUG, INFO,
    WARNING, ERROR and CRITICAL. INFO is recommended in production.
* pidfile - PID file location. Uses working directory by default. PID file
    doubles as a lock file in daemon mode.
* threads - Number of health checks to launch in parallel. Recommend setting
    this value to one less than the number of physical CPU cores.
* daemonize - Detaches a standalone daemon if set to True (yes). Iterates
    through health checks on a frequency defined by 'interval'.
* interval - Frequency health checks are run in seconds. Only applies in
    daemon mode. Defaults to once per minute.

### Action Definitions

Actions come in two varieties, promotions and demotions. Promotions are
triggered whenever all health checks return true (system is healthy), while
demotions are triggered whenever one or more health checks fail (system is
unhealthy).

Both promotions are demotions are defined as key-value pairs. The value is
often a command or executable to be run whenever the system changes state,
while the key is referenced in the health check definitions. Each unique key
must have both promotion and demotion values. Example below.

```ini
[promotions]
nginx = service nginx start

[demotions]
nginx = service nginx stop
```

### Health Check Definitions

Defines Heartbeat health checks. Example below.

* action - Defintes promotion and demotion actions to take when system health
    changes state. See 'Action Definitions' for more details.
* hostname - Hostname or IP address to check.
* port - TCP port to connect to (1 - 65535).
* path - Path to check (e.g. /health). Used for HTTP/HTTPS health checks only.
* timeout - Health check response timeout, in seconds.
* type - Health check to perform. Valid types are HTTP, HTTPS and TCP.

```ini
[webapp]
type = http
hostname = localhost
port = 8080
path = /app/login
action = nginx
```

## Usage

Once you have a virtual environment configured and activated, simply launch heartbeat.py!

```shell
(venv)> python ./heartbeat.py -c heartbeat.conf
```

Check heartbeat.log to monitor the daemon.
