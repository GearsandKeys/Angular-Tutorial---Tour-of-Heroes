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

## Add Services
### Why Services?
Components shouldn't fetch or save data directly and they certainly shouldn't knowingly present fake data. They should focus on presenting data and delegate data access to a service.

Services are a great way to share information among classes that _don't know each other_.

### Creating a Service
``` ng g service hero```

The command generates a skeleton HeroService class in src/app/hero.service.ts

### @Injectable() Services
Notice that the new service imports the Angular Injectable symbol and annotates the class with the [`@Injectable()`](https://angular.io/api/core/Injectable) decorator. This marks the class as one that participates in the _dependency injection system._

The @Injectable() decorator accepts a metadata object for the service, the same way the @Component() decorator did for your component classes.

### Getting Data
A service can get data from anywhere -  a web service, local storage, or mock data.

Removing data access from components means you can change your mind about the implementation anytime, without touching any components. They don't know how the service works.

Lets add mock heroes to our service:
1. Import it
```
// in hero.service.ts
import { Hero } from './hero';
import { HEROES } from './mock-heroes';
```
2. Add a `getHeroes` method to return the _mock heroes_
```
// in hero.service.ts
getHeroes(): Hero[] {
  return HEROES;
}
```

### Provide the HeroService
You must make a service available to the dependency injection system before Angular can inject it into a component by registering a provider. A provider is something that can create or deliver a service; in this case, it instantiates the HeroService class to provide the service.

To make sure that the HeroService can provide this service, register it with the injector, which is the object that is responsible for choosing and injecting the provider where the application requires it.

By default, the Angular CLI command ng generate service registers a provider with the root injector for your service by including provider metadata, that is providedIn: 'root' in the @Injectable() decorator.

```
@Injectable({
  providedIn: 'root',
})
```

When you provide the service at the root level, Angular creates a single, shared instance of HeroService and injects into any class that asks for it. Registering the provider in the @Injectable metadata also allows Angular to optimize an application by removing the service if it turns out not to be used after all.

[More on Providers](https://angular.io/guide/providers)
[More on Dependency Injection](https://angular.io/guide/dependency-injection)

### Updating HeroesComponent to Use Service
Delete the Heroes import and replace it with the HeroService
and Replace the definition of the `heroes` property with a declaration

```
// inside heroes.component.ts
import { HeroService } from '../hero.service';

...

`heroes: Hero[] = [];`
```
### Inject the Service
Add a private heroeService parameter of type HeroService to the constructor.
`constructor(private heroService: HeroService) {}`

The parameter simultaneously defines a private heroService property and identifies it as a HeroService injection site.

When Angular creates a HeroesComponent, the Dependency Injection system sets the heroService parameter to the singleton instance of HeroService.

### Adding Methods to a Service
Create a method to retrieve the heroes from the service.
```
getHeroes(): void {
  this.heroes = this.heroService.getHeroes();
}
```

### Good Use of ngOnInit()
While you could call getHeroes() in the constructor, that's not the best practice. 

Reserve the constructor for minimal initializations such as wiring constructor parameters to properties. The constructor shouldn't _do anything_. It certainly shouldn't call a function that makes HTTP requests to a remote server as a real data service would.

Instead, call getHeroes() inside the ngOnInit lifecycle hook and let Angular call ngOnInit() at an appropriate time after constructing a HeroesComponent instance.
```
ngOnInit(): void {
  this.getHeroes();
}
```

### Observable Data and Asynchronous Operation
Right now the HeroService.getHeroes() method has a synchronous signature. This works with mock data, but would fail if it were fetching from a remote server (which is a asynchornous operation) 

### Observable HeroService
Angulars [HttpClient](https://angular.io/api/common/http/HttpClient) methods return [RxJS](https://rxjs.dev/) Observables. 

To simulate getting data from the server with the RxJS of() function:

Open the HeroService file and import the Observable and of symbols from RxJS.
```
// in hero.service
import { Observable, of } from 'rxjs';
```
Replace the getHeroes() method with the following:
```
getHeroes(): Observable<Hero[]> {
  const heroes = of(HEROES);
  return heroes;
}
```
of(HEROES) returns an Observable<Hero[]> that emits a single value, the array of mock heroes.

### Subscribing in HeroesComponent
The HeroService.getHeroes method used to return a Hero[]. Now it returns an Observable<Hero[]>.

You'll have to adjust to that difference in HeroesComponent.

Find the getHeroes method and replace it with the following code
```
// in heroes.component.ts
getHeroes(): void {
  this.heroService.getHeroes()
      .subscribe(heroes => this.heroes = heroes);
}
```
Observable.subscribe() is the critical difference.

This waits for the Observable to emit the array of heroes —which could happen now or several minutes from now. The subscribe() method passes the emitted array to the callback, which sets the component's heroes property.

### Show Messages
Create your messages component:
`ng g component messages`

Modify the AppComponent template to display the generated MessagesComponent.
```
// in app.component.html
<h1>{{title}}</h1>
<app-heroes></app-heroes>
<app-messages></app-messages>
```

### Messages Service
Create the messages service:
`ng g service message`

Replace its contents with:
```
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root',
})
export class MessageService {
  messages: string[] = [];

  add(message: string) {
    this.messages.push(message);
  }

  clear() {
    this.messages = [];
  }
}
```

### Inject it into the HeroService
Import the `MessageService` into `hero.service.ts`

in `hero.service.ts` modify the constructor with a parameter to declare a private messageService property. Angular will inject the singleton `MessageService` into that property when it creates the `HeroService`

`constructor(private messageService: MessageService) { }`

_This is a typical "service-in-service" scenario: You inject the MessageService into the HeroService which is injected into the HeroesComponent._

### Sending Messages
Modify getHeroes() method to send a message when heroes are fetched.

```
getHeroes(): Observable<Hero[]> {
  const heroes = of(HEROES);
  this.messageService.add('HeroService: fetched heroes');
  return heroes;
}
```

### Display Messages
Go to `messages.component.ts` and import the message service
```import { MessageService } from '../message.service';```

Modify the constructor with a parameter that declares a public messageService property. Angular will inject the singleton MessageService into that property when it creates the MessagesComponent.
```
constructor(public messageService: MessageService) {}
```

The _messageService_ property must be public because you're going to bind to it in the template.

_Angular only binds to public component properties._

### Bind to the MessageService
Replace the messages.component.html with:
```
<div *ngIf="messageService.messages.length">

  <h2>Messages</h2>
  <button type="button" class="clear"
          (click)="messageService.clear()">Clear messages</button>
  <div *ngFor='let message of messageService.messages'> {{message}} </div>

</div>
```

### Additional Messages to Hero Service
WE can add the message service to the constructor of the `heroes.component.ts` and add to the method of when a hero is selected we:
```
this.messageService.add(`HeroesComponent: Selected hero id=${hero.id}`)
```

## Navigation with Routing
### Add a Routing Module
`ng generate module app-routing --flat --module=app`

`--flat` :	Puts the file in src/app instead of its own folder.

`--module=app` :	Tells the CLI to register it in the imports array of the AppModule.

Replace the `app-routing.module.ts` with:
```
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HeroesComponent } from './heroes/heroes.component';

const routes: Routes = [
  { path: 'heroes', component: HeroesComponent }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

### Routes
Routes tell the Router which view to display when a user clicks a link or pastes a URL into the browser address bar.

Since app-routing.module.ts already imports HeroesComponent, you can use it in the routes array:

Example Route:
```
const routes: Routes = [
  { path: 'heroes', component: HeroesComponent }
];
```
A typical Angular Route has two properties:

`path` :	A string that matches the URL in the browser address bar.

`component` :	The component that the router should create when navigating to this route.

This tells the router to match that URL to `path: 'heroes'` and display the `HeroesComponent` when the URL is something like `localhost:4200/heroes`.

### RouterModule.forRoot()
The `@NgModule` metadata initializes the router and starts it listening for browser location changes.

The following line adds the `RouterModule` to the AppRoutingModule imports array and configures it with the routes in one step by calling `RouterModule.forRoot()`:

`imports: [ RouterModule.forRoot(routes) ],`

_he method is called forRoot() because you configure the router at the application's root level. The forRoot() method supplies the service providers and directives needed for routing, and performs the initial navigation based on the current browser URL._

Next, `AppRoutingModule` exports `RouterModule` so it will be available throughout the application.

`exports: [ RouterModule ]`

### Add RouterOutlet
Inside the AppComponent template and relace the `<app-heroes>` element with a `<router-outlet>` element.
```
<h1>{{title}}</h1>
<router-outlet></router-outlet>
<app-messages></app-messages>
```

__NOTE: I HAD TO DELETE MY ORIGINAL ROUTER AND USE THE ROUTING COMMAND ABOVE TO GET IT TO WORK. PROBABLY SOMETHING TO DO WITH THE ARGUMENTS PASSED IN__

### Adding Navigation Links
To add a nav link, we use the `<nav>` tag and add a link using the routerLink:

```
<h1>{{title}}</h1>
<nav>
  <a routerLink="/heroes">Heroes</a>
</nav>
<router-outlet></router-outlet>
<app-messages></app-messages>
```

Of course we can style these links individually as well.

### Add a Dashboard View
First lets create a component to use:
` ng generate component dashboard`

Replace the default file content in these three files as follows:

dashboard.component.html
```
<h2>Top Heroes</h2>
<div class="heroes-menu">
  <a *ngFor="let hero of heroes">
    {{hero.name}}
  </a>
</div>
```
dashboard.component.ts
```
import { Component, OnInit } from '@angular/core';
import { Hero } from '../hero';
import { HeroService } from '../hero.service';

@Component({
  selector: 'app-dashboard',
  templateUrl: './dashboard.component.html',
  styleUrls: [ './dashboard.component.css' ]
})
export class DashboardComponent implements OnInit {
  heroes: Hero[] = [];

  constructor(private heroService: HeroService) { }

  ngOnInit(): void {
    this.getHeroes();
  }

  getHeroes(): void {
    this.heroService.getHeroes()
      .subscribe(heroes => this.heroes = heroes.slice(1, 5));
  }
}
```

The `.subscribe(heroes => this.heroes = heroes.slice(1, 5))`
returns only the top 4 of the heroes.

### Add Dashboard Route
Add the import for the dashboard to the app-routing.module.ts
`import { DashboardComponent } from './dashboard/dashboard.component';`

And add the route to the component in the routes:
`{ path: 'dashboard', component: DashboardComponent },`

### Default Route
It's good to have a home route that traffic is also redirected to when a route is used that doesn't work.

`{ path: '**', redirectTo: '/dashboard', pathMatch: 'full' },`
_MAKE SURE THIS IS AT THE BOTTOM_

### Add Dashboard to the Shell
Don't forget to add the dashboard component to `app.component.html` above the heroes link.

```
<h1>{{title}}</h1>
<nav>
  <a routerLink="/dashboard">Dashboard</a>
  <a routerLink="/heroes">Heroes</a>
</nav>
<router-outlet></router-outlet>
<app-messages></app-messages>
```

### Navigating to Hero Details
We want hero details to be availible:
1. By clicking a hero in the dashboard.
2. By clicking a hero in the heroes list.
3. By pasting a "deep link" URL into the browser address bar that identifies the hero to display.

Let decouple the details component
1. Delete `<app-hero-detail>` from heroes.component.html
2. Add hero detail route

```
// inside app-routing.module.ts
import { HeroDetailComponent } from './hero-detail/hero-detail.component';

...

{ path: 'detail/:id', component: HeroDetailComponent },
```
_The colon (:) character in the path indicates that :id is a placeholder for a specific hero id_

3. Add DashboardComponent hero links
Now that the router has a route to HeroDetailComponent, fix the dashboard hero links to navigate using the parameterized dashboard route.

```
// in dashboard.components.html
<a *ngFor="let hero of heroes"
  routerLink="/detail/{{hero.id}}">
  {{hero.name}}
</a>
```

4. Adjust heroes.component.html button to become a link

```
<ul class="heroes">
  <li *ngFor="let hero of heroes">
    <a routerLink="/detail/{{hero.id}}">
      <span class="badge">{{hero.id}}</span> {{hero.name}}
    </a>
  </li>
</ul>
```
5. Remove old unused code
Tidy up the heroes.component.ts file by removing the onSelect method since we're routing them now.


### Routable HeroDetailComponent
Add the following imports:
```
// hero-detail.component.ts
import { ActivatedRoute } from '@angular/router';
import { Location } from '@angular/common';

import { HeroService } from '../hero.service';
```

Inject the ActivatedRoute, HeroService, and Location services into the constructor, saving their values in private fields:
```
// hero-detail.component.ts
constructor(
  private route: ActivatedRoute,
  private heroService: HeroService,
  private location: Location
) {}
```

The `ActivatedRoute` holds information about the route to this instance of the HeroDetailComponent. This component is interested in the route's parameters extracted from the URL. The "id" parameter is the id of the hero to display.

The `HeroService` gets hero data from the remote server and this component will use it to get the hero-to-display.

The `location` is an Angular service for interacting with the browser. You'll use it later to navigate back to the view that navigated here.

### Extract the ID route parameter
In the `ngOnInit()` lifecycle hook call `getHero()` and define it as follows.
```
// in hero-detail.component.ts
ngOnInit(): void {
  this.getHero();
}

getHero(): void {
  const id = Number(this.route.snapshot.paramMap.get('id'));
  this.heroService.getHero(id)
    .subscribe(hero => this.hero = hero);
}
```
The route.snapshot is a static image of the route information shortly after the component was created.

The paramMap is a dictionary of route parameter values extracted from the URL. The "id" key returns the id of the hero to fetch.

Route parameters are always strings. The JavaScript Number function converts the string to a number, which is what a hero id should be.

The browser refreshes and the application crashes with a compiler error. HeroService doesn't have a getHero() method. Add it now.

### Add HeroService.getHero()
pen HeroService and add the following getHero() method with the id after the getHeroes() method:
```
// hero.service.ts
getHero(id: number): Observable<Hero> {
  // For now, assume that a hero with the specified `id` always exists.
  // Error handling will be added in the next step of the tutorial.
  const hero = HEROES.find(h => h.id === id)!;
  this.messageService.add(`HeroService: fetched hero id=${id}`);
  return of(hero);
}
```

### Adding a Go Back Button
Add the following to hero-detail.component.html
```
<button type="button" (click)="goBack()">go back</button>
```

Add the method to the hero-detail.component.ts:
```
goBack(): void {
  this.location.back();
}
```

