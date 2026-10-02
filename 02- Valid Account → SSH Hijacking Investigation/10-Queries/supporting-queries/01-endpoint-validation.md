# Project 02 — Endpoint Validation Query

## Purpose

Validate that the monitored Linux endpoint is the expected Project 02 system before and during investigation activity.

## KQL

```kql
host.name : "soc-linux"
```

## Investigation Context

The query restricts the result set to the designated Project 02 Linux endpoint.

Fixed lab endpoint:

* Host: `soc-linux`
* IP: `192.168.1.16`

## Usage

This query can be used as a supporting filter when validating:

* Linux endpoint telemetry
* SSH events
* Authentication events
* Detection events
* Investigation results

## Project Mapping

* **Project:** 02 — Valid Account → SSH Hijacking
* **Phase:** Supporting Query
* **Platform:** Elastic Security
* **Endpoint:** `soc-linux`

