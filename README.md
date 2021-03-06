﻿[![Build Status](https://travis-ci.org/ewancook/fixr.svg?branch=master)](https://travis-ci.org/ewancook/fixr) [![Go Report Card](https://goreportcard.com/badge/github.com/ewancook/fixr)](https://goreportcard.com/report/github.com/ewancook/fixr) [![codecov](https://codecov.io/gh/ewancook/fixr/branch/master/graph/badge.svg)](https://codecov.io/gh/ewancook/fixr)


# FIXR

A wrapper around [FIXR](https://fixr.co)'s private ticket API

## Installation
1. `go get github.com/pkg/errors`
2. `go get github.com/ewancook/fixr`
3. Done!

## Example Code:

```go
package main

import (
	"fmt"

	"github.com/ewancook/fixr"
)

func main() {
	// Update FIXR version
	if err := fixr.UpdateVersion(); err != nil {
		fmt.Printf("FIXR version not updated (%s)\n", fixr.FixrVersion)
	} else {
		fmt.Printf("FIXR version updated (%s)\n", fixr.FixrVersion)
	}
	// Create client and logon
	c := fixr.NewClient("username")
	if err := c.Logon("password"); err != nil {
		fmt.Printf("logon failed (%v)\n", err)
	}
	// Fetch event information
	e, err := c.Event(141151926)
	if err != nil {
		fmt.Printf("failed getting event (%v)\n", err)
		return
	}
	// Determine ticket information
	for _, t := range e.Tickets {
		fmt.Printf("[%d] %s (£%.2f; Max: %d)\n", t.ID, t.Name, t.Price+t.BookingFee, t.Max)
	}
	// Book a ticket
	b, err := c.Book(&e.Tickets[0], 1, nil)
	if err != nil {
		fmt.Printf("purchase failed (%v)\n", err)
		return
	}
	fmt.Printf("booked: %s (PDF: %s)\n", b.Event, b.PDF)
}

```
