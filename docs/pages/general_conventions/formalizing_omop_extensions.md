---
title: Formalizing OMOP CDM Extensions
keywords: extensions, standardization, working groups
last_updated: December 12, 2024
tags: [extensions, CDM]
summary: "Standardized workflow for developing extension tables to the OMOP Common Data Model that promote consistency, interoperability, and community adoption."
sidebar: mydoc_sidebar
permalink: /formalizing_omop_extensions.html
---

## Issue # and location


## Issue summary
As the OHDSI community expands into new clinical domains, there is a growing need for extension tables to the OMOP CDM to accommodate domain-specific observational health data that cannot be adequately represented in existing tables. To ensure these extensions maintain consistency with OMOP CDM conventions, integrate seamlessly with the OHDSI software ecosystem, and enable reproducible multi-site research, we need a formalized workflow for creating and disseminating extension tables. This convention establishes standardized processes for structure definition, semantic representation, and validation based on best practices from successful extensions like the Medical Imaging CDM developed by the OHDSI Medical Imaging Working Group.

## Convention type
Global

## CDM table
NA

## CDM field
NA

## Links to issue discussion
- Park WY, Jeon K, Schmidt TS, et al. Development of Medical Imaging Data Standardization for Imaging-Based Observational Research: OMOP Common Data Model Extension. Journal of Imaging Informatics in Medicine. 2024;37:899-908. https://doi.org/10.1007/s10278-024-00982-6

## Provenance of data
General, All

## The ratified convention

This convention establishes a standardized workflow for developing extension tables to the OMOP Common Data Model (CDM). Extension tables should be developed when existing OMOP CDM tables cannot adequately represent domain-specific observational health data. The development process consists of three primary components: (1) Structure Definition, (2) Semantic Representation, and (3) Validation and Documentation.

### 1. Structure Definition

#### 1.1 Adherence to OMOP CDM Conventions

All extension tables must follow established OMOP CDM conventions:

- **Person-centric design**: Link to the Person table via `person_id` and datetime fields
- **Domain specificity**: Each table should be limited to a specific domain
- **Minimal duplication**: Use foreign keys to existing clinical data tables
- **Standard field patterns**: Include `concept_id` and `type_concept_id` fields following OMOP conventions

#### 1.2 Table Specification Requirements

For each proposed extension table, provide the following detailed specifications:

**Table Purpose and Scope**
- Clear description of what the table represents
- Justification for why existing tables are insufficient
- Expected cardinality relationships with existing OMOP tables

**Field-Level Documentation**

Each field must be documented with:

| Component | Description | Example |
|-----------|-------------|---------|
| **Field Name** | Descriptive name following OMOP naming conventions | `image_occurrence_id` |
| **Required** | Whether field is mandatory (Yes/No) | Yes |
| **Data Type** | Specific data type with size constraints | `varchar(250)` |
| **Description** | Detailed explanation of field purpose and usage | "DICOM Study UID - globally unique identifier for imaging study" |
| **Vocabulary Binding** | Reference to standard vocabulary if applicable | DICOM, SNOMED, LOINC, etc. |
| **Foreign Key** | Relationship to other tables if applicable | Links to Concept table |

**Example Table Structure Template:**

```
Table: [TABLE_NAME]

Purpose: [Detailed description of table purpose]

Relationships:
- One-to-many with [TABLE_A]: [explanation]
- Many-to-one with [TABLE_B]: [explanation]

Field Specifications:
```

| Field | Required | Data Type | Description |
|-------|----------|-----------|-------------|
| [field_name] (PK/FK) | Yes/No | integer | [detailed description] |

#### 1.3 Integration Documentation

Document how extension tables integrate with existing OMOP CDM:

- **Foreign key relationships**: Explicit mapping to Person, Visit_occurrence, Provider, etc.
- **Clinical domain table integration**: How extension data relates to Measurement, Observation, Condition_occurrence, etc.
- **Temporal relationships**: How datetime fields enable temporal queries
- **Episode tracking**: If applicable, document use of Episode and Episode_event tables for longitudinal tracking

#### 1.4 Cardinality Specifications

Clearly define relationships between tables:

- One-to-one relationships
- One-to-many relationships
- Many-to-many relationships (with junction tables if needed)

**Example from Medical Imaging WG:**
- Image_occurrence has many-to-many relationship with Procedure_occurrence (multiple CPT codes can link to multiple DICOM series)
- Image_feature has one-to-many relationship with Image_occurrence (one image series may produce multiple findings)
- Image_feature has one-to-one relationship with clinical domain tables (one row in Image_feature links to one row in Measurement table)

### 2. Semantic Representation

