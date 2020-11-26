---
title: Home
hide_title: true
sections:
  - section_id: hero
    type: section_hero
    title: What is MMIC?
    content: >-
      <div style="text-align: justify">
      The Molecular Mechanics Interoperable Components (MMIC) project provides a
      standard for input and output of MM programs by defining the scientific
      and computational stages of classical MM pipelines, but leaving the
      implementation up to the developer/user. MMIC attempts to define the
      “what” of scientific stages without restricting the “how” i.e. MMIC
      defines only the input and output the implementation must conform to so
      that end-users can swap out different implementations with minimal effort
      in their existing pipelines, or workflow tools of their choice. </div>
  - section_id: img
    type: section_content
    title: What are MMIC Components?
    content: >-
      Components in the MMIC world behave like  structured "boxes" with a common API. 
      Each component has well-defined input and output schemas, with complete unawareness
      of other components used in the workflow.

      <div style="text-align:center"><img src="images/component.png"/></div><BR>

      Each component does internal data validation to ensure the input and output models
      are compliant with the specified schemas. Using any component requires a single
      class method to be invoked:<BR><BR>
      <div style="text-align:left"><img src="images/comp-code.png"/></div><BR>
      For more info about the API and component design, see the online <a href="https://github.com/MolSSI/MMIC">documentation</a>.
  - section_id: components
    type: section_posts
    title: Components Under Development
    posts_number: 4
    actions: []
template: advanced
---
