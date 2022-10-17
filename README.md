# Telegraf execd dnsmasq input

This is a dnsmasq statistics input plugin for Telegraf, meant to be compiled separately and used externally with telegraf's execd input plugin. 

See "cache statistics" section in [https://manpages.debian.org/stretch/dnsmasq-base/dnsmasq.8.en.html#NOTES](https://manpages.debian.org/stretch/dnsmasq-base/dnsmasq.8.en.html#NOTES)

An example command to query this, using the dig utility would be

``` shell
dig +short chaos txt cachesize.bind
```

# Install Instructions

Download the repo somewhere

    $ git clone git@github.com:machinly/dnsmasq-telegraf-plugin.git dnsmasq-tp

build the "dnsmasq-tp" binary

    $ go build -o dnsmasq-tp cmd/main.go
    
 (if you're using windows, you'll want to give it an .exe extension)
 
    go build -o dnsmasq-tp.exe cmd/main.go

You should be able to call this from telegraf now using execd:

```
[[inputs.execd]]
  command = ["/path/to/dnsmasq-tp_binary"]
  # for using plugin
  # command = ["/path/to/dnsmasq-tp_binary", "-config", "/path/to/plugin.conf"]
  # for using with custom scraping interval (there also exists a parameter called "pollIntervalDisabled")
  # command = ["/path/to/dnsmasq-tp_binary", "-config", "/path/to/plugin.conf" "-pollInterval", "30s"]

  signal = "none"
  
# sample output: write metrics to stdout
[[outputs.file]]
  files = ["stdout"]
```


# Plugin Configuration:
```toml
# Read metrics about dnsmasq dns side.
[[inputs.dnsmasq]]
  # Dnsmasq server IP address and port.
  server = "127.0.0.1:53"

```

# Metrics:

- dnsmasq
  - tags:
    - server
  - fields:
    - auth (float)
    - cachesize (float)
    - evictions (float)
    - hits (float)
    - insertions (float)
    - misses (float)
	- queries (float)
	- queries_failed (float)

# Example Output:

```
dnsmasq,host=localhost,server=127.0.0.1,port=53 insertions=0,evictions=0,misses=0,hits=12,auth=0,queries=0,queries_failed=0,cachesize=150 1598519060000000000
```
