---
layout: tables
title: Schemas
permalink: /data/
toc: true
---

# Data in GLEAM DP are organized an arbatrary number of [Tabular Data Resources](https://specs.frictionlessdata.io/tabular-data-resource/). These files contain the actual time series data and time-stamped auxiliary data, and are described as `resources` in the `datapackage.json` file (see [Metadata](../metadata)). The descriptions of their tabular fields follow #the [Table Schema](https://specs.frictionlessdata.io/table-schema/) specifications and are presented below in human-readable form. Fields indicated with `*` are required (i.e. cannot be empty).

The GLEAM DP schemas define the structure, constraints, and relationships of metadata and data used to describe light exposure and optical measurement studies. Schemas are expressed using [Frictionless Table Schema for tabular resources](https://specs.frictionlessdata.io/tabular-data-resource/) (e.g. study, participants, participant characteristics, light data) and JSON Schema for structured, non-tabular resources (e.g. datasets, devices, datasheets). The descriptions of their fields follow the [Table Schema](https://specs.frictionlessdata.io/table-schema/) specifications and are presented below in human-readable form. Together, they specify required fields (indicated with `*`, allowed values, and cross-references between entities, enabling validation, interoperability, and consistent interpretation across datasets.
