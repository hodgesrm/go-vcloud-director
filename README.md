# go-vcloud-director [![Build Status](https://travis-ci.org/vmware/go-vcloud-director.svg?branch=master)](https://travis-ci.org/vmware/go-vcloud-director) [![Coverage Status](https://coveralls.io/repos/vmware/go-vcloud-director/badge.svg?branch=master&service=github)](https://coveralls.io/github/vmware/go-vcloud-director?branch=master) [![GoDoc](https://godoc.org/github.com/vmware/go-vcloud-director?status.svg)](http://godoc.org/github.com/vmware/go-vcloud-director) [![Chat](https://img.shields.io/badge/chat-on%20slack-brightgreen.svg)](https://vmwarecode.slack.com/messages/CBBBXVB16)

This repo contains the `go-vcloud-director` package which implements
an SDK for vCloud Director. The project serves the needs of Golang
developers who need to integrate with vCloud Director. It is also the
basis of the [vCD Terraform
Provider](https://github.com/terraform-providers/terraform-provider-vcd).

## Contributions ##

Contributions to `go-vcloud-director` are gladly welcome and range
from participating in community discussions to submitting pull
requests.  Please see the [contributing guide](CONTRIBUTING.md) for
details on joining the community.

### Install and Build ###

Create a standard Golang development tree with bin, pkg, and src directories. 
Set GOPATH to the root directory. Then:
```
go get github.com/vmware/go-vcloud-director
cd $GOPATH/src/github.com/vmware/go-vcloud-director/govcd
go build
```
This command only builds a library. There is no executable.

### Example ###

To show the SDK in action run the example shown below.  
```
mkdir $GOPATH/src/example
cd $GOPATH/src/example
vi example.go    <-- Copy contents into file and fix config information.
go build
./example
```
Here's the code:
```go
package main

import (
        "fmt"
        "net/url"
        "os"

        "github.com/vmware/go-vcloud-director/govcd"
)

type Config struct {
        User     string
        Password string
        Org      string
        Href     string
        VDC      string
        Insecure bool
}

func (c *Config) Client() (*govcd.VCDClient, error) {
        u, err := url.ParseRequestURI(c.Href)
        if err != nil {
                return nil, fmt.Errorf("Unable to pass url: %s", err)
        }

        vcdclient := govcd.NewVCDClient(*u, c.Insecure)
        org, vcd, err := vcdclient.Authenticate(c.User, c.Password, c.Org, c.VDC)
        if err != nil {
                return nil, fmt.Errorf("Unable to authenticate: %s", err)
        }
        vcdclient.Org = org
        vcdclient.OrgVdc = vcd
        return vcdclient, nil
}

func main() {
  config := Config{
                User:     "myuser",
                Password: "password",
                Org:      "MyOrg",
                Href:     "https://vcd-host/api",
                VDC:      "My-VDC",
        }

  client, err := config.Client() // We now have a client
  if err != nil {
      fmt.Println(err)
      os.Exit(1)
  }
  fmt.Printf("Org URL: %s\n", client.OrgHREF.String())
}
```
