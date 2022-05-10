# Tour of Heros Notes

## Build the project:

` ng new angular-tour-of-heroes `
The I selected yes to add routing, and chose scss because it allows me to do extra stuff with css.

to run the skeleton I can use:
``` 
cd angular-tour-of-heroes
ng serve --open
```

The ng serve command builds the app, starts the development server, watches the source files, and rebuilds the application as you make changes to those files.

The --open flag opens a browser to http://localhost:4200.
## Creating a Project
### Components
The page you see is the application shell. The shell is controlled by an Angular component named `AppComponent`

_Components_ are the fundamental building blocks of Angular applications. They display data on the screen, listen for user input, and take action based on that input.

You'll find the implementation of the shell AppComponent distributed over three files:
| Files | Details
| --- | --- |
| `app.component.ts` |	The component class code, written in TypeScript. |
| `app.component.html` | The Component template, written in HTML |
| `app.component.css` | The component's private CSS styles |

### Change the Title

in `app.component.ts` we set the title.
The value is passed into the `app.component.html` (template) with {{}} for _interpolation binding syntax_
```
<h1>{{title}}<h1/>
```

### Styles
The CLI generated an empty `styles.scss` for this purpose. Put your global/application-wide styles there.

## Hero Editor

### Creating a component
`ng generate component <name_of_component>` (Make sure you're in the app directory)

So here we used: `ng g component heroes`

Now we have a directory called _heroes_ with:
1. HTML file
2. SCSS file
3. Test file (.spec)
4. Typescript file for the code

The cli generated three metadata properties:
| Properties | Details
| --- | --- |
| `selector` |	The component's CSS element selector. |
| `templateUrl` | The location of the component's template file. |
| `styleUrls` | The location of the component's private CSS styles. |

The CSS element selector, 'app-heroes', matches the name of the HTML element that identifies this component within a parent component's template.

The ngOnInit() is a lifecycle hook. Angular calls ngOnInit() shortly after creating a component. It's a good place to put initialization logic.

Always export the component class so you can import it elsewhere … like in the AppModule.

Every component must be declared in exactly one NgModule

### Showing a Component View
1. Declare properties in component
2. Using _interpolation binding syntax_ ( {{}} ) put properties in HTML
3. Put component tags in the main app HTML

Remember that app-heroes is the element selector for the HeroesComponent. So add an <app-heroes> element to the AppComponent
```
<h1>{{title}}</h1>
<app-heroes></app-heroes>
```

### Creating Interface
Create a Hero interface in its own file in the src/app folder. Give it id and name properties.

```
// in scr/app/hero.ts
export interface Hero {
  id: number;
  name: string;
}
```

Because of the export we can import it into our `hero.component.ts` component
```import { Hero } from '../hero';```

AND we can declare our hero upon init with the type `Hero`:
```
export class HeroesComponent implements OnInit {
  hero: Hero = {
    id: 1,
    name: 'Windstorm'
  };
```

Now, since hero is an object we'll need to use the dot notation to print its properties:

```
// in heroes.component.html
<h2>{{hero.name}} Details</h2>
<div><span>id: </span>{{hero.id}}</div>
<div><span>name: </span>{{hero.name}}</div>
```
### Pipes
We can use formatting pipes like UppercasePipe
```
<h2>{{hero.name | uppercase}} Details</h2>
```
This will print the name in uppercasee

[There's a whole bunch of awesome formatting pipes!](https://angular.io/guide/pipes)

### Editing a Hero
We can set up editing a `name` property using an `<input>` textbox

The textbox should both display the hero's name property and update that property as the user types.
To automate that data flow, setup a two-way data binding between the <input> form element and the hero.name property.

```
<div>
  <label for="name">Hero name: </label>
  <input id="name" [(ngModel)]="hero.name" placeholder="name">
</div>
```

`[(ngModel)]` is Angular's two-way data binding syntax.

However, ngModel is a valid Angular directive, it isnt available by default. We need to _opt-in_ to using it from the optional `FormsModule`

### Importing Angular Modules
Angular needs to know how the pieces of your application fit together and what other files and libraries the application requires. This information is called metadata.

To import an Angular Module we need to:
1. first declare it at the top of our `app.module.ts`
```
// in app.module.ts
import { FormsModule } from '@angular/forms'; // <-- NgModel lives here
```

2. Add it to the [`@NgModule`](https://angular.io/api/core/NgModule) metadatas import array (Which contains a list of external modules for the application)

```
// in app.module.ts
imports: [
  BrowserModule,
  FormsModule
],
```

## Display a List

### Creating Mock/Dummy Data
Create a file called mock-heroes.ts in the src/app/ folder. Define a HEROES constant as an array of ten heroes and export it. 

### Displaying Mock/Dummy Data
Now we can import the mock!
```
// in src/heroes/heroes.component.ts
import { HEROES } from '../mock-heroes';
```

Now we can declare heroes to be our mock:
```
export class HeroesComponent implements OnInit {

  heroes = HEROES;
}
```

### ngFor
The *ngFor is Angular's repeater directive. It repeats the host element for each element in a list.

_Don't forget the asterisk (*) character in front of ngFor. It's a critical part of the syntax._

Now we can open our template file and loop through our heroes list calling each element a hero and listing its properties with dot notation:
```
// in heroes.component.html
<h2>My Heroes</h2>
<ul class="heroes">
  <li *ngFor="let hero of heroes">
    <button type="button">
        <span class="badge">{{hero.id}}</span>
        <span class="name">{{hero.name}}</span>
      </button>
  </li>
</ul>
```

The syntax in this example is as follows:
| Syntax | Details
| --- | --- |
| `<li>` |	The host element |
| `heroes` | Holds the mock heroes list from the class. |
| `hero` | Holds the currecnt hero object for each iteration |

Now we have an unordered list with a button displaying a heroes properties.

Now we can use the classes to add CSS properties.

### Style the Heroes
You may prefer instead to define private styles for a specific component and keep everything a component needs —the code, the HTML, and the CSS— together in one place.

This approach makes it easier to re-use the component somewhere else and deliver the component's intended appearance even if the global styles are different.

_See CSS in heroes.component.html template_

### Click Methodsv (Viewing Details)
#### Add Click Event Binding
You can add click binding to all the list like this:
```
<li *ngFor="let hero of heroes">
  <button type="button" (click)="onSelect(hero)">
  <!-- ... -->
```
The parentheses around click tell Angular to listen for the `<button>` element's click event. When the user clicks in the `<button>`, Angular executes the onSelect(hero) expression.

#### Add Click Event Handler
Add the following onSelect() method, which assigns the clicked hero from the template to the component's selectedHero.
```
// in heroes.component.ts
selectedHero?: Hero; //near top during var declare

onSelect(hero: Hero): void { // near bottom after ngOnInit func
  this.selectedHero = hero;
}
```

Then rename the component's hero property to selectedHero but don't assign any value to it since there is no selected hero when the application starts.

#### Add Details Section
Add the following to `heroes.component.html` beneath the list section:
```
<div *ngIf="selectedHero">
  <h2>{{selectedHero.name | uppercase}} Details</h2>
  <div>id: {{selectedHero.id}}</div>
  <div>
    <label for="hero-name">Hero name: </label>
    <input id="hero-name" [(ngModel)]="selectedHero.name" placeholder="name">
  </div>
</div>
```

The hero details should only be displayed when a hero is selected. When a component is created initially, there is no selected hero, so we add the *ngIf directive to the <div> that wraps the hero details, to instruct Angular to render the section only when the selectedHero is actually defined (after it has been selected by clicking on a hero).

### Styling Selected Items
To help identify the selected hero, you can use the .selected CSS class in the styles you added earlier. 

```
.heroes button.selected:active {
  background-color: black;
  color: white;
}
```
To apply the .selected class to the <li> when the user clicks it, use class binding.

Angular's class binding can add and remove a CSS class conditionally. Add [class.some-css-class]="some-condition" to the element you want to style.

```
// in heroes.component.html
[class.selected]="hero === selectedHero"
```

When the current row hero is the same as the selectedHero, Angular adds the selected CSS class. When the two heroes are different, Angular removes the class.

Finished list looks like:
```
<li *ngFor="let hero of heroes">
  <button [class.selected]="hero === selectedHero" type="button" (click)="onSelect(hero)">
    <span class="badge">{{hero.id}}</span>
    <span class="name">{{hero.name}}</span>
  </button>
</li>
```

## Creating a Feature Component
As features in a component grow, it will not be maintainable. You will have to split up large components into smaller sub-components, each focused on a specific task or workflow.

HeroesComponent: Presents list of heroes
HeroDetailComponent: Presents details of a selected hero

Make new component:
`ng g component hero-detail`

The command also adds the `HeroDetailComponent` as a declaration in the [`@NgModule`](https://angular.io/api/core/NgModule) decorator of the src/app/app.module.ts file.

### Write the template
Move the detail logic to the new `hero-detail.component.html`

### Add @Input() Hero Property
Import the Hero type:
```
// in hero-detail.component.ts
import { Hero } from '../hero';
```
The hero property must be an Input property, annotated with the @Input() decorator, because the external HeroesComponent will bind to it like this.

Amend the @angular/core import statement to include the Input symbol.
```
// in hero-detail.component.ts
import { Component, OnInit, Input } from '@angular/core';
```
Add a hero property, preceded by the @Input() decorator.
```
// in hero-detail.component.ts
@Input() hero?: Hero;
```

### Show the HeroDetailComponent
The two components will have a parent/child relationship. The parent HeroesComponent will control the child HeroDetailComponent by sending it a new hero to display whenever the user selects a hero from the list.

You won't change the HeroesComponent class but you will change its template.

### Updating the Parent Component (Heroes)
The HeroDetailComponent selector is 'app-hero-detail'. Add an <app-hero-detail> element near the bottom of the HeroesComponent template, where the hero detail view used to be.

Bind the HeroesComponent.selectedHero to the element's hero property like this.

```
<app-hero-detail [hero]="selectedHero"></app-hero-detail>
```