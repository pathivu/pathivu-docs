Katchi is a command line interface used to interact with the Pathivu server. It offers a variety of utility and abstraction over a lot of basic functions that you can perform with pathivu. Some of these functionalities are indexed below. If you wish for any additional functionality to be integrated then feel free to open an [issue](https://github.com/pathivu/pathivu/issues).

---

## Index

- [Installation](#installation)
  - [Get Katchi](#get-katchi)
  - [Set up environment](#set-up-environment)
- [Usage](#usage)
- [Log Discovery](#log-discovery)
- [Viewing Logs](#viewing-logs)
  - [Static View](#static-view)
  - [Tailing View](#tailing-view)
- [Querying Logs](#querying-logs)

---


### Installation
---


<h4 id="get-katchi"> Get katchi </h4>

You can find the latest release of Pathivu from the [GitHub release page](https://github.com/pathivu/pathivu/releaseshttps://github.com/pathivu/pathivu/releases). After selecting the version that you wish to go for, just run the following commands to get the Katchi CLI:

```sh
# Install the pathivu tarball (you can choose a different release)
$ wget https://github.com/pathivu/pathivu/archive/v0.1-alpha.tar.gz

# Extract the tarball
$ tar -xvf v0.1-alpha.tar.gz
```

The aforementioned commands will create a director with the name of the pathivu release that you downloaded. 

<h4 id="set-up-environment"> Set Up Environment </h4>

To get up and running with the Katchi CLI, run the following commands on your terminal:

```sh
# Add katchi to path. Here the directory is named after 
# the release that you have downloaded on your system
$ mv ./pathivu-0.1-alpha/katchi/katchi /usr/bin 

# Run the following command. If you see the mentioned output
# it means that your katchi CLI is working
$ katchi
katch is a cli tool for pathivu, which let's you folks to view all the logs in pathivu

Usage:
  katchi [command]

Available Commands:
  apps        apps gives all the app name of logs that has been ingested in the pathivu
  help        Help about any command
  logs        query logs 
  tail        tail all the apps

Flags:
  -h, --help          help for katchi
      --host string   pathivu host address

Use "katchi [command] --help" for more information about a command.
```

Katchi uses an environment variable to know which host your pathivu server is running on. If your server is running on localhost, you can set up your host by running the following command, where port `6180` is actually what the pathivu gRPC server listens on. 

```sh
$ export PATHIVU_HOST=localhost:6180
```
If you have executed all of these commands successfully then your Katchi CLI is set up and good to go for the particular shell session. Optionally you can add the `PATHIVU_HOST` environment variable in your `~/.bashrc` for permanent residency across terminal sessions. 


[Go to index](#index)

<br>

### Usage
---
Running `katchi` without any command line arguments prints out the usage of the command line utility. All of the provided functionalities can be used as keywords while calling pathivu using katchi:


```sh
katchi [function] [flags]
```
Using a `-h` flag provides the same output as running katchi without any arguments. Both of them can be used for help.

[![Katchi Help](https://asciinema.org/a/301161.png)](https://asciinema.org/a/301161)

[Go to index](#index)

<br>


### Log Discovery
---
Pathivu has an internal namespace for log ingestion across multiple apps. These namespaces can be displayed using the `apps` sub-command, which works in the following way:

```sh
$ katchi apps
App Names
svc1
svc2
svc3
svc4
svc5
```
Here, the app names that are printed are discrete log ingestion sources that can ship logs to pathivu independently.

A simple use case of this command would be to debug if all log ingestion sources are able to ship logs to pathivu or not, hence this command is very efficient in log service discovery. 


[Go to index](#index)

<br>


### Viewing Logs
---
Katchi CLI can be used to view the logs ingested by pathivu. For this purpose, Katchi offers a very intuitive `logs` sub-command, which is useful for both viewing as well as querying the ingestion.

The most minimal requirement for viewing logs in Katchi is to pass in the `--host` flag. Additionally you can also define which application logs to view using the `--apps` flag. Logs in Katchi can be viewed in two modes:

<h4 id="static-view"> Static View </h4>
Static view dumps all of the logs to *stdout*. For example, the following query can be used for viewing all of the logs ingested by the provided host and application.

```sh
$ katchi logs --host=localhost:6180 --apps=demo
{
  "data": [
    {
      "ts": 2,
      "entry": {
        "country": "pakistan"
      },
      "source": "demo"
    }, 
    {
      "ts": 1,
      "entry": {
        "country": "india"
      },
      "source": "demo"
    }
  ]
}
```


<h4 id="tailing-view"> Tailing View </h4>

The `logs` sub-command can be replaced by `tail`, which watches for logs and displays them in real-time. For example, the following command listens for all logs with the specified host.

```sh
$ katchi tail --host=localhost:6180
```

A real-time example is given below:

[![katchi tail](https://asciinema.org/a/301228.png)](https://asciinema.org/a/301228)


[Go to index](#index)

<br>


### Querying Logs
---
Katchi CLI has a very powerful query engine for viewing logs. It can be invoked by using the `--query` flag in the `log` sub-command.

A typical query in katchi looks like this:

```sh
$ katchi logs --host=localhost:6180 --apps=demo --query="distinct(country) as ctry"
{
  "data": ["pakistan", "india"]
}
```
The aforementioned command prints out the names of the distinct countries in the `demo` source log payload. 

To view the logs from a particular time, `--since` can be used. Here, a duration can be passed. For example, the following command prints the count of countries in the log payload since the last 3 hours:

```sh
$ katchi logs --host=localhost:6180 --apps=demo --query="count(country) as ctry_count" --since=3h
```
The following notations are available for `--since` flag in katchi. If you want more duration notations, to be integrated then feel free to open an [issue](https://github.com/pathivu/pathivu/issues).

| Duration | Katchi Notation |
|:----------:|:-----------------------------------:|
| Seconds | s | 
| Minutes | m | 
| Hours | h |


View more supported queries in the [query section](https://docs.pathivu.io/#/query) of the documentation.

[Go to index](#index)

<br>

