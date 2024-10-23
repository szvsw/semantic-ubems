# Semantic Stock Modeling: Towards a Global Building Inventory

## Abstract

Rapid decarbonization of the building sector is critical for mitigating climate change, yet existing urban building energy modeling (UBEM) approaches face significant limitations when scaled up. Traditional UBEM relies on archetypal energy models which compromises building-scale accuracy and requires exccessive modeling efforts and data fusion at superurban scales. This paper introduces Semantic Stock Modeling (SSM), a novel framework that replaces archetypal templates with semantic building descriptions which are decoupled from model translation layers. By decoupling high-level, human-readable building features from computational representations, SSM accommodates incomplete or probabilistic data and facilitates contributions from multiple stakeholders with different skillsets, including GIS managers, energy modelers, and research software engineers. To illustrate the process of semantic stock modeling, a case study was conducted modeling approximately 2.3 million residential buildings in Massachusetts to assess the economic viability of heat pump adoption. The SSM approach enables detailed, building-specific analyses, revealing significant variations in economic outcomes based on heating systems and regional characteristics. For instance, homes using delivered heating oil showed clear financial benefits from switching to heat pumps, while those using natural gas did not. These insights underscore the importance of semantic granularity in policy decision-making. By providing a scalable and adaptable framework, SSM has the potential to transform large-scale building energy modeling, supporting efforts toward energy efficiency and carbon reduction at state, national, and global scales. This paradigm shift not only enhances modeling granularity but also empowers stakeholders to make informed decisions to accelerat decarbonization and contribute to global climate objectives.

## Introduction

TODO:

1. Urgency of decarbonization
1. Intersection of policy, economics, retrofitting rates: carbon, energy, and cost reduction are typically aligned, but not always - and equity is often orthogonal
1. Quick overview of existing approaches with templates, bottom up, top-down
1. segue into problems with traditional approaches

Urban building energy modeling traditionally has relied on the law of large numbers to generate urban-scale insight: underlying archetypal energy models are assumed to be unbiased such that individual energy modeling errors at the building-scale destructively interfere to result in sufficient accuracy at the aggregated urban-scale, given a sufficient quantity of buildings within each archetypal class (cite shoeboxer papers). On the one hand, this is a form of regularization, but on the other, it is a wasted opportunity. Although UBEMs are bottom-up, they still fail to deliver actionable information at the building scale. This leaves UBEMs with unfulfilled potential: every building is modeled, but no building is right. If compute is going to be spent, it ought to be fully leveraged to yield the most information it can. The most obvious use-case for building-scale accuracy in large urban datasets is in triaging the building stock to identify not just which _types_ of buildings are best targeted with an intervention or policy measure, but which _actual_ buildings should be targeted.

At the same time as an increase in granularity is desired, an increase in scale is sought: how do we move from the urban-scale to the state/province-scale, the national-scale, and even the global-scale? While superficially, the goals of building-scale accuracy and superurban-scale data might seem opposed, many of the barriers - and thus solutions - to delivering both are shared. In both cases, one of the key challenges arises from the combination of data availability (or lack thereof) in conjunction with the curse of dimensionality. On the one hand, to get building-scale accuracy, more and more dimensions are needed to accurately capture the differences between each building and its neighbor, and thus an explosion in the full-factorial combinatoric possibilities which each require unique archetypal energy model templates. Similarly, as more and more geographic regions are covered, more possible values exist wihin each dimension (e.g. different residential envelope constructions in each region), which similarly induces a significant increase in the modeling complexity to create the necessary templates (cite paper with table counting number of templates required per region). This is exacerbated by varying degrees of data-availability: many buildings, or even whole regions, may have incomplete data available. For instance, the heating system of a building may be completely unknown, which can have a large impact on the economics of a retrofit adoption incentivization strategy, as dicussed in the case study in this paper. Similarly, only distributional information about a certain feature may be known, e.g. income level of the homeowner based off of census data. This scenario of incomplete or probabilistic data does not have a clear resolution in a framework where a single building is assumed to be associated with a single prototypical archetype energy model template.

Another related major challenge that affects both building-scale accuracy and global-scale reach is data fusion: there is no authoritative source of truth on the state of the building stock in any one region, let alone across the world. Any superurban-scale modeling effort would necessarily require integrating a patchwork of GIS datasets. For instance, a model might consume public datasets to extract fields like geometry and height for generating 2.5D building footprint based models, while consuming private or state-supplied tax parcel databases for information like building usage or age, and then consuming state-of-the-art research datasets for fields like computer-vision-extracted window-to-wall ratios. Deciding how to fuse these datasets, segment them into archetypes, and then create archetypal energy model templates for each segmented class is a non-trivial task. Furthermore, different regions may require entirely different energy models, with no universal isotropic building energy model which is applicable to the entire building stock achievable: the correct way to model a suburban home in Western Massachusetts might be drastically different from the correct way to model a commercial building in London or a naturally ventilated apartment building in Morocco.

