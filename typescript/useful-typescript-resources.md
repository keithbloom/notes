# Useful TypeScript resources

This is a list of documentation and projects from around the internet that I have found useful overtime

* The [TypeScript handbook](https://www.typescriptlang.org/docs/handbook/intro.html) is very well written with interactive examples. Some pages to check are:
    * The pages on [Type Manipulation](https://www.typescriptlang.org/docs/handbook/2/types-from-types.html) explain the specific power of the type system in TypeScript 
    * The [Do's and Don't](https://www.typescriptlang.org/docs/handbook/declaration-files/do-s-and-don-ts.html) page - which can be largely summarized as do not use the `any` type
* [This post](https://www.executeprogram.com/blog/porting-a-react-frontend-to-typescript) by Gary Bernhardt has a good write up of porting a UI application. 
* I booked marked a post about [Building a DSL](https://dev.to/matechs/building-custom-dsls-in-typescript-29el) which has some crazy generics
* Also book marked this from the Tableau engineering blog about [advanced generics](https://engineering.tableau.com/really-advanced-typescript-types-c590eee59a12)
* A project that caught my eye recently is [ts-pattern](https://github.com/gvergnaud/ts-pattern) which aims to bring pattern matching to TypeScript. I am not sure if this is necessary as TypeScript can narrow types nicely in conditionals which brings a form of type based pattern matching but it looks like a good example of what can be done.

