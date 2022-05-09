# Setup

Basic setup for a vue app

`index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>...</title>
    <!-- Import Styles -->
    <link rel="stylesheet" href="./assets/styles.css" />
    <!-- Import Vue.js -->
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
      ...
    </div>

    <!-- Import Js -->
    <script src="./main.js"></script>
    
    <!-- Mount App -->
    <script>
      const mountedApp = app.mount('#app')
    </script>
  </body>
</html>
```

`main.js`

```javascript
const app = Vue.createApp({
    data() {
        return {
            ...
        }
    }
})
```

# Directives

## Attribute Binding

### v-bind

Dynamically bind an attribute to an expression\
Example:

```html
<!-- full -->
<img v-bind:src="image">

<!-- shorthand -->
<img :src="image">

<!-- other usecases: -->
<img :alt="description">
<a :href="url">
<div :class="isActive">
<span :style="isActive">
<span :disabled="isDisabled">
```

## Conditional Rendering

### v-if

Render element it based upon a condition\
Example:

`index.html`

```html
<div class="product-info">
	<h1>{{ product }}</h1>
	<p v-if="inStock">In Stock</p>
	<p v-else>Out of Stock</p>
</div>
```

`main.js`

```javascript
data() {
	return {
		product: 'Socks',
		image: './assets/images/socks_blue.jpg',
		inStock: true
	}
}
```

### v-else-if

```html
<div class="product-info">
	<h1>{{ product }}</h1>
	<p v-if="inventory > 10">In Stock</p>
	<p v-else-if="inventory <=10 && inventory > 0">Almost sold out!</p>
	<p v-else>Out of Stock</p>
</div>
```

### v-show

Rather then add or remove the elevement from the DOM it toggels its visibility

```html
<div class="product-info">
	<h1>{{ product }}</h1>
	<p v-show="inStock">In Stock</p>
</div>
```

Is the value `false` ist will now hide the element with inline style `display: none`

```html
<p style="display: none;">In Stock</p>
```

## List Rendering

### v-for

Render elements from an array

`index.html`

```html
<ul>
	<li v-for="detail in details">{{ detail }}</li>
</ul>
```

`main.js`

```javascript
data() {
	return {
		...
		details: ['50% cotton', '30% wool', '20% polyester']
	}
}
```

By saying `:key="variant.id"`, we’re using the shorthand for `v-bind` to bind the variant’s `id` to the `key` attribute. This gives each DOM element a unique key so that Vue can grasp onto the element and not lose track of it as things update within the app

`index.html`

```html
<div v-for="variant in variants" :key="variant.id">{{ variant.color }}</div>
```

`main.js`

```javascript
data() {
	return {
		...
		variants: [
			{ id: 2234, color: 'green', image: './assets/images/socks_green.jpg' },
			{ id: 2235, color: 'blue', image: './assets/images/socks_blue.jpg' },
		]
	}
}
```

## Event Handling

### v-on

Listen vor events on an element

`index.html`

```html
<div class="cart">Cart({{ cart }})</div>
      
<div class="product-display">
	<div class="product-container">
		...
		<div class="product-info">
			...
            <button class="button" v-on:click="addToCart">Add to Cart</button>
		</div>
	</div>
</div>
```

`main.js`

```javascript
data() {
	return {
		cart:0,
		...
	}
},
methods: {
	addToCart() {
      	this.cart += 1
	}
}
```

### @mouseover

The Vue.js variant of hover

`index.html`

```html
<div v-for="variant in variants" :key="variant.id" @mouseover="updateImage(variant.image)">{{ variant.color }}</div>
```

`main.js`

```javascript
methods: {
	...
	updateImage(variantImage) {
		this.image = variantImage
	}
}
```

### Class &  Style Binding

`index.html`

```html
<div 
	v-for="variant in variants" 
	:key="variant.id" 
	@mouseover="updateImage(variant.image)"
	class="color-circle"
	:style="{ backgroundColor: variant.color }" 
></div>
<button class="button" @click="addToCart">Add to Cart</button>
```

Using style binding like this there are some important points to keep in mind.

- Naming Convention:\
  Use camelCased property names in stead of a dash. `backgroundColor: ...` in stead of `background-color: ...`. A dash will be looked at as `minus` because the `style object` is still `JavaScript`
- Multiple style properties:\
  Instead of adding all the style properties `inline`, you can all the properties to an `Object` in the `data()`

`main.js`

```javascript
data() {
	return {
    	styles: {
        	color: 'red',
            fontSize: '14px'
		}
	}
}
```

## Computed Properties

### Basic computed title

```javascript
const app = Vue.createApp({
    data() {
        return {
            product: 'Socks',
            brand: 'Vue Mastery',
        }
    },
    computed: {
        title() {
            return this.brand + ' ' + this.product
        }
    }
})
```

### Multiple computed data from variants

`index.html`

```html
<div 
	class="color-circle" 
	v-for="(variant, index) in variants" 
	:key="variant.id" 
	@mouseover="updateVariant(index)" 
	:style="{ backgroundColor: variant.color }"></div>
<button class="button" :class="{ disabledButton: !inStock }" :disabled="!inStock" v-on:click="addToCart">Add to Cart</button>
```

