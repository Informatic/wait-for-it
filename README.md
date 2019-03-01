## wait-for-http

`wait-for-http.sh` is a pure bash script that will wait on the availability of a host and TCP port.  It is useful for synchronizing the spin-up of interdependent services, such as linked docker containers.  Since it is a pure bash script, it does not have any external dependencies.

## Usage

```
wait-for-http.sh host:port [-s] [-t timeout] [-- command args]
-h HOST | --host=HOST       Host or IP under test
-s | --strict               Only execute subcommand if the test succeeds
-S STATUS | --status=STATUS Expect this status code
-q | --quiet                Don't output any status messages
-t TIMEOUT | --timeout=TIMEOUT
                            Timeout in seconds, zero for no timeout
-- COMMAND ARGS             Execute command with args after the test finishes
```

## Examples

For example, let's test to see if we can access port 80 on www.google.com, and if it is available, echo the message `google is up`.

```
$ /wait-for-http.sh https://www.google.com/
wait-for-http.sh: waiting 15 seconds for https://www.google.com/
wait-for-http.sh: https://www.google.com/ is available after 0 seconds
google is up
```

You can set your own timeout with the `-t` or `--timeout=` option.  Setting the timeout value to 0 will disable the timeout:

```
$ ./wait-for-http.sh -t 0 https://www.google.com/ -- echo "google is up"
wait-for-http.sh: waiting for https://www.google.com/ without a timeout
wait-for-http.sh: https://www.google.com/ is available after 0 seconds
google is up
```

The subcommand will be executed regardless if the service is up or not.  If you wish to execute the subcommand only if the service is up, add the `--strict` argument. In this example, we will test port 81 on www.google.com which will fail:

```
$ ./wait-for-http.sh http://www.google.com:81 --timeout=1 --strict -- echo "google is up"
wait-for-http.sh: waiting 1 seconds for http://www.google.com:81
wait-for-http.sh: timeout occurred after waiting 1 seconds for http://www.google.com:81
wait-for-http.sh: strict mode, refusing to execute subprocess
```

If you don't want to execute a subcommand, leave off the `--` argument.  This way, you can test the exit condition of `wait-for-http.sh` in your own scripts, and determine how to proceed:

```
$ /wait-for-http.sh https://www.google.com/
wait-for-http.sh: waiting 15 seconds for https://www.google.com/
wait-for-http.sh: https://www.google.com/ is available after 0 seconds
$ echo $?
0
$ ./wait-for-http.sh http://www.google.com:81
wait-for-http.sh: waiting 1 seconds for http://www.google.com:81
wait-for-http.sh: timeout occurred after waiting 15 seconds for http://www.google.com:81
$ echo $?
124
```

Additionaly you can check for specific status code:
```
$ ./wait-for-http.sh http://google.com --status=301
wait-for-http.sh: waiting 15 seconds for http://google.com
wait-for-http.sh: http://google.com is available after 0 seconds
```
