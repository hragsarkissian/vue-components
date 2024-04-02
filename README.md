# vue-components
The "Vue Components" project aims to demonstrate expertise in Vue.js by presenting a curated collection of Vue components in a code showcase format. 
This showcase provides an opportunity to explore the implementation details of various Vue components,functionality, and usage.

#Styling
Block, Element, Modifier (BEM):

Block (bem.block): The main container element of the component. It represents the highest-level component, encapsulating all its elements and functionality. In this case, it's applied as a class to the main <div> element of the component.

Element (bem.element('elementName')): Nested elements within the main block. Elements are identified by their relationship to the block and are styled relative to the block. Examples here include elements like 'overlay', 'wrapper', 'header', 'content', etc. These are applied as classes to the respective elements within the component.

Modifier (bem.modifier and bem.element('elementName').modifier): Variations or states of blocks or elements. Modifiers alter the appearance or behavior of blocks or elements without changing their core functionality.

#Script - Typscript

The Vue components above are implemented using TypeScript, leveraging its powerful type system to enhance code clarity, maintainability, and type safety. 
Each component defines its props with explicit types, ensuring that data passed into the component adheres to expected formats. 