The scope also necessarily requires integrating multiple stakeholders, none of whom necessarily have all of the knowledge, skills or data at hand necessary to complete the pipeline. GIS managers may have the data representing the building stock, but insufficient knowledge of what is relevant to the building energy modeling effort (or another analysis modality). Energy modelers may have a good sense of what each of the different energy models ought to look like given a particular building description, but not the ability to programmatically generate or execute them all. Research software engineers may have the skillset to programmatically generate templates or to orchestrate the execution of models, but not the requisite knowledge for how to interpret a given row of GIS data into a particular energy model.

To address these challenges attendant to building-scale accuracy and superurban reach, we propose a new approach to representing and organizing large datasets of real-world buildings: Semantic Stock Modeling (SSM). The key paradigm shift vis-a-vis traditional UBEMs is to dispose of the notion of archetypes, i.e. the association of a prototypical, pre-defined (excluding geometry) energy model with each building; instead, we adopt a new organizational framework which consists of semantic building descriptions which are agnostic as to their computational representation and consist of high-level, human-readable features, paired with translation layers which interpret the semantics and compile energy models (or other analysis modalities) and finally the attendant computation layers which execute the models. While the lines between these two approaches is blurred in some sense - under the hood, many UBEM studies likely implement some form of description/translation/computation isolation - here, we hope to shift the vocabulary and organizational frameworks by more explicitly surfacing and decoupling these different layers. In so doing, the goal is to leverage the affordances of such isolation to enable the creation of more robust, detailed, and larger scale building stock energy models. Part of the motivation is to more rigorously define a framework for the interactions of GIS managers, building energy modelers, and research software engineers which can better facilitate the development and construction of national and global-scale building stock models while supporting different levels of data availability, uncertainty, and analysis modalities.

[figure]

To illustrate the utility of such a framework, a case study is conducted which models approximately 2.3 million residential buildings in the state of Massachusetts while considering a variety of fields associated with each building, such as age, single-family vs multi-family, heating systems, and more. This model is then used to analyze the economic efficacy of heat-pump adoption for homeowners and illustrate how increasing the level of semantic knowledge about heating systems drastically changes the outlook for the state of Massachusetts.

In proposing Semantic Stock Modeling, our goal is not to immediately define a completed, universal standard for how building stock models should be represented, but rather to provide a new methodological framework which building stock modeling efforts can follow to better organize stakeholders and empower large scale data exchange and analysis.

## Lit Review

1. classic ubem papers
1. existing templating efforts
1. similar approaches which are not tied to real buildings: resstock - highly detailed, distributions, etc etc, but can't tell u what 123 Oak st's energy use is.
1. lehong cisbat granularity paper

## Methodology

The core challenges which Semantic Stock Modeling aims to address include the modeling complexity encountered when trying to increase the granularity and/or scale of existing urban building energy modeling approaches, the incompatibility of existing approaches with uncertainty or incomplete data, the difficulty of fusing patchwork GIS datasets, and the disparate skillsets required to execute a large-scale analysis. The fundamental solution proposed here is to more explicitly isolate the high-level semantic descriptions of buildings from their interpretation and conversion into executable models.

The term _semantic_ is chosen to emphasize the level of detail of information stored in the building description layer and re-inforce its conceptual isolation from the _syntactic_ interpretation within the translation and computation layers. This enforces a stricter separation of concerns between the semantic building descriptions and the syntactic energy models. Rather than storing detailed energy-model information, which might often be numerical value assignments or other energy-model specific components/information, e.g. `Window U-Value = 2.72 W/m2K, Window SHGC=0.8` or `Infiltration rate = 0.05 m3/s/m2 exposed exterior surface area`, this layer is meant to capture coarser, human-readable semantic information about a building, e.g. `Single Pane Window` or `Leaky envelope`. The translation layer implemented by any particular modeling engine is responsible for converting these high-level fields into whatever form necessary to execute its computation. In so doing, this separation of concerns also allows for better separation of responsibilities which can better take advantage of the different skillsets of the various process stakeholders: GIS managers are clearly responsible for the building description layer, energy modelers are clearly responsible for defining the interpretation of fields by defining atomistic components and component selection mappings, and finally research software engineers are clearly responsible for the compilation and execution of models according to the transformations and component mappings defined by the energy modelers on top of the GIS data provided by the GIS managers. An obvious requirement of such a framework then is concise, clear interfaces between each layer: a layer must provide manifests of what information it requires, and what information it produces.

