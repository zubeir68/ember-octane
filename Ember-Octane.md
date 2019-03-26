# Ember-Octane

![enter image description here](http://hangaroundtheweb.com/wp-content/uploads/2018/08/ember-octane.png)

## Was ist Ember-Octane ?

***"People often ask me: 'is Octane a version of Ember? Is Octane Ember 4.0?'  
Neither.  
Octane is an announcement that number of anticipated features are ready for apps to use, because the guides, tooling, and most importantly the wider ecosystem is ready."***   -Yehuda Katz (co-founder of Ember.js)

**Ziel: Produktivität und Perfomance**

## Features

### 1.  jQuery nicht mehr standardmäßig

![enter image description here](https://upload.wikimedia.org/wikipedia/commons/thumb/f/fd/JQuery-Logo.svg/768px-JQuery-Logo.svg.png) ![enter image description here](https://media.giphy.com/media/1jkVi22T6iUrQJUNqk/giphy.gif)

### 2. Native JavaScript Classes

  ####  Ember Standard

```javascript
export default Component.extend({
  foo: service(),
      
  bar: computed('someWord', 'otherWord', function() {
    let someWord = this.someWord;
    let otherWord = this.otherWord;
         
    return `${someWord} - ${otherWord}`;
  }),
       
  actions: {
    trigger() {
      //blabla
    }
  }
}) 
```

####  Ember-Octane

```javascript
export default class ExampleComponent extends Component{
  @service foo
  
  @computed('someWord', 'otherWord')
  get bar() {
    const someWord = this.get('someWord');
    const otherWord = this.get('otherWord');
    return `${someWord} - ${otherWord}`;
  }
  
  @action
  trigger() {
    //blabla
  }
}
```
    
### 3. Glimmer Components

#### Ember-Standard

```html
{{!-- templates//components/post.hbs --}}

{{#if (eq  type  'image')}}
<img  src={{post.imageUrl}}  title={{post.imageTitle}}>
{{/if}}

{{post.text}}
```
```javascript
// components/post.js
export  default  Component.extend({

  tagName: 'section',
  classNames: ['post'],
  classNameBindings: ['type'],
  ariaRole: 'region',

  post: null,
  
  type: readOnly('post.type'),
  
  didInsertElement() {
    this._super(...arguments);
    if (this.type  ===  'image') {
      setupImageOverlay(this.element.querySelector('img'));
    } 
  }
});
```

#### Ember-Octane

```html
<section
  ...attributes
  role="region"
  type={{@post.type}
  class="post {{@post.type}}"
>
  {{#if (eq @post.type  'image')}}
    <img  {{did-insert  this.didInsertImage}}  src={{@post.imageUrl}}  title={{@post.imageTitle}}/>
  {{/if}}
  {{@post.text}}
</section>
```
```javascript
// components/post.js
export default class PostComponent extends GlimmerComponent{
  @action
  didInsertImage(element) {
    setupImageOverlay(element);
  }
}
```

### 4. Element Modifiers

#### Ember-Standard
```html
{{#if this.isOpen}}
  <div class="popover">
    {{yield}}
  </div>
{{/if}}
```
```javascript
export default Component.extend({
  didRender() {
    if (this.isOpen && !this._popper) {
      let popoverElement = this.element.querySelector('.popover');
       
      this._popper = new Popper(documents, popoverElement);
    } else if(this._popper) {
      this._popper.destroy();
    }
  },
   
  willDestroyElement() {
    if(this._popper) {
      this._popper.destroy();
    }
  }
})
```
#### Ember-Octane

```html
{{#if this.isOpen}}
  <div 
    {{did-insert (action this.setupPopper)}}
    {{will-destroy (action this.teardownPopper)}}
     
    class="popover"
  >
    {{yield}}
  </div>
{{/if}}
```

```javascript
export default Component.extend({
  setupPopper(element) {
    this._popper = new Popper(document, element);
  },
   
  teardownPopper() {
    this._popper.destroy();
  }
})
```
