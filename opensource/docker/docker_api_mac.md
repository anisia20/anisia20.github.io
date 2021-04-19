### Settings
    $ brew install socat
    $ socat -d TCP-LISTEN:2376,range=127.0.0.1/32,reuseaddr,fork UNIX:/var/run/docker.sock
    $ curl http://127.0.0.1:2376/version
    {
    Version: "17.03.1-ce",
    ApiVersion: "1.27",
    MinAPIVersion: "1.12",
    GitCommit: "c6d412e",
    GoVersion: "go1.7.5",
    Os: "linux",
    Arch: "amd64",
    KernelVersion: "4.9.13-moby",
    Experimental: true,
    BuildTime: "2017-03-24T00:00:50.070226199+00:00"
    }

