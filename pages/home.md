---
title: GLEAM DP
background: /assets/home.jpeg
permalink: /
description: Data exchange format for optical radiation and visual experience data
---

**Gathered Light Exposure and Auxiliary Measurements - Data Package** (GLEAM DP) is a community-developed data exchange format for optical radiation and visual experience data. A GLEAM DP is a [Frictionless Data Package](https://specs.frictionlessdata.io/data-package/) in which `datapackage.json` declares all files as `resources` and links them to schemas for validation and interoperability.

File / resource | Description
--- | ---
`datapackage.json`{:.d-inline-block style="width:150px;"} | Package-level metadata plus a `resources` list describing all files in the package.
Core resources (required) | Canonical resources required by the GLEAM DP profile: `study`, `participants`, `datasets`, `devices`, `device_datasheets`.
Optional core resources | Canonical resources that may be included when available: `participant_characteristics`.
Additional resources | Any number of additional resources (tabular or JSON), e.g. optical radiation, light exposure time series, or auxiliary signals. Additional resources are validated when they declare a `schema` (Table Schema) or `jsonSchema` (JSON Schema).

## Context

See [visualdiet.org](https://www.visualdiet.org) to learn more about GLEAM DP and the [metadata publication](https://doi.org/10.1186/s44247-024-00113-9) to learn more about the metadata structure.

<!--
## Example

See the [example dataset](example/).
-->
## Software

- [LightLogR](https://github.com/tscnlab/LightLogR): R package to read and manipulate GLEAM DP.

## Validation

### Validation principles

Validation in GLEAM DP is performed using a **custom validator** that applies the following principles:

- **Core resources** defined by the GLEAM profile (e.g. `study`, `participants`, `datasets`, `devices`, `device_datasheets`)  
  must declare a schema (`schema` for tabular resources, `jsonSchema` for JSON resources).  
  Missing schemas for core resources result in **validation errors**.

- **Additional resources** may be included with any name.
  - If a `schema` or `jsonSchema` is declared, it is used for validation.
  - If no schema is declared, schema-based validation is skipped and a **warning** is issued.

- **Tabular resources** are validated against their declared schemas using the Frictionless *Table Schema* specification.

- **JSON resources** are validated against their declared schemas using *JSON Schema*.

- In addition to schema validation, the custom validator performs **cross-resource consistency checks**
  (e.g. verifying references between studies, participants, devices, datasets, and device datasheets).

To enable validation, the `datapackage.json` of your dataset should reference the used version of GLEAM DP in `profile`. Core resources use canonical names; additional resources may use any name. Resources are validated against their declared `schema` (tabular) or `jsonSchema` (JSON).

```json
{
  "name": "gleam-dataset",
  "profile": "https://raw.githubusercontent.com/tscnlab/GLEAM-dp/<version>/schemas/gleam-dp-profile.json",
  "resources": [
    {
      "name": "study",
      "path": "data/study.json",
      "profile": "https://raw.githubusercontent.com/tscnlab/GLEAM-dp/<version>/schemas/json-entity-resource.json",
      "mediatype": "application/json",
      "jsonSchema": "https://raw.githubusercontent.com/tscnlab/GLEAM-dp/<version>/schemas/study.schema.json"
    },
    {
      "name": "participants",
      "path": "data/participants.json",
      "profile": "tabular-data-resource",
      "format": "json",
      "mediatype": "application/json",
      "schema": "https://raw.githubusercontent.com/tscnlab/GLEAM-dp/<version>/schemas/participants.schema.json"
    },
    {
      "name": "datasets",
      "path": "data/datasets.json",
      "profile": "https://raw.githubusercontent.com/tscnlab/GLEAM-dp/<version>/schemas/json-entity-resource.json",
      "mediatype": "application/json",
      "jsonSchema": "https://raw.githubusercontent.com/tscnlab/GLEAM-dp/<version>/schemas/dataset.schema.json"
    },
    {
      "name": "devices",
      "path": "data/devices.json",
      "profile": "https://raw.githubusercontent.com/tscnlab/GLEAM-dp/<version>/schemas/json-entity-resource.json",
      "mediatype": "application/json",
      "jsonSchema": "https://raw.githubusercontent.com/tscnlab/GLEAM-dp/<version>/schemas/device.schema.json"
    },
    {
      "name": "device_datasheets",
      "path": "data/datasheets/",
      "profile": "https://raw.githubusercontent.com/tscnlab/GLEAM-dp/<version>/schemas/json-entity-resource.json",
      "mediatype": "application/json",
      "jsonSchema": "https://raw.githubusercontent.com/tscnlab/GLEAM-dp/<version>/schemas/device_datasheet.schema.json"
    },

    {
      "name": "participant_characteristics",
      "path": "data/participant_characteristics.csv",
      "profile": "tabular-data-resource",
      "format": "csv",
      "mediatype": "text/csv",
      "schema": "https://raw.githubusercontent.com/tscnlab/GLEAM-dp/<version>/schemas/participant_characteristics.schema.json"
    },

    {
      "name": "wrist_light",
      "path": "data/wrist_light.csv",
      "profile": "tabular-data-resource",
      "format": "csv",
      "mediatype": "text/csv",
      "dialect": { "delimiter": ";", "decimalChar": "." },
      "schema": "schemas/wrist_light.schema.json"
    },
    {
      "name": "optical_radiation",
      "path": "data/optical_radiation.csv",
      "profile": "tabular-data-resource",
      "format": "csv",
      "mediatype": "text/csv",
      "schema": "schemas/optical_radiation.schema.json"
    }
  ]
}
```

### Running the validator

To validate a GLEAM DP dataset, use the GLEAM DP validator:

```shell
pip install frictionless
python3 gleam_validator.py path/to/your/datapackage.json
```

## Contribute

Questions? Suggestions? Contribute to the development of GLEAM DP by watching the [repository](https://github.com/tscnlab/GLEAM-dp) and participating in [issue discussions](https://github.com/tscnlab/GLEAM-dp/issues).

## Citation

To cite the Metadata paper:

> Spitschan, M., Hammad, G., Blume, C., Schmidt, C., Skene, D., J., Wulff, K., Santhi, N., Zauner, J., Münch, M., Metadata recommendations for light logging and dosimetry datasets. BMC Digit Health 2, 73 (2024). https://doi.org/10.1186/s44247-024-00113-9

To cite the GLEAM DP standard, use the [citation provided by GitHub](https://github.com/tscnlab/GLEAM-dp), which is generated from a `CITATION.cff` file. See [About CITATION files](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-citation-files) for more info.

GLEAM DP is managed by the [GLEE Team](https://www.visualdiet.org/team).

## Acknowledgement

The technical implementation of domain specific metadata to the Frictionless standard and automated documentation was supported by and is based on the the work of the [Camtrap DP](https://camtrap-dp.tdwg.org) project.
