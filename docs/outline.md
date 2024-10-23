# Semantic UBEMs: Towards a Global Building Inventory

The methodology presented aims to automate the generation of urban building energy models by leveraging Geographic Information Systems (GIS) data to construct individual energy models for each building.
This system provides a structured approach to map GIS metadata fields to energy modeling components, thereby streamlining the energy modeling process for state-, national-, and global-scale analyses.
Unlike traditional UBEM template archetype approaches, the key innovation lies in avoiding the need for energy modelers to manually create full-factorial combinations of building templates.
Instead, it enables them to develop a library of individual components and define mapping rules that automatically select appropriate components based on building-specific metadata.
This approach maintains a _separation of concerns_ between the semantic information which lives in the GIS data (e.g. "woodframe construction" or "high-income household"), and the _interpretation_ of that data as it is _compiled_ into an energy model.
It also enables flexibility by providing mechanisms for dealing with incomplete or incrementally updated data as well as probabilistic modeling.
It also provides an easy mechanism for running different types of analysis on the same underlying GIS data, for instance running the data through a black-box simulation engine for rapid monthly energy estimation and then separately through a white-box simulation engine with an entirely different set of modeling inputs for a detailed overheating analysis.

## Introduction

1. Urgency of decarbonization
1. Intersection of policy, economics, retrofitting rates: carbon, energy, and cost reduction are typically aligned, but not always - and equity is often orthogonal
1. Quick overview of existing approaches with templates, bottom up, top-down

Urban building energy modeling traditionally has relied on the law of large numbers to generate urban-scale insight: underlying archetypal energy models are assumed to be unbiased such that individual energy modeling errors at the building-scale destructively interfere to result in sufficient accuracy at the aggregated urban-scale, given a sufficient quantity of buildings within each archetypal class. On the one hand, this is a form of regularization, but on the other, it is a wasted opportunity. Although UBEMs are bottom-up, they still fail to deliver actionable information at the building scale. This leaves UBEMs with unfulfilled potential: every building is modeled, but no building is right. If compute is going to be spent, it ought to be fully leveraged to yield the most information it can. The most obvious use-case for building-scale accuracy in large urban datasets is in triaging the building stock to identify not just which _types_ of buildings are best targeted with an intervention or policy measure, but which _actual_ buildings should be targeted.

At the same time as an increase in granularity is desired, an increase in scale is sought: how do we move from the urban-scale to the state/province-scale, the national-scale, and even the global-scale? While superficially, the goals of building-scale accuracy and superurban-scale data might seem opposed, many of the barriers - and thus solutions - to delivering both are shared. In both cases, one of the key challenges arises from the combination of data availability (or lack thereof) in conjunction with the curse of dimensionality. On the one hand, to get building-scale accuracy, more and more dimensions are needed to accurately capture the differences between each building and its neighbor, and thus an explosion in the full-factorial combinatoric possibilities which each require unique archetypal energy model templates. Similarly, as more and more geographic regions are covered, more possible values exist wihin each dimension (e.g. different residential envelope constructions in each region), which similarly induces a significant increase in the modeling complexity to create the necessary templates. This is exacerbated by varying degrees of data-availability: many buildings, or even whole regions, may have incomplete data available. For instance, the heating system of a building may be completely unknown, which can have a large impact on the economics of a retrofit adoption incentivization strategy, as dicussed in the case study in this paper. Similarly, only distributional information about a certain feature may be known, e.g. income level of the homeowner based off of census data. This scenario of incomplete or probabilistic data does not have a clear resolution in a framework where a single building is assumed to be associated with a single prototypical archetype energy model template.

Another related major challenge that affects both building-scale accuracy and global-scale reach is data fusion: there is no authoritative source of truth on the state of the building stock in any one region, let alone across the world. Any such effort would necessarily require integrating a patchwork of GIS datasets. For instance, a model might consume from public datasets to extract fields like geometry and height for generating 2.5D building footprint based models, while consuming private or state-supplied tax parcel databases for information like building usage or age, and then consuming state-of-the-art research datasets for fields like computer-vision-extracted window-to-wall ratios. Deciding how to fuse these datasets, segment them into archetypes, and then create archetypal energy model templates for each segmented class is a non-trivial task. Furthermore, different regions may require entirely different energy models, with no universal isotropic building energy model which is applicable to the entire building stock achievable: the correct way to model a suburban home in Western Massachusetts might be drastically different from the correct way to model a commercial building in London or a naturally ventilated apartment building in Morocco.

The scope also necessarily requires integrating multiple stakeholders, none of whom necessarily have all of the knowledge, skills or data at hand necessary to complete the pipeline. GIS managers may have the data representing the building stock, but insufficient knowledge of what is relevant to the building energy modeling effort (or another analysis modality). Energy modelers may have a good sense of what each of the different energy models ought to look like given a particular building description, but not the ability to programmatically generate or execute them all. Research software engineers may have the skillset to programmatically generate templates or to orchestrate the execution of models, but not the requisite knowledge for how to interpret a given row of GIS data into a particular energy model.