`main.js`

```javascript
const app = Vue.createApp({
    data() {
        return {
            ...
            product: 'Socks',
            brand: 'Vue Mastery',
            selectedVariant: 0,
            ...
            variants: [
              { id: 2234, ..., quantity: 50 },
              { id: 2235, ..., quantity: 0 },
            ]
        }
    },
    methods: {
        updateVariant(index) {
            this.selectedVariant = index
        },
    },
    computed: {
        image() {
            return this.variants[this.selectedVariant].image
        },
        inStock() {
            return this.variants[this.selectedVariant].quantity
        }
    }
})
```

## Components and Props

### Using Components

```
> Intro-to-Vue-3
  > assets
  > components
      ProductDisplay.js
  index.html
  main.js
```

`ProductDisplay.js`

```javascript
app.component('product-display', {
    template:
    /*html*/
    `<div class="product-display">
        <div class="product-container">
            <div class="product-image">
                <img v-bind:src="image">
            </div>
            <div class="product-info">
                <h1>{{ title }}</h1>

                <p v-if="inStock">In Stock</p>
                <p v-else>Out of Stock</p>
                <ul>
                <li v-for="detail in details">{{ detail }}</li>
                </ul>

                <div 
                v-for="(variant, index) in variants" 
                :key="variant.id" 
                @mouseover="updateVariant(index)" 
                class="color-circle" 
                :style="{ backgroundColor: variant.color }">
                </div>
                
                <button class="button" :class="{ disabledButton: !inStock }" :disabled="!inStock" v-on:click="addToCart">Add to Cart</button>
            </div>
        </div>
    </div>`,
    data() {
        return {
            product: 'Socks',
            brand: 'Vue Mastery',
            selectedVariant: 0,
            details: ['50% cotton', '30% wool', '20% polyester'],
            variants: [
              { id: 2234, color: 'green', image: './assets/images/socks_green.jpg', quantity: 50 },
              { id: 2235, color: 'blue', image: './assets/images/socks_blue.jpg', quantity: 0 },
            ]
        }
    },
    methods: {
        addToCart() {
            this.cart += 1
        },
        updateVariant(index) {
            this.selectedVariant = index
        }
    },
    computed: {
        title() {
            return this.brand + ' ' + this.product
        },
        image() {
            return this.variants[this.selectedVariant].image
        },
        inStock() {
            return this.variants[this.selectedVariant].quantity
        }
    }
})
```

`main.js`

```javascript
const app = Vue.createApp({
    data() {
        return {
            cart: 0
        }
    },
    methods: {
        
    },
})
```

`index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Vue Mastery</title>
    <!-- Import Styles -->
    <link rel="stylesheet" href="./assets/styles.css" />
    <!-- Import Vue.js -->
    <script src="https://unpkg.com/vue@3.0.11/dist/vue.global.js"></script>
  </head>
  <body>
    <div id="app">
      <div class="nav-bar"></div>

      <div class="cart">Cart({{ cart }})</div>

      <product-display></product-display>
      <product-display></product-display>
      <product-display></product-display>

    </div>

    <!-- Import App -->
    <script src="./main.js"></script>

    <!-- Import Components -->
    <script src="./components/ProductDisplay.js"></script>

    <!-- Mount App -->
    <script>
      const mountedApp = app.mount('#app')
    </script>
  </body>
</html>
```

### Using Props

In order to access data from outside the component . For example the parent has a `premium: true`data the the child component has to access. Here we can us props that acts as a funnel

```
(parant) main.js has value 'premium'
(child) ProductDisplay.js has prop 'premium' which it gets from main.js
```

`main.js`

```javascript
const app = Vue.createApp({
  data() {
    return {
      cart: 0,
      premium: true
    }
  }
})
```

Here we can validate that the `premium` value is of type `Boolean` and is `required: true`

`ProductDisplay.js`

```javascript
app.component('product-display', {
  props: {
    premium: {
      type: Boolean,
      required: true
    }
  },
  ...
}
```

Now we can add the custome `premium` attribute on the component that we are using it in

`index.html`

```html
<product-display :premium="premium"></product-display>
```

With this set we can now use the prop in the component

`ProductDisplay.js`

```javascript
app.component('product-display', {
    props: {
        premium: {
            type: Boolean,
            required: true
        }
    },
    template:
    /*html*/
    `<div class="product-display">
        <div class="product-container">
            ...
            <div class="product-info">
                ...
                <p>Shipping: {{shipping}}</p>
                ...
            </div>
        </div>
    </div>`,
    data() {
        ...
    },
    methods: {
        ...
    },
    computed: {
        ...
        shipping() {
            if (this.premium) {
                return 'Free'
            }
            return 2.99
        }
    }
})
```

<br>

# Nextup Communicating Events
https://app.nuclino.com/DaveRingg/Main/Intro-to-Vue-3-2244c431-1c14-4be3-a3e1-9ff78f0f44cb