By separating concerns, we enable the model description and computation to become decoupled in separate layers, with the obvious consequence that the model compilation and computation layer can be swapped out for different performance/cost tradeoffs or different modalities of analysis altogether. For instance, a variety of approaches can be used to achieve the desired scale of results in a reasonable amount of time: on the one hand, using high horizontal capacity and whitebox physics-based models, i.e. distributed cloud computing with large volumes of parallel nodes with low-throughput per node, or on the other hand, high vertical capacity and blackbox machine-learned models of the same function, i.e. exteremely high-throughput on relatively few GPU-equipped compute nodes. Similarly, different analysis engines can be supported from the same building-stock description, from a Bayesian model of a retrofit uptake economics analysis to an hourly heat-risk analysis which is accurate at the building scale, both consuming the same source dataset. The isolation of the building stock descriptions from the analysis engines makes it (relatively) straightforward to support these different approaches simultaneously.

[figure]

Additionally, by separating the concerns and disposing of the notions of templates, it allows the upstream semantic building stock descriptions to easily incorporate probabilistic data without concern for how the downstream analysis tasks may or may not acknolwedge it. For instance, census GIS data may easily be merged into the dataset to assign probabilities to income levels or number of occupants and used to drive downstream selection of occupancy schedule or equipment densities (cite yuqian); or computer vision models might be used to add columns representing envelope construction from street-view images with probabilities over a few categories (e.g. woodrame, masonry, steelframe) (cite); or the heating system of a home, which might be totally unknown and assumed to have uniform probability over the availability categories. It is then incumbent upon the translation and execution layers to decide how to acknowledge or ignore such uncertainties according to their needs. This naturally pairs then with incremental data availability: as new data becomes available, distributions in the upstream data are updated and downstream computations can be re-run to collapse uncertainty.

[figure]

The semantic stock modeling approach can be broken up into the n major stages, roughly following an extract-transform-load (ETL) pattern from data engineering:

1. Acquisition of GIS datasets.
1. Transformation of GIS datasets into cleaned semantic fields.
1. Definition of ruleset describing transformations from semantic fields into energy modeling components.
1. Translation of semantic fields into assembled energy models according to specified transformation rules.
1. Execution of energy models.
1. Data post-processing and results anlaysis.

[figure]

todo

1. manifests/more detailed specs examples
1. leveraging local modeler knowledge etc
1. regions of relevance (e.g. Massachusetts, portugal etc) etc

## Case Study: Using SSM to evaluate heat-pump economic efficacy in Massachusetts

Massachusetts is undertaking an ambitious statewide energy transition. As a heating-dominated climate, one of the key components of its decarbonization pathway is the electrification of heating through heat pumps, vastly reducing both site energy and source emissions. State-funded programs like MassSave provide financial incentives to homeowners to increase heat pump adoption rates (cite). However, it is not necessarily clear that the incentive strategies, which largely do not differentiate funding levels available between any two particular homes, are being effectively deployed (cite de simone). To illustrate this, a model of approximately 2.3m residential homes in Massachusetts was developed with the SSM methodology. The model is used to determine the net change in dollars spent for home energy usage for each home when transitioning to a heat pump; crucially, the financial rationality of switching to a heat pump entirely depends on whether or not the home is originally heated via delivered heating oil or natural gas and the associated grid economics.

To conduct this case study, two data sources are used: the Overture Maps Foundation Buildings database (cite), which contains footprints and heights for every building in Massachusetts and limited building-use information, and the MassGIS Standardized Assessors' Parcel Map dataset, which contains more detailed age and parcel use data (cite).

### Semantic Stock Model Workflow

Following the SSM philosophy, a sequence of transformations are described to generate the high-level semantic fields which are available to the energy modeler, and guide their component definitions. First, we will specify the datasets being used.

```yaml
sources:
  overture:
    - geometry
    - height
    - primary_use
    - secondary_use
    - area
  massgis:
    - year_built
    - parcel_use
```

Next, we specify a sequence of transformations from the raw dataset to generate the semantic fields.

```yaml
computed:
  divide:
    source: height
    divisor: 3.5
    round: up
    output: num_floors

  concatenate:
    separator: ": "
    columns:
      - primary_use:
          source: overture
      - secondary_use:
          source: overture
      - parcel_use:
          source: massgis
    output: uses

  classify:
    source: uses
    targets:
      - residential single-family
      - residential multi-family
      - non-residential
    output: use_class

  group:
    source: year_built
    breakpoints:
      values:
        - 1975
        - 2003
      before: pre_
      between: _to_
      after: post_
    output: age_bracket
```

Notice that the classification of the raw `uses` field into coarse categories is left as an implementation detail, which could be easily implemented with anything from regular expression pattern matching to traditional NLP techniques, embedding vector search, or LLM-driven word-association.

Next we attach a filter, since we are only want to consider residential buildings with an area less than or equal to X sqm:

