# Open Telemetry Provider

[![wercker status](https://app.wercker.com/status/8b9d7c2d939aaf7bdbe5ae392ae2d513/s/master "wercker status")](https://app.wercker.com/project/byKey/8b9d7c2d939aaf7bdbe5ae392ae2d513)
[![GoDoc](https://godoc.org/github.com/crgimenes/otp?status.png)](https://godoc.org/github.com/crgimenes/otp)
 [![Go Report Card](https://goreportcard.com/badge/github.com/crgimenes/otp)](https://goreportcard.com/report/github.com/crgimenes/otp)

Simple and easy telemetry provider written in Go to use with [NASA OpenMCT](https://nasa.github.io/openmct/)

## Install

```
go get -u github.com/crgimenes/otp
```

OpenMPT is undergoing many changes. At the moment our code works with the last version before the API change.
```
git clone https://github.com/nasa/openmct.git
git checkout 156ba832f2da3c219f65f86198fb228557566449
```


## Example

![OpenMCT with otp](img/openMCT.png)

Taxonomy dictionary file *dictionary.json*
```json
{
    "name": "OTP",
    "identifier": "board",
    "subsystems": [
        {
            "name": "Power",
            "identifier": "pwr",
            "measurements": [
                {
                    "name": "Voltage",
                    "identifier": "pwr.v",
                    "units": "V",
                    "type": "float"
                }
            ]
        }
    ]
}
```

App example

```go
package main

import (
	"fmt"
	"log"
	"math"
	"os"
	"os/signal"

	otp "github.com/crgimenes/otp"
)

func main() {

	go func() {
		sc := make(chan os.Signal, 1)
		signal.Notify(sc, os.Interrupt)
		<-sc
		// close all connections
		otp.CloseAll()

		fmt.Print("\n")
		log.Println("Have a nice day!")
		os.Exit(0)
	}()

	otp.LoadTaxonomyDictionaryFromFile("./dictionary.json")
	otp.SubsystemHandleFunc("pwr.v", pwrvFunc)
	otp.ListenAndServe(8081, 1000)

}

var x float64

func pwrvFunc() {
	x += 0.01
	y := math.Sin(x) * (x / 2.0 * math.Pi)
	otp.SetDataValue("pwr.v", otp.MakeTimestamp(), y)
}
```

## Contributing

- Fork the repo on GitHub
- Clone the project to your own machine
- Create a *branch* with your modifications `git checkout -b fantastic-feature`.
- Then _commit_ your changes `git commit -m 'Implementation of new fantastic feature'`
- Make a _push_ to your _branch_ `git push origin fantastic-feature`.
- Submit a **Pull Request** so that we can review your changes
