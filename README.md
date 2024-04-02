# vue-components
The "List of Vue Dynamic Components Showcase" project aims to exhibit proficiency in Vue.js by creating a dynamic and interactive website showcasing a variety of Vue components.

#Styling
Block, Element, Modifier (BEM):

Block (bem.block): The main container element of the component. It represents the highest-level component, encapsulating all its elements and functionality. In this case, it's applied as a class to the main <div> element of the component.

Element (bem.element('elementName')): Nested elements within the main block. Elements are identified by their relationship to the block and are styled relative to the block. Examples here include elements like 'overlay', 'wrapper', 'header', 'content', etc. These are applied as classes to the respective elements within the component.

Modifier (bem.modifier and bem.element('elementName').modifier): Variations or states of blocks or elements. Modifiers alter the appearance or behavior of blocks or elements without changing their core functionality. In this template, modifiers are applied conditionally through JavaScript logic or Vue component props. For example, the fade-slide modifier is used to specify the transition effect for the component.

#Script - Typscript

The Vue components above are implemented using TypeScript, leveraging its powerful type system to enhance code clarity, maintainability, and type safety. 
Each component defines its props with explicit types, ensuring that data passed into the component adheres to expected formats. 