#### 2.1 Vocabulary Requirements

Extension tables must leverage standardized vocabularies for semantic interoperability:

**Standard Vocabularies**
- **SNOMED CT**: Clinical findings, procedures, anatomical locations
- **LOINC**: Laboratory and clinical measurements
- **RxNorm**: Medications and drug products

**Domain-Specific Vocabularies**

When standard vocabularies are insufficient, document the need for specialized vocabularies:

**Example from Medical Imaging WG:**
- **DICOM**: Properties of image acquisition, modality types, value sets
- **RadLex**: Radiological findings not adequately covered by SNOMED CT (e.g., "ground glass," "lobular" patterns)

#### 2.2 Custom Vocabulary Development

When domain-specific concepts are not available in standard vocabularies, develop custom vocabularies using the Custom Vocabulary Builder (CVB) framework or similar standardized approaches:

**CVB Workflow for Extension Vocabularies**

1. **Collaborative Validation**
   - Use collaborative interfaces (e.g., Google Sheets) for domain expert review
   - Include clinicians, ontologists, and subject matter experts
   - Document mapping decisions and rationale

2. **SSSOM Compliance**
   - Structure mappings using Simple Standard for Sharing Ontology Mappings (SSSOM)
   - Include source-to-standard mappings with relationship identifiers
   - Capture mapping confidence levels

3. **Automated Pipeline**
   - Continuous integration from source mappings to delta vocabulary tables
   - Version control via GitHub for provenance tracking
   - Automated testing against standard vocabularies

4. **Delta Vocabulary Output**
   - Generate CONCEPT, CONCEPT_RELATIONSHIP, CONCEPT_ANCESTOR tables
   - Assign two billion range concept IDs for custom concepts
   - Include mapping metadata CSV for provenance
   - Create version-controlled release tags

**Vocabulary Documentation Requirements**

For each custom vocabulary, provide:

- **Vocabulary ID and Name**: Unique identifier and descriptive name
- **Version**: Semantic versioning (e.g., v1.0.0)
- **Concept Class**: Classification of concept types
- **Domain**: OMOP domain assignment
- **Mappings**: Relationships to standard vocabularies
- **Provenance**: Who created mappings, validation method, confidence level
- **Repository Location**: GitHub repository with delta vocabulary tables

#### 2.3 Concept Type Definitions

Define `type_concept_id` values to capture provenance:

**Example from Medical Imaging WG:**
- Algorithm-derived features
- DICOM structured reports
- Manual annotations
- EHR-derived values

This enables tracking the source and method used to generate each data point.

### 3. Examples and Validation

#### 3.1 Use Case Documentation

Provide detailed use cases demonstrating:

**Clinical Scenario**
- Research question or clinical need
- Target patient population
- Required data elements

**Example Data Flow**
- How source data enters extension tables
- Integration with existing OMOP tables
- Query patterns for common analyses

**Template for Use Case Documentation:**

```markdown
### Use Case: [Title]

**Objective**: [Research question or clinical goal]

**Scenario**: [Detailed clinical scenario description]

**Data Requirements**:
- Extension table data: [specific fields needed]
- Related OMOP tables: [which standard tables are queried]
- Temporal constraints: [time-based criteria]

**Implementation**:
1. [Step-by-step data flow]
2. [Table population logic]
3. [Example queries]

**Results**: [Expected outcome or findings structure]
```

#### 3.2 Synthetic Data Examples

Provide synthetic data demonstrating:

1. **Single Record Example**
   - Complete row for each extension table
   - All required and optional fields populated
   - Realistic values following data type constraints
   - Foreign key relationships clearly indicated

2. **Multi-Record Scenario**
   - Longitudinal tracking (if applicable)
   - Multiple related records across tables
   - Demonstration of one-to-many and many-to-many relationships

3. **Edge Cases**
   - Null/optional field handling
   - Maximum cardinality scenarios
   - Complex relationship examples

#### 3.3 Visualization

Include diagrams showing:

- **Entity-Relationship Diagrams**: Table relationships with cardinality
- **Data Flow Diagrams**: How data moves from source systems to extension tables
- **Integration Diagrams**: How extension tables connect to existing OMOP CDM
- **Use Case Workflows**: Step-by-step process flows for clinical scenarios

**Example from Medical Imaging WG:**
- Comprehensive ER diagram showing Image_occurrence and Image_feature integration
- Longitudinal tracking diagram using Episode tables
- Detailed example with lung nodule tracking across multiple visits

#### 3.4 Provenance Documentation

Document algorithm and data provenance:

- **Algorithm Identification**: URI or unique identifier for processing algorithms
- **Version Information**: Algorithm version used to generate features
- **Execution Metadata**: Timestamp, parameters, configuration
- **Validation Metrics**: When applicable, include algorithm performance metrics

This enables reproducibility and traceability of computationally-derived data.

### 4. Extension Review and Approval Process

#### 4.1 Working Group Review

1. **Initial Proposal**: Present extension rationale and preliminary design to relevant OHDSI working group
2. **Feedback Incorporation**: Address community feedback on structure and semantics
3. **Convention Alignment**: Verify adherence to OMOP CDM conventions
4. **Use Case Validation**: Demonstrate extension utility with real-world scenarios

#### 4.2 Documentation Completeness

Ensure the following documentation is complete:

- [ ] Detailed table structure specifications
- [ ] Field-level documentation with data types and descriptions
- [ ] Vocabulary mappings and custom vocabulary documentation
- [ ] Integration points with existing OMOP CDM tables
- [ ] At least one detailed use case with example data
- [ ] Synthetic data examples
- [ ] Entity-relationship diagrams
- [ ] Provenance tracking methodology

#### 4.3 Reference Implementation

Develop a reference implementation including:

- SQL DDL (Data Definition Language) scripts for table creation
- ETL (Extract, Transform, Load) examples from source data
- Sample queries demonstrating common use cases
- Data quality checks and constraints
- Performance considerations and indexing recommendations

### 5. Maintenance and Versioning

#### 5.1 Version Control

Extension tables should follow semantic versioning:

- **Major version**: Breaking changes to table structure
- **Minor version**: Backward-compatible additions
- **Patch version**: Bug fixes and clarifications

#### 5.2 Evolution Path

Document migration path for:

- Schema updates
- Vocabulary additions
- Deprecated fields
- Backward compatibility considerations

#### 5.3 Community Engagement

Establish mechanisms for:

- User feedback collection
- Enhancement requests
- Bug reporting
- Community contributions to vocabularies

### 6. Integration with OHDSI Ecosystem

#### Extension Support in OHDSI Tools

The OHDSI community is developing a comprehensive set of R packages to integrate extension tables seamlessly with the core OHDSI software ecosystem. These tools enable researchers to leverage extension tables in the same manner as core OMOP CDM tables.

**FeatureExtractionForExtensions**

One key package under development is **FeatureExtractionForExtensions** (available at https://github.com/OHDSI/FeatureExtractionForExtensions), which extends the popular FeatureExtraction package to support:

- Automated feature generation from extension tables
- Integration with cohort definitions
- Temporal feature extraction across extension and core tables
- Standardized aggregation functions for domain-specific features

#### Requirements for Tool Support

**To ensure extension tables can be supported by OHDSI ecosystem packages, extensions must follow the conventions outlined in this document, including:**

1. **Structural conventions**: Person-centric design with standard foreign keys
2. **Semantic conventions**: Proper use of concept_id and type_concept_id fields
3. **Vocabulary compliance**: Standard or well-documented custom vocabularies
4. **Provenance tracking**: Clear documentation of data sources and derivation methods
5. **Complete documentation**: Field specifications, relationships, and use cases

Extensions that adhere to these standards will be eligible for integration with OHDSI ecosystem tools, enabling broader adoption and standardized analytics across the community.

### 7. Summary

This formalized workflow ensures that OMOP CDM extensions maintain consistency, interoperability, and alignment with OHDSI community standards. By following these guidelines, extension developers can create robust, well-documented table structures that integrate seamlessly with existing OMOP infrastructure and tools.

The success of the Medical Imaging CDM extension demonstrates that following this structured approach enables:

- Rapid community adoption
- Reproducible research across institutions
- Integration with federated data networks
- Expansion of observational research into new domains

As the OHDSI community continues to expand into new clinical domains, this workflow provides a foundation for sustainable, standardized extension development.

## Date of ratification/published
TBD

## Downstream implications
Yes - Extensions developed following this convention will be compatible with OHDSI ecosystem tools including FeatureExtractionForExtensions and other packages designed to support extension tables.

## Link to DQD check
NA

## Related conventions/further information

- [OMOP Common Data Model Conventions](https://ohdsi.github.io/CommonDataModel/dataModelConventions.html)
- [Custom Vocabulary Builder (CVB)](https://github.com/TuftsCTSI/CVB)
- [FeatureExtractionForExtensions](https://github.com/OHDSI/FeatureExtractionForExtensions)
- Park WY, Jeon K, Schmidt TS, et al. Development of Medical Imaging Data Standardization for Imaging-Based Observational Research: OMOP Common Data Model Extension. Journal of Imaging Informatics in Medicine. 2024;37:899-908.
