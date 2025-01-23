---
title: Workflows Form Inputs Library
description: Instructions to build the UI of individual workflows and all the types of inputs possible in Workflows
sidebar_position: 2
sidebar_label: Workflows Form Inputs Library
redirect_from:
  - /docs/internal-developer-portal/flows/custom-extensions
  - /docs/internal-developer-portal/flows/flows-input
---

## Introduction

Workflows allow users to provide input parameters that drive the execution of templates. A well-designed form ensures a smooth user experience by offering the right input types and validation mechanisms. Inputs can be categorized into static inputs, where users manually enter values, and dynamic inputs, which fetch or derive values based on context.

Below are the different ways you can design form inputs in IDP workflows:

#### Static Inputs

These inputs require users to enter predefined values manually.

- `string` – Single-line text input
- `textarea` – Multi-line text input
- `number` – Numeric input
- `boolean` – Checkbox (true/false)
- `enum` – Dropdown selection from a predefined list
- `array` – List of values (e.g., strings, numbers)
- `object` – Key-value pair inputs
- `password` – Masked input for sensitive values

#### Dynamic Inputs

These inputs fetch values dynamically based on external data sources or runtime context.

1. [Standard Workflow UI Picker](/docs/internal-developer-portal/flows/flows-input#workflow-ui-pickers)
    - `Entity Picker` – Select an entity from the catalog
    - `Owner Picker` – Select a user or group
    - `Repository Picker` – Choose a repository from a version control provider

2. [API Based Dynamic Workflow UI Picker](/docs/internal-developer-portal/flows/dynamic-picker)

    - `Dynamic API Picker` – Fetch options dynamically via an API request
    - `Autocomplete Fields` – Suggestions based on previous inputs or external data fetched using Dynamic API Picker. 



## Input Examples

### Simple text input

#### Simple input with basic validations

Basic form inputs allow users to provide structured data while ensuring it meets specific requirements. You can define constraints such as character limits, patterns, and UI hints to guide users effectively.

Example [`workflows.yaml`](https://github.com/harness-community/idp-samples/blob/main/workflow-examples/text-input-pattern.yaml)

This example demonstrates a simple text input field with:

- A `title` and `description` for clarity.
- A **maximum length constraint** (`maxLength: 8`).
- A r**egex pattern validation** (`pattern`) to enforce naming rules.
- **UI enhancements** like autofocus and helper text.

<details>
<summary>Example YAML</summary>

```YAML
parameters:
  - title: Fill in some steps
    properties:
      name:
        title: Simple text input
        type: string
        description: Description about input
        maxLength: 8
        pattern: "^([a-zA-Z][a-zA-Z0-9]*)(-[a-zA-Z0-9]+)*$"
        ui:autofocus: true
        ui:help: "Hint: additional description..."
```

</details>

![](./static/workflows-pattern.png)

#### Multi-line text input

Multi-line text inputs are useful for capturing larger blocks of text, such as descriptions, configuration snippets, or scripts. This example demonstrates how to use a textarea widget to enable multi-line input, with additional UI options for better usability.

Example [`workflows.yaml`](https://github.com/harness-community/idp-samples/blob/main/workflow-examples/multi-line-input.yaml)

This configuration includes:

- A textarea widget (`ui:widget: textarea`) for multi-line input.
- Custom row height (`ui:options: rows: 10`) for better visibility.
- A **placeholder example** showcasing a shell script.
- **Helper text** (`ui:help`) to guide users.

<details>
<summary>Example YAML</summary>

```YAMl
parameters:
  - title: Fill in some steps
    properties:
      multiline:
        title: Text area input
        type: string
        description: Insert your multi line string
        ui:widget: textarea
        ui:options:
          rows: 10
        ui:help: 'Hint: Make it strong!'
        ui:placeholder: |
          a=50
          b=60
          sh << word
          > echo "Equation: a + b = 110"
          > echo $(($a + $b))
          > echo ""
          > echo "Inside Here Tag, Assignment c=110"
          > c=`expr $a + $b`
          > echo $c
          > word
```

</details>

![](./static/workflows-multiline.png)


### Array options

Array inputs allow users to provide multiple values, either as strings, numbers, or complex objects. These can be structured to ensure uniqueness, predefined options, or flexible custom objects.

#### Array with strings

Example [`workflows.yaml`](https://github.com/harness-community/idp-samples/blob/main/workflow-examples/arrays.yaml)

#### Array with distinct values

Values mentioned under `enum` needs to be distinct, duplicate values aren't allowed under `enum`.

<details>
<summary>Example YAML</summary>

```YAML
parameters:
  - title: Fill in some steps
    required:
      - name
    properties:
      name:
        title: Name
        type: string
        description: Unique name of the component
      volume:
        title: Volume Type
        type: string
        description: The volume type to be used
        default: 'Cold HDD'
        enum:
          - 'Provisioned IOPS'
          - 'Cold HDD'
          - 'Throughput Optimized HDD'
          - 'Magnetic'
```

</details>

![Arrays With Distinct Values](./static/arrays-distinct-values.png)

#### Array with duplicate values

Allows multiple values, including duplicates, by using `enumNames` to provide user-friendly labels.

<details>
<summary>Example YAML</summary>

```YAML
parameters:
  - title: Fill in some steps
    properties:
      volume_type:
        title: Volume Type
        type: string
        description: The volume type to be used
        default: gp2
        enum:
          - gp2
          - gp3
          - io1
          - io2
          - sc1
          - st1
          - standard
        enumNames:
          - 'General Purpose SSD (gp2)'
          - 'General Purpose SSD (gp3)'
          - 'Provisioned IOPS (io1)'
          - 'Provisioned IOPS (io2)'
          - 'Cold HDD (sc1)'
          - 'Throughput Optimized HDD (st1)'
          - 'Magnetic (standard)'
```

</details>

![Arrays With Duplicate Values](./static/arrays-duplicate-values.png)

#### A multiple choices list with checkboxes

Users can select multiple predefined values from checkboxes.

Key Features:
- Supports **multiple selections**
- Uses **checkbox UI** for easy selection
- Ensures **unique selections** with `uniqueItems: true`

Example [`workflows.yaml`](https://github.com/harness-community/idp-samples/blob/5140ef7993a3c932c49af9162562a99e16428080/workflow-examples/multi-choice-list.yaml#L24-L34)

<details>
<summary>Example YAML</summary>

```yaml
parameters:
  - title: Fill in some steps
    properties:
      name:
        title: Select environments
        type: array
        items:
          type: string
          enum:
            - production
            - staging
            - development
        uniqueItems: true
        ui:widget: checkboxes
```

</details>

![](./static/multi-option-arrays.png)

#### Array with Custom Objects

This allows users to enter an **array of complex objects**, each containing multiple fields. It supports adding, removing, and reordering objects dynamically.

Example [`workflows.yaml`](https://github.com/harness-community/idp-samples/blob/5140ef7993a3c932c49af9162562a99e16428080/workflow-examples/multi-choice-list.yaml#L24-L34)

A user needs to provide a list of configurations, each containing:

- A dropdown selection (`array`)
- A boolean flag (`flag`)
- A free-text input (`someInput`)

<details>
<summary>Example YAML</summary>

```yaml
parameters:
  - title: Fill in some steps
    properties:
      arrayObjects:
        title: Array with custom objects
        type: array
        minItems: 0
        ui:options:
          addable: true
          orderable: true
          removable: true
        items:
          type: object
          properties:
            array:
              title: Array string with default value
              type: string
              default: value3
              enum:
                - value1
                - value2
                - value3
            flag:
              title: Boolean flag
              type: boolean
              ui:widget: radio
            someInput:
              title: Simple text input
              type: string
```

</details>

![](./static/template-arrays-multipleobjects.png)


### Comparison Table of Array Input Types  

| Feature | Distinct Values | Duplicate Values | Multiple Choice List | Custom Object Array |
|---------|----------------|------------------|----------------------|---------------------|
| **Dropdown Selection** | ✅ | ✅ | ❌ | ❌ |
| **Checkbox UI** | ❌ | ❌ | ✅ | ❌ |
| **Allows Multiple Selections** | ❌ | ❌ | ✅ | ✅ |
| **Supports Complex Objects** | ❌ | ❌ | ❌ | ✅ |
| **User-friendly Labels** (`enumNames`) | ❌ | ✅ | ❌ | ✅ |



#### Pass an Array of Inputs to a Harness Pipeline 

Harness Pipelines only support three variable types:

- String
- Number
- Secret

This means that arrays cannot be directly passed as pipeline inputs. Instead, if you need to pass multiple values, you should convert the array into a comma-separated string using [join](https://mozilla.github.io/nunjucks/templating.html#join) in Nunjucks.

- Use Case: 
You want users to select multiple values from a `enum` list, and then pass those values as a single comma-separated string into the Harness Pipeline’s `inputset`.

- How It Works: 
1. User selects multiple options from an enum list (`Option1`, `Option2`, `Option3`).
2. The selected options are joined into a single string using `parameters.exampleVar.join(',')`.
3. The pipeline **receives the values as a single string**, ensuring compatibility with Harness’ input format.

```YAML
    - title: Pass Variables Here      
      properties:
        exampleVar:
          title: Select an option
          type: array
          items:
            type: string
            enum:
              - Option1
              - Option2
              - Option3
          default: 
            - Option1
      ui:
        exampleVar:
          title: Select Options
          multi: true
  steps:
    - id: trigger
      name: Call a harness pipeline, and pass the variables from above
      action: trigger:harness-custom-pipeline
      input:
        url: 'https://app.harness.io/ng/account/*********/home/orgs/default/projects/*************/pipelines/*************/pipeline-studio/?storeType=INLINE'
        inputset:
          exampleVar: ${{ parameters.exampleVar.join(',') }}
          owner: ${{ parameters.owner }}
        apikey: ${{ parameters.token }}
```


### Boolean options

Boolean inputs allow users to select between true/false or yes/no values in forms. These inputs are useful for enabling/disabling features, selecting configuration options, and making binary choices.

#### Basic Boolean (Checkbox Input)

A simple **checkbox** allows users to toggle a setting on/off.

```YAML
parameters:
  - title: Fill in some steps
    properties:
      name:
        title: Checkbox boolean
        type: boolean
```

![](./static/template-checkbox-boolean.png)

#### Boolean Yes or No options (Radio Button)

Instead of a checkbox, you can use radio buttons for a clearer Yes/No selection.

```YAML
parameters:
  - title: Fill in some steps
    properties:
      name:
        title: Yes or No options
        type: boolean
        ui:widget: radio
```

![](./static/template-boolean-radio.png)

#### Boolean multiple options

For cases where **multiple boolean choices** are needed, you can use an array of checkboxes.

<details>
<summary>Example YAML</summary>

```yaml
parameters:
  - title: Fill in some steps
    properties:
      name:
        title: Select features
        type: array
        items:
          type: boolean
          enum:
            - "Enable scraping"
            - "Enable HPA"
            - "Enable cache"
        uniqueItems: true
        ui:widget: checkboxes
```

</details>

![](./static/template-boolean-multiselect.png)

#### When to Use Each Boolean Input Type

| **Input Type**            | **Best Use Case**           | **Example Scenario**                |
|---------------------------|----------------------------|--------------------------------------|
| **Checkbox Boolean**       | Single feature toggle      | Enable/Disable Dark Mode            |
| **Radio Button Boolean**   | Explicit Yes/No choice     | Confirming a deletion               |
| **Multi-Select Boolean**   | Selecting multiple options | Enable multiple monitoring features |


## Advanced Input Configurations



## Conditionals

## UI Pickers


## Dynamic API Picker