```yaml
filters:
  op: and
  conditions:
    - source: use_class
      condition:
        starts-with: residential
    - source: area
      condition:
        le: x
```

Finally, we define which fields are available for use by the energy modeler:

```yaml
yields:
  area:
    min: 0
    max: null
    units: sqft
  num_floors:
    min: 1
    max: null
    type: integer
    units: floors
  age:
    min: 1670
    max: 2024
    type: integer
    units: years
  age_bracket:
    categories:
      - pre_1975
      - 1975_to_2003
      - post_2003
  use_class:
    categories:
      - residential multi-family
      - residential single-family
```

The energy model interpretation layer can then be represented with a straightforward component map. The component-map is used to determine how various components defined by the energy modeler will be selected and compiled in the energy model, including nested selections and value overwriting. For instance, here we see that different envelope constructions will first be selected according to whether or not the building is a single-family home or multi-family home; then the window constructions will be updated according to the age bracket as well the nested thickness of the facade construction's insulation layer. We also see that a single default space use template will be used, representing things like thermostat setpoints and schedules, while the occupant density will be directly specified according to whether or not it is a single-family or multi-family home, and the nested equipment density will be decremented according to the year built.

**TODO: update the below with actual values**

```yaml
component-map:
  envelope:
    source: use_class
    window-construction:
      source: age_bracket
    facade-construction:
      insulation-layer:
        thickness:
          source: age_bracket
    infiltration:
      source: age_bracket

  space_use:
    source: default
    occupants:
      density:
        assign:
          source: use_class
          value:
            - residential single-family: 0.xx
            - residential multi-family: 0.xx
    loads:
      source: use_class
      epd:
        op:
          source: age_bracket
          type: subtract
          value:
            - pre_1975: 0
            - 1975_to_2003: 0.4
            - post_2003: 0.8
```

While this is not a complete description of the building energy model used in the case study, it illustrates the conceptual utility of the SSM approach: the energy modeler only needs to define a few components, and then a ruleset for how those components should be selected, combined, or mutated when compiling the energy model for each building according to the semantic fields provided. As previously mentioned, the domain-specific language illustrated here is not meant to be a final standard, but rather, just one example of how this approach can be used to clearly communicate the connection between the different layers.

This information in conjunction with the base datasets is sufficient to construct the EnergyPlus model for each building. To construct the energy models, a rotated rectangle is fit around the footprint of each building; if the width or depth of this rectangle exceeds 15m, a perimeter/core floorplate division into five zones is performed. The zones are then extruded to a floor-to-floor height of 3.5m and repeated according to the number of floors in the building. Ideal loads airsystems are used to generate annual thermal demands. These demands are converted to site energy use distributions according to probabilistic assumptions about system performances according to selected heating fuels. Similarly, assumptions are made for emissions intensities and energy prices reflecting the highly divergent cost of fuels in Massachusetts.

Annual cost and carbon distributions due to a heat pump upgrade can then easily be modeled according to the difference between the relevant resulting distributions. These assumptions can be found in the table below. Two scenarios are explored: one in which no information is known about the initial heating fuel system, and another in which we assume we know the heating fuel system for each building; while that data was not readily available, is chosen to clearly illustrate the effect of increasing the semantic uncertainty about the building on analysis results.

Each model is executed as part of a massively parallel scatter-gather job in the cloud with thousands of compute nodes, taking approximately 2 hours to complete all 2.3m simulations.

**TODO: include assumptions table.**

### Results

When the initial heating fuel source is not known for any particular building, we assume equal probabilities that it is a natural gas system or a delievered oil system. As a result, we see a highly bimodal annual cost distribution for each building, with mean and median values barely positive, but massive uncertainty in whether the actual value will be a net cost reduction or increase in the annual utility expenditure by the homeowner depending on the original fuel source.

[figure]

To illustrate the effect of increasing the semantic certainty, we assume buildings in western Massachusetts are largely consuming delivered fuel while buildings in eastern Massachusetts are largely consuming natural gas. After doing so and re-running model the postprocessing with the cached thermal demand results, the distribution for each building's change in annual utility bill naturally collapses into a much more compact unimodal form, illustrating the clear economic incentive for oil-based homes to upgrade to a heat-pump and the clear economic disincentive to upgrade for typical natural gas furnace homes.

[figure]

This analysis can now naturally be paired with retrofit installation cost models (cite) homeowner willingness-to-pay models (cite), census data, and incentive equity and adoption models to evaluate the efficacy of policy decisions at the state-scale.

## Discussion

1. Future work
   1. illustrating different ways of incorporating distributional data
   1. discussing things like representing retrofit actions or costs etc
   1. more rigorously defining the stock description/transformation/compilation manifests/standards.

## Conclusion

lorem ipsum
