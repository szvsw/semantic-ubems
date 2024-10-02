# Semantic UBEM construction

## Introduction

The methodology presented aims to automate the generation of urban building energy models by leveraging Geographic Information Systems (GIS) data to construct individual energy models for each building. This system provides a structured approach to map GIS metadata fields to energy modeling components, thereby streamlining the energy modeling process for state-, national-, and global-scale analyses. Unlike traditional UBEM template archetype approaches, the key innovation lies in avoiding the need for energy modelers to manually create full-factorial combinations of building templates. Instead, it enables them to develop a library of individual components and define mapping rules that automatically select appropriate components based on building-specific metadata. This approach maintains a _separation of concerns_ between the semantic information which lives in the GIS data (e.g. "woodframe construction" or "high-income household"), and the _interpretation_ of that data as it is _compiled_ into an energy model. It also enables flexibility by providing mechanisms for dealing with incomplete or incrementally updated data as well as probabilistic modeling. It also provides an easy mechanism for running different types of analysis on the same underlying GIS data, for instance running the data through a black-box simulation engine for rapid monthly energy estimation and then separately through a white-box simulation engine with an entirely different set of modeling inputs for a detailed overheating analysis.

---

## System overview

The system operates by interpreting a manifest (specification file) that outlines how GIS metadata fields correspond to energy modeling components. The manifest is defined in YAML to emphasize human readiability and accessible editing. The manifest is divided into three primary sections:

1. **requires**: Specifies the required metadata fields, their data types, allowable ranges or enumerations, and default values. Can be used to validate compatibility with incoming GIS data.
2. **computes**: Defines computed fields derived from existing metadata, allowing for grouping or breakpointing of data to create new variables which can be referenced in the component map.
3. **component-map**: Establishes the mapping rules that determine which energy modeling components (or values) are selected based on the provided and computed fields.

Energy modeling engines can use a manifest to systematically interpret semantic GIS metadata and compile specific energy models, ensuring flexibility, consistency and scalability in large-scale bottom-up energy modeling.

## Manifest schema

### Example manifest

An example manifest which defines each of the sections mentioned above is provided below. Subsequent sections will discuss each part of the manifest in more detail.

```yaml
massachusetts:
  requires:
    year_built:
      numeric:
        min: 1620
        max: 2024
        integer: true
      missing:
        default: 1980

    typology:
      enum:
        - single-family
        - multi-family
        - commercial
        - industrial
        - institutional
        - mixed-use
      missing:
        skip:

    construction:
      enum:
        - wood
        - masonry
        - concrete
        - steel
      missing:
        fail:

  computes:
    year_bracket:
      source: year_built
      breakpoints:
        - 1975
        - 2003

    typology_family:
      source: typology
      groups:
        SFH:
          - single-family
        MFH:
          - multi-family
        CM:
          - commercial
          - industrial
          - institutional
          - mixed-use

  component-map:
    space_use:
      base: typology_family
    envelope:
      base:
        - typology_family
        - construction
        - year_bracket
```

### The `requires` section: defining GIS metadata expectations

The `requires` section enumerates the essential metadata fields that should be present for each building in the GIS dataset. Each field is specified with the following attributes:

- **Data Type**: Indicates whether the field is numeric or categorical (enum).
- **Validation Constraints**:
  - For numeric fields: `min` and `max` values to ensure data validity.
  - For enum fields: A list of acceptable categorical values.
- **Missing Data Handler**: Specifies a default value to be used if the field is missing or falls outside the valid range, or whether to skip a buiding or fail entirely.

_Example from a manifest:_

```yaml
requires:
  year_built:
    numeric:
      min: 1620
      max: 2024
    missing:
      default: 1980

  typology:
    enum:
      - single-family
      - multi-family
      - commercial
      - industrial
      - mixed-use
    missing:
      skip:
```

This indicates that `year_built` is a required numeric field with valid years ranging from 1620 to 2024. If a building lacks this data, a default value of 1980 is assigned. Similarly, it indicates that a `typology` field should be provided in the GIS data, and if a value is missing, that building should be skipped entirely.

### The `computes` section: deriving new tags

The `computes` section allows for the creation of new fields based on transformations or groupings of existing metadata fields. This is particularly useful for categorizing continuous variables or aggregating multiple categories into broader groups.

- **Breakpoints**: Used to segment numeric fields into ranges.
- **Groups**: Used to aggregate categorical variables into higher-level categories.

_Example from a manifest:_

```yaml
computes:
  year_bracket:
    source: year_built
    breakpoints:
      - 1975
      - 2003
  typology_family:
    source: typology
    groups:
      residential:
        - single-family
        - multi-family
      non-residential:
        - commercial
        - industrial
        - mixed-use
```

This computes a `year_bracket` field by segmenting `year_built` into ranges based on the specified breakpoints (before 1975, 1975-2003, after 2003). Similarly, it groups up multiple different values of the `typology` enum field into coarser groups.

### The `component-map` section: defining component assignment rules

The `component-map` section defines how energy modeling components should be selected based on one or more metadata fields, which can be either `required` fields directly provided by the GIS data or `computed` fields derived accordingly, or a mixture. Each energy model component (e.g., `space_use`, `envelope`) is associated with one or more fields that determine its selection. Additionally, for more granular control, specific values can be injected (e.g. for updating an Equipment Power Density within a previously selected component).

- **Base Fields**: The fields used to select the corresponding component.
- **Naming Convention**: Components are named based on the values of the base fields, concatenated with underscores.

_Example from a manifest:_

```yaml
space_use:
  base: typology_family
envelope:
  base:
    - typology_family
    - construction
    - year_bracket
```

For `space_use`, the system will select a component matching the value of `typology_family`. For `envelope`, it will look for a component named by concatenating `typology_family`, `construction`, and `year_built` values, e.g. `residential_wood_pre1975`.

---

## Operational workflow

For each building in the GIS dataset, the system follows these steps:

1. **Validation and Default Assignment**:

   - Check that all required fields are present and valid.
   - Assign default values where data is missing or invalid.

2. **Computed Field Generation**:

   - Compute new fields as specified in the `computes` section.
   - Apply breakpoints and groupings to derive new categorical variables.

3. **Component Selection**:
   - Use the `component-map` rules to determine the appropriate energy modeling components.
   - Construct component names by concatenating the values of the base fields when needed.
   - Retrieve the corresponding components from the library.
   - Dynamically inject specific values when requested for leaf parameters (e.g. Equipment Power Density)

### Example process for a single building

- **Metadata**:

  - `year_built`: 1990
  - `typology`: `commercial`
  - `construction`: `steel`

- **Processing**:
  1. **Validation**:
     - All required fields are present and valid.
  2. **Computed Fields**:
     - `year_bracket`: Since 1990 is between 1975 and 2003, `year_bracket` is set to the middle bracket, `1975to2003`.
     - `typology_family`: `typology` of `commercial` maps to `non-residential`.
  3. **Component Mapping**:
     - **`space_use`**: Will search for a `space_use` component called `non-residential`.
     - **`envelope`**: Combine `typology_family` (`non-residential`), `construction` (`steel`), and `year_bracket` (`1975to2003`) to form the component name `non-residential_steel_1975to2003`. Select the corresponding envelope component.
