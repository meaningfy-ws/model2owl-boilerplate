# model2owl-boilerplate
Boilerplate for running model2owl on new projects

📖 **Generated ReSpec documentation:** <https://meaningfy-ws.github.io/model2owl-boilerplate/>

# Getting started
This project will use model2owl to transform a UML model into a formal OWL ontology, a SHACL shape, a conventions report
and glossary  based on established UML conventions.

Main steps:
* Fork this repository or make a new branch from main
* Put your UML model/models export (XML file) in the implementation folder
* Configure model2owl using config folder
* Modules are auto-detected from the implementation folder

> **Note:**  
> If the branching option is used, the branch will not be merged into `master`. 
> It is recommended to delete the branch once the desired output is generated and the work is complete.
# Usage
## Naming conventions
* The name of the created folders should not contain spaces. It can contain underscores or hyphen if it's strictly necessary 
* The name of the UML model export file should match its folder name (i.e mymodel.xml)
## Folder structure conventions
To add a new UML model follow this folder structure
```
implementation
        |___firstModel
                |___model2owl-config
                |___xmi_conceptual_model
                |___respec_resources
```
## Adding a UML model
* Create a folder under implementation with the name of the UML model following the naming conventions. 
* Create xmi_conceptual_model folder inside the folder created at the previous step
* Put the UML export in the xmi_conceptual_model folder following the naming conventions
## Adding model2owl config
* Copy model2owl-config folder into UML model implementation folder created at the previous step
* Configure model2owl using the files inside model2owl-config folder
### Configuration Files

As presented above, this validator will use a maximum of three configuration files, depending on the UML model validation option you have selected (check the **Validator Options** section).

#### Config Parameters File (config-parameters.xsl)