To address these challenges attendant to building-scale accuracy and national-to-global reach, we propose a new approach to representing and organizing large datasets of real-world buildings: Semantic Stock Modeling (SSM). The key paradigm shift vis-a-vis traditional UBEMs is to dispose of the notion of archetypes, i.e. the association of a prototypical, pre-defined (excluding geometry) energy model with each building, and instead adopt a new organizational framework which consists of semantic building descriptions (which are agnostic as to their computational representation), paired with translation layers which interpret the semantics and compile energy models (or other modalities) and their attendant computation layers which execute models. While the lines between these two approaches is blurred in some sense - under the hood, many UBEM studies likely implement some form of description/translation/computation isolation - here, we hope to shift the vocabulary and organizational frameworks by more explicitly surfacing and decoupling these different layers. In so doing, the goal is to leverage the affordances of such isolation to enable the creation of more robust, detailed, and larger scale building stock energy models. Part of the motivation is to more rigorously define a framework for the interactions of GIS managers, building energy modelers, and research software engineers which can better organize the development and construction of national and global-scale building stock energy models which achieve ... (granularity? varying data availability? etc etc)

The term _semantic_ is chosen to emphasize the level of detail of information stored in the building description layer and re-inforce its conceptual isolation from the _syntactic_ interpretation within the translation and computation layers. This enforces a stricter separation of concerns between the semantic building descriptions and the syntactic energy models. Rather than storing detailed energy-model information, which might often be numerical value assignments or other energy-model specific components/information, e.g. `Window U-Value = 2.72 W/m2K, Window SHGC=0.8` or `Infiltration rate = 0.05 m3/s/m2 exposed exterior surface area`, this layer is meant to capture coarser, human-readable semantic information about a building, e.g. `Single Pane Window` or `Leaky envelope`. The translation layer implemented by any particular modeling engine is responsible for converting these high-level fields into whatever form necessary to execute its computation. In so doing, this separation of concerns also allows for better separation of responsibilities which can better take advantage of the different skillsets of the various process stakeholders: GIS managers are clearly responsible for the building description layer, while energy modelers are clearly responsible for defining the interpretation of fields by defining components and component mappings, and research software engineers are clearly responsible for the compilation and execution of models according to the transformations and component mappings defined by the energy modelers on top of the GIS data. A clear requirement of such a framework then is concise, clear interfaces between each layer: a layer must provide manifests of what information it requires, and what information it produces.

1. solution: decoupling buildings from energy models
   1. energy modelers escape curse of dimensionality through simpler atomistic component definitions and rule specifications
   1. allowing for probabilistic representation over semantic features

By separating concerns, we enable the model description and computation to become decoupled in separate layers, with the obvious consequence that the model compilation and computation layer can be swapped out for different performance/cost tradeoffs or different modalities of analysis altogether. For instance, a variety of approaches can be used to achieve the desired scale of results in a reasonable amount of time: on the one hand, using high horizontal capacity and whitebox physics-based models, i.e. distributed cloud computing with large volumes of parallel nodes with low-throughput per node, or on the other hand, high vertical capacity and blackbox machine-learned models of the same function, i.e. exteremely high-throughput on relatively few GPU-equipped compute nodes. Similarly, different analysis engines can be supported from the same building-stock description, from a Bayesian model of a retrofit uptake economics analysis to an hourly heat-risk analysis which is accurate at the building scale, both consuming the same source dataset. The isolation of the building stock descriptions from the analysis engines makes it (relatively) straightforward to support these different approaches simultaneously.

To illustrate the utility of such a framework, a case study is conducted which models approximately 2.3 million residential buildings in the state of Massachusetts while considering a variety of fields associated with each building, such as age, single-family vs multi-family, heating systems, and more. This model is then used to analyze the economic efficacy of heat-pump adoption for homeowners and illustrate how increasing the level of semantic knowledge about heating systems drastically changes the outlook for the state of Massachusetts.

In proposing Semantic Stock Modeling, our goal is not to immediately define a completed, universal standard for how building stock models should be represented, but rather to provide a new methodological framework which building stock modeling efforts can follow to better organize stakeholders and empower large scale data exchange and analysis.

## Lit Review

1. classic ubem papers
1. existing templating efforts
1. similar approaches which are not tied to real buildings: resstock - highly detailed, distributions, etc etc, but can't tell u what 123 Oak st's energy use is.

## Methodology

1. solution: decoupling buildings from energy models

   1. maintain stricter separation of concerns between semantic building descriptions and the syntactic energy models
   1. interpretation layers with clear interfaces compile energy models
   1. energy modelers escape curse of dimensionality through simpler atomistic component definitions and rule specifications
   1. allowing for probabilistic representation over semantic features

1. defining semantics
1. defining energy model components
1. defining translation layers
1. extensibility, e.g. going from age/(sfh|mfh) to age/(sfh|mfh)/(window type)

## Case Study

1. goal: how feasible is heat-pump adoption in MA given grid economics?
1. data sources
   1. overture data
   1. state tax assessor data
1. component descriptions
   1. age -> windows
   1. typology -> envelope
   1. typology,age -> insulation level, infiltration
   1. typology -> loads.epd/loads.lpd
1. unknowns
   1. heating system
   1. basement status
   1. attic status
1. assumptions
1. predictions with fuel assignment unknown
1. predictictions with fuel assignment unknown
1. ingest data from individual homeowners
1. total compute time, electricity, carbon?

# Discussion

1. Future work
   1. illustrating different ways of incorporating distributional data
   1. more rigorously defining the stock description/transformation/compilation manifests/standards.

## Conclusion
