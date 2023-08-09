>npm create svelte@latest crowd-stories
select skeleton project
select typescript
eslint
prettier
>npm install

## Normalize general css properties like padding etc
>npm i modern-normalize
then in root layout import
import 'modern-normalize/modern-normalize.css';

### Instal Sass
>npm i -D sass

>npm i -D svelte-preprocess

for pre-processing data in svelet.config.js
used instead of vite-preprocess

## package to help in media queries
> npm i @unsass/breakpoint

## Global styles
in src/styles folder create main.css file
and import it in the root layout

### font
>npm i @fontsource/metropolis

## icon package
> npm i lucide-svelte


## Using typescript to extend native html attributes

## tippy.js for dropdowns using actions
 $actions alias in svelte.config.js file
 >npm i tippy.js