This is an XSLT file that contains a set of variables used by the system during model validation. Some variables can be modified, while others should remain unchanged as they are preconfigured. A boilerplate for the config parameters can be found [here](#). The boilerplate includes comments to guide what can and cannot be changed.

**Sample:**

```xml
<!-- Types of elements and names for attribute types that are acceptable to produce object properties -->
<xsl:variable name="acceptableTypesForObjectProperties" select="('epo:Identifier', 'rdfs:Literal')"/>

<!-- Acceptable stereotypes -->
<xsl:variable name="stereotypeValidOnAttributes" select="()"/>
<!-- ... other variables ... -->

<!-- Allowed characters for a normalized string (characters allowed in a QName) -->
<xsl:variable name="allowedStrings" select="'^[\w\d-_:]+$'"/>
```
There are three types of data you can pass into the variables: lists, strings, or booleans.  
- For **lists**, the variable value should be enclosed in double quotes (`""`), with individual values wrapped in single quotes (`''`) and separated by commas.  
- For **strings** and **booleans**, the value should always be enclosed in double quotes (`""`).

**Example:**
```xml
String variable 
<xsl:variable name="my-variable" select="'my string value'"/>
List variable
<xsl:variable name="stereotypeValidOnDependencies" select="('Disjoint', 'disjoint', 'join')"/>
Boolean variable
<xsl:variable name="enableGenerationOfSkosConcept" select="fn:false()"/>
```
**Notes:**
- **Do not delete variables**: Each variable serves a purpose in the generation process.
- **Maintain variable types**: Do not change the type of a variable (e.g., from a list to a string). If a variable is originally set as a list, it must remain a list.
- If a variable is unnecessary or if you prefer not to impose restrictions, leave the variable with an empty list or string. See the examples below:
  - **Empty string variable**:
  
    ```xml
    <xsl:variable name="my-variable" select="''"/>
    ```

  - **Empty list variable**:

    ```xml
    <xsl:variable name="stereotypeValidOnAttributes" select="()"/>
    ```
    
#### Namespaces File (namespaces.xml)

This file holds the namespace values and names used in the model being processed.
Sample:

```xml

<?xml version="1.0" encoding="UTF-8"?>
<prefixes xmlns="http://publications.europa.eu/ns/">
   <prefix name="" value="http://data.europa.eu/a4g/ontology#"/>
    <prefix name="foaf" value="http://xmlns.com/foaf/0.1/"/>
   <!-- ... other prefixes ... -->
</prefixes>
```
**Notes:**
- If you want any URI from this list to be imported as statements (`owl:imports`) in the generated OWL and SHACL artefacts, define it in the imports.xml file (as described in the [Imported ontologies configuration](https://github.com/meaningfy-ws/model2owl/tree/develop?tab=readme-ov-file#imported-ontologies-configuration)).

#### XSD and RDF Datatypes File (xsdAndRdfDataTypes.xml)

This file contains declarations of XSD and RDF datatypes. 

**Sample:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<datatypes xmlns="http://publications.europa.eu/ns/">
   <datatype namespace="xsd" qname="xsd:anyURI"/>
   <datatype namespace="xsd" qname="xsd:base64Binary"/>
   <datatype namespace="xsd" qname="xsd:boolean"/>
   <datatype namespace="xsd" qname="xsd:byte"/>
   <datatype namespace="xsd" qname="xsd:date"/>
   <datatype namespace="xsd" qname="xsd:dateTime"/>
   <datatype namespace="rdfs" qname="rdfs:Literal"/>
</datatypes>
```
#### UML to XSD DataTypes File (umlToXsdDataTypes.xml)

This file defines the mappings between UML datatypes and XSD datatypes.
If this in not necessary leave the default file.
### Sample:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<mappings xmlns="http://publications.europa.eu/ns/">

    <!-- epo prefixed datatypes -->
    <mapping>
        <from qname="epo:Indicator"/>
        <to qname="xsd:boolean"/>
    </mapping>
    <mapping>
        <from qname="epo:Date"/>
        <to qname="xsd:date"/>
    </mapping>
    <mapping>
        <from qname="epo:DateTime"/>
        <to qname="xsd:dateTime"/>
    </mapping>
</mappings>
```
#### Metadata File (metadata.json)

This file contains metadata information used for generating documentation, convention report, and ReSpec documentation. It defines titles, descriptions, versioning, contributors, and other publication details for the ontology.

**Sample:**

```json
{
    "metadata": {
        "conventionReportAuthor": "Your Organization",
        "conventionReportUMLModelName": "Your Ontology Name",
        "ontologyTitleCore": "Your Ontology Core",
        "feedbackUrl": "https://github.com/your-org/your-ontology/issues",
        "license": "CC BY 4.0",
        "repositoryUrl": "https://github.com/your-org/your-ontology",
        "status": "Draft",
        "title": "Your Ontology Documentation"
    },
    "customMetadata": {
        "metadataSectionProperties": [
            {
                "key": "Related documentation",
                "data": [
                    {
                        "value": "User Guide",
                        "href": "https://your-organization.org/user-guide"
                    }
                ]
            }
        ]
    }
}
```



**Notes:**
- This file is used by all model2owl artefacts including ReSpec documentation generation
- The custom metadata (`customMetadata`) section allow you to add additional documentation links and information
- The `customMetadata.metadataSectionProperties` property can be used to introduce any custom metadata needed by the user to be available in the [main](./implementation/demo_ontology/respec_resources/templates/main.j2) template
- The `metadata.projectLocalResources` property allows to define project files available in the repository that should be displayed in the _Project resources_ section of the generated ReSpec documentation
- The `metadata.projectLocalResources.path` property must specify a path **relative to the module** that contains the configuration file. For instance, in the demo_ontology module, when editing `implementation/demo_ontology/model2owl-config/metadata.json`, the `OWL core resource` is referenced as `owl_ontology/demo_ontology.rdf`. Additional examples can be found in the [metadata.json](./implementation/demo_ontology/model2owl-config/metadata.json) file.


## Adjust GitHub actions
Two GitHub Action scripts are located in the [.github](./.github) directory:
 * [transform_with_model2owl.yml](.github/workflows/transform_with_model2owl.yml)
   is used to transform the UML model/models into set of supported artefacts.
 * [diff-combined.yml](.github/workflows/diff-combined.yml) is used to compute a
   difference between two versions of RDF artefacts and generate
   machine-readable (JSON) and human-readable (AsciiDoc) reports.

### Transform with model2owl

The workflow triggers on changes to any XMI file under the implementation directory:
```yaml
    paths:
      - "implementation/*/xmi_conceptual_model/*.xml"
```
Modules are auto-detected from the changed files. On manual dispatch, all modules are processed.

```
Example:

implementation
        |___modelOne
        |___modelTwo

Both models will be auto-detected and processed by the workflow.
```

### RDF Diffing
The workflow uses the new model2owl CLI commands for calculating diffs and
generating reports. Further information about the commands can be found in [the
model2owl project's README](https://github.com/meaningfy-ws/model2owl/blob/develop/README.md#generating-diff-reports).

New version of model files must be committed to the repository where the workflow is
defined. The workflow allows comparing the files from the current revision with
one committed in a previous revision or even in a different repository.

The RDF diff workflow is called automatically by the transform workflow after
generating new OWL/SHACL artefacts. It can also be triggered manually via
[workflow dispatch](https://docs.github.com/en/actions/managing-workflow-runs-and-deployments/managing-workflow-runs/manually-running-a-workflow).

Configuration is managed through [diff-config.env](.github/workflows/diff-config.env).
This file allows you to override the default RDF diff behaviour on push-triggered runs,
such as which revision to compare against or which modules to include.
Values can also be set as [GitHub Variables](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-variables#creating-configuration-variables-for-a-repository)
or passed as workflow inputs. Priority: workflow inputs > config file > repository variables > defaults.

#### Workflow configuration parameters
The following list describes available configuration parameters:

- `REVISION_TO_COMPARE_COMMIT_ID` - (Optional) Git revision to compare against (branch, tag or SHA, default: `develop`)
- `REVISION_TO_COMPARE_REPO_URL` - (Optional) URL of an external public Git repository for the old version
- `OLD_ONTOLOGY_DIR` - Root directory for old OWL/SHACL files (default: `implementation`)
- `NEW_ONTOLOGY_DIR` - Root directory for new OWL/SHACL files (default: `implementation`)
- `MODULES` - Comma-separated module names to diff (empty = auto-detect)
- `RDF_DIFF_OUTDIR` - Output directory for generated diff reports (default: `diff-reports`)

Diff reports are stored in `<RDF_DIFF_OUTDIR>/<module>/` with separate subdirectories for each module.

File paths are derived automatically from the module name and directory configuration
(e.g., `<OLD_ONTOLOGY_DIR>/<module>/owl_ontology/<module>.ttl`). The old version is
resolved based on `REVISION_TO_COMPARE_COMMIT_ID` and `REVISION_TO_COMPARE_REPO_URL`:
- Both specified: files are fetched from the external repository at the given revision
- Only revision specified: files are fetched from the same repository at the given revision
- Neither specified: files are fetched from the current revision

## Output
The output is automatically generated by the GitHub action scripts described previously. Each of the scripts will 
do an automatic commit on the branch that was executed from. To see the output executing a git pull after the GitHub 
action ran successfully is mandatory.
The scripts will generate automatically folders and transformation files under a specific structure that is presented
below.

### Output folders structure and content

Glossaries will be stored at the top level of this project outside the implementation folder, and it will 
contain the individual glossaries for the UML model and a unified glossary if there are more than one UML model to
be processed by GitHub action scripts

```
     /
    .github
    glossary
        |__static  -> folder to hold css and js neccesary for the glossary
        |__modelOne_glossary.html
        |__modelOne_glossary.adoc
        |__modelOne_glossary.adoc.html*
        |__modelTwo_glossary.html
        |__modelTwo_glossary.adoc
        |__modelTwo_glossary.adoc.html*
        |__ontologies_combined_glossary.html        -> combined glossary
        |__ontologies_combined_glossary.adoc        -> combined glossary
        |__ontologies_combined_glossary.adoc.html*   -> combined glossary
    implementation
    model2owl-config
```
Note: The `*.html` files relate to the former glossary, while the `*.adoc` and `*.adoc.html` files represent the new glossary. The new version overlaps significantly with the old one but includes minor enhancements, such as deduplicated term definitions in the _definition_ columns.

_* The `*.adoc.html` file is different from `*.html` as it's generated from `*.adoc` sources for demonstration purposes only._

The formal OWL ontology and a SHACL shape will be inside each UML model folder under specific folders as described 
below.
```
    implementation
        |___modelOne
                |__conventions_report
                |       |__static -> folder to hold css and js neccesary for the convention report
                |       |__modelOne-convention-report.html
                |__owl_ontology
                |       |__modelOne_core.rdf
                |       |__modelOne_core.ttl
                |       |__modelOne_restrictions.rdf
                |       |__modelOne_restrictions.ttl
                |__shacl_shapes
                |       |__modelOne_shapes.rdf
                |       |__modelOne_shapes.ttl
                |___model2owl-config
                |___xmi_conceptual_model
        |___modelTwo
```

Diffing reports are stored in a directory specified by the user in
`RDF_DIFF_OUTDIR` config parameter stored as [GitHub
Variable](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-variables#creating-configuration-variables-for-a-repository).
The parameter defaults to a top-level `diff-reports` directory. 

The default behaviour of the diffing feature is to store a single set of diffing
reports at a time, but this can be changed by the user by adjusting the
`RDF_DIFF_OUTDIR` path to store reports for subsequent GitHub Action runs in a
different directory (or a subdirectory).

Note that regardless of the chosen directory structure for the output, any set
of diffing reports can be accessed by inspecting past Git revisions. Identifying
a report’s sources is made easier by the information encoded in the commit
message.

### Commit messages for automatically generated reports
The workflows commit generated files with messages following conventional commits format:
```
chore(ci): transform 2 module(s) #143 [skip ci]
chore(ci): diff 2 module(s) #143 [skip ci]
```
The message includes the number of modules processed and the workflow run number.
`[skip ci]` prevents the commit from retriggering the workflow.

### Workflow summary
Each workflow run generates a summary visible in the GitHub Actions run page. The summary
shows which modules were processed, with links to the generated diff reports. If a module
fails (e.g., no preexisting files to compare to), it will be noted in the summary.

### Troubleshooting failed CI runs
When a transform/diff/pages run fails, the [`debugging-ci-runs`](.claude/skills/debugging-ci-runs/SKILL.md)
Claude Code skill describes how to inspect the run with `gh`, filter the logs down to the real
error, classify the failure, and fix it iteratively. **Important:** on a (partially) successful
run the workflows commit generated artefacts back to the branch, so always `git pull` before
editing, committing, or rebasing.

## ReSpec Documentation Generation

The workflow also generates **ReSpec documentation** - a comprehensive HTML documentation package that includes your ontology artifacts and examples. This section explains how to customize and work with ReSpec resources.

### Understanding ReSpec Directories

It's important to distinguish between the **input** and **output** directories:

- **`respec_resources/`** (input directory): Contains your customization files, templates, and assets
- **`respec/`** (output directory): Contains the generated HTML documentation package

### ReSpec Resources Structure

To customize your ReSpec documentation, create a `respec_resources` folder in your implementation directory:

```
implementation
    |___yourModel
            |___respec_resources          <- INPUT directory (your customizations)
            |       |___templates
            |       |       |___main.j2           <- Your main template (extends base.j2)
            |       |       |___appendix.j2       <- Optional appendix template
            |       |       |___main-demo.j2      <- Optional demo template
            |       |___assets
            |               |___img               <- Images for your documentation
            |               |___examples          <- Example files (JSON-LD, TTL, etc.)
            |                       |___example1.jsonld
            |                       |___example2.ttl
            |___respec                    <- OUTPUT directory (generated documentation)
                    |___index.html        <- Final HTML documentation
                    |___sds               <- Semantic artifacts (OWL, SHACL, JSON-LD)
                    |___assets            <- Copied assets and examples
```

### Template System

ReSpec uses **Jinja2 templating** with a hierarchical template structure:

#### Base Template (`base.j2`)
The foundation template that provides:
- HTML structure and metadata
- CSS and JavaScript includes  
- Navigation and layout
- Standard macros and functions

> **Note:** The `base.j2` template is provided by Model2OWL and contains the core functionality. 
> For detailed information about available variables and macros, see the [Model2OWL README](https://github.com/meaningfy-ws/model2owl/blob/develop/README.md).

#### Main Template (`main.j2`)
Your customizable template that **extends** `base.j2`:

{% raw %}
```jinja2
{% extends "base.j2" %}

{% block content %}
<section id="introduction">
    <h2>Introduction</h2>
    <p>Your custom content here...</p>
    
    <!-- Include examples -->
    <div class="example" id="example1">
        <div class="example-title marker">Example 1: Basic Usage</div>
        <!-- Example content will be loaded from assets/examples/ -->
    </div>
</section>
{% endblock %}
```
{% endraw %}

### Customizing Your Documentation

#### 1. Start with the Example
Copy the `respec_resources` folder from an existing implementation (like `demo_ontology`) as your starting point:

```bash
cp -r ./respec_resources_example implementation/yourModel/
```

#### 2. Edit the Main Template
Modify `respec_resources/templates/main.j2` to:
- Add your custom sections and content
- Include examples using the example system
- Customize the documentation structure

#### 3. Add Assets and Examples
Place your assets in `respec_resources/assets/`:
- **Images**: `assets/img/` - Screenshots, diagrams, logos
- **Examples**: `assets/examples/` - JSON-LD, Turtle, XML examples that demonstrate your ontology usage
- **SHACL Shapes**: `assets/shacl/` - SHACL validation shapes files (automatically copied from generated artifacts)
  - `ontology_shapes.ttl` - Main ontology SHACL shapes (Turtle format, universal name)
  - `ontology_shapes.jsonld` - JSON-LD context validation shapes (JSON-LD format, following DCAT-AP approach)

#### 4. Example Integration
The ReSpec system automatically processes files in `assets/examples/`:
  - Files are made available in the final documentation
  - JavaScript automatically creates tabbed interfaces for examples with:
    - **Copy** button allow users to copy examples to clipboard
    - **Validate** button perform comprehensive SHACL validation with detailed reports
        - **SHACL Validation**: Uses ITB (Interoperability Testbed) validation service
        - **Reports**: 
            - **Success Reports**: Shows validation success with warnings if any
            - **Failure Reports**: Detailed violation reports with focus nodes and paths
    - **Open in Playground** button opens JSON-LD examples in the JSON-LD Playground



#### Mandatory SHACL Shapes Files for Validation

To enable the **Validate** functionality for your examples, you must provide two SHACL shapes files with the exact names as specified below. These files are required for the validation service to work correctly:

- **`ontology_shapes.ttl`**  
  - **Purpose:** Used for validating Turtle (RDF) examples.
  - **Location:** Place this file in `respec_resources/assets/shacl/`.
  - **Naming:** The file **must** be named  `ontology_shapes.ttl`.

- **`ontology_shapes.jsonld`**  
  - **Purpose:** Used for validating JSON-LD examples.
  - **Location:** Place this file in `respec_resources/assets/shacl/`.
  - **Naming:** The file **must** be named  `ontology_shapes.jsonld`.

> **Note:**  
> - The validation buttons in the documentation examples will not work unless both files are present and named as above.
> - The system automatically selects the appropriate shapes file based on the example's format (Turtle or JSON-LD).

**Summary Table:**

| Example Format | Required SHACL File         | File Name                | Location                                 |
|:--------------:|:---------------------------|:------------------------|:-----------------------------------------|
| Turtle         | Ontology SHACL shapes      | `ontology_shapes.ttl`   | `respec_resources/assets/shacl/`         |
| JSON-LD        | Ontology SHACL shapes       | `ontology_shapes.jsonld` | `respec_resources/assets/shacl/`         |

If you rename or omit these files, validation will fail and users will see an error message.




**Validation Report Format**:
```
Validation Result - SUCCESS
PREFIX vs:             
PREFIX wdrs:           
PREFIX wdsr:           
PREFIX xhv:            
PREFIX xml:            
PREFIX xsd:            

[ rdf:type     sh:ValidationReport;
  sh:conforms  true
] .
```
You can add interactive examples anywhere in your documentation by inserting a `<div>` element with the class `h3 examples`. The `id` attribute of this `<div>` should match the file name (without extension) of your example. For example, to include an example from `example1.ttl` and `example1.jsonld`, use:

Example in your template:
```jinja2
       <div class="h3 examples" id="example1">Example 1</div>
```

### Metadata Configuration

ReSpec documentation uses metadata from `model2owl-config/metadata.json`:

```json
{
    "title": "Your Ontology Documentation",
    "description": "Comprehensive documentation for your ontology",
    "version": "1.0.0",
    "authors": [
        {"name": "Your Name", "email": "your.email@example.com"}
    ]
}
```

### Generated Output

The workflow generates a complete documentation package in the `respec/` directory:
- **`index.html`**: Main documentation page
- **`sds/`**: All semantic artifacts (OWL, SHACL, JSON-LD context files)
- **`assets/`**: Your images and examples
- **Static resources**: CSS, JavaScript for functionality

### GitHub Pages Integration

The generated ReSpec documentation is automatically published to GitHub Pages, creating a documentation website for your ontology that includes:
- Interactive examples
- Downloadable artifacts
- Search functionality


