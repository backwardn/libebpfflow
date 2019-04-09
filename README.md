# libebpfflow
Traffic visibility library based on eBPF

### Introduction
libebpfflow is a traffic visibility library based on eBPF able to compute network flows. It can be used to:
* enable network visibility
* create a packet-less network probe
* inspect host and container communications for different container runtimes

### Main features
* Ability to inspect TCP and UDP traffic
* Container visibility
* TCP latency computation
* Process and user visibility

### Supported Languages
* Golang
* C/C++

# Build
Library only
```sh
$ make libebpfflow.a
```
Library and toolebpflow
```sh
$ make
```
Go testing tool
```sh
make go_toolebpflow
```
 
### Requirements
You need a modern eBPF-enabled Linux distribution.

On Ubuntu 18.04 LTS you can install the prerequisites (we assume that the compiler is already installed) as follows:
```sh
$ sudo apt-get install libbpfcc-dev
```

### Testing
The library comes with two different tools: *toolebpflow* and *go\_toolebpflow*. The _Build_ section is reported how to build the tools.
Both tools have been developed to show how to use the library, however *toolebpflow* displays all the information provided by *libebpfflow* along with some options to filter flow events. By default all events will be shown.
```sh
$ sudo ./toolebpflow -h
Usage: ebpflow [ OPTIONS ] 
   -h, --help      display this message 
   -t, --tcp       TCP events 
   -u, --udp       UDP events 
   -i, --in        incoming events (i.e. TCP accept and UDP receive) 
   -o, --on        outgoing events (i.e. TCP connect and UDP send) 
   -r, --retr      retransmissions events 
   -c, --tcpclose  TCP close events 
   -d, --docker    gather additional information concerning containers (default: enabled)
   -v, --verbose   vebose formatting (default: every event is shown) 
Note: please run as root 
```
What follows is a demostration of the execution of *toolebpflow* in a system where both minikube with containerd as runtime and docker containers are running at the same time.
```sh
sudo ./toolebpflow -tio
Welcome to toolebpflow v.1.0.190407
(C) 2018-19 ntop.org
Initializing eBPF [Legacy API]...
eBPF initializated successfully
1554803923.684786 [lo][Sent][IPv4/TCP][pid/tid: 1446/496 [/usr/bin/kubelet], uid/gid: 0/0][father pid/tid: 1/0 [/lib/systemd/systemd], uid/gid: 0/0][addr: 127.0.0.1:53790 <-> 127.0.0.1:10252][latency: 0.10 msec]
1554803923.685139 [lo][Rcvd][IPv4/TCP][pid/tid: 2554/2329 [/usr/local/bin/kube-controller-manager], uid/gid: 0/0][father pid/tid: 2295/0 [/usr/local/bin/containerd-shim], uid/gid: 0/0][addr: 127.0.0.1:53790 <-> 127.0.0.1:10252][containerID: 275d71585e03][runtime: containerd][kube_pod: kube-controller-manager-minikube][kube_ns: kube-system][latency: 0.00 msec]
1554803924.781354 [eth0][Sent][IPv4/TCP][pid/tid: 30197/30197 [/usr/bin/curl], uid/gid: 0/0][father pid/tid: 26219/0 [/bin/bash], uid/gid: 0/0][addr: 172.17.0.2:54348 <-> 216.58.205.46:80][containerID: cbd2540ec5be][runtime: docker][docker_name: sleepy_haibt][latency: 0.22 msec]
1554803929.257494 [enp0s3][Sent][IPv4/TCP][pid/tid: 30221/30221 [/usr/lib/apt/methods/http], uid/gid: 104/65534][father pid/tid: 30216/0 [/usr/bin/apt], uid/gid: 0/0][addr: 10.0.2.15:37140 <-> 91.189.88.162:80][latency: 0.17 msec]
```
A basic example of usage in c++ can be found in the directory */examples* whereas for the Go language the example provided is the one in */go/ebpf_flow.go*. More details on how to use the library you can be found in the [ntopng](https://github.com/ntop/ntopng) code or by inspecting the code of the tool toolebpflow application.

### Open Issues
While the library is already usable in production, we plan to add some additional features including:
* Add POD/K8 visibility
* Implement periodic flow stats exports including bytes/packets/retransmissions
* Add flow termination export

