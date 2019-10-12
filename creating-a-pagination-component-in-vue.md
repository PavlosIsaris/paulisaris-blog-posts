---
title: "Creating a pagination component in Vue.js"
date: "2019-10-12"
tags: [javascript,tutorials,programming]
image: img/posts/creating_a_pagination_component_in_vue.jpg
---



# Introduction

 post photo by [pexels](https://www.pexels.com/)

[Vue.js](https://vuejs.org/v2/guide/) is a progressive JavaScript framework that allows you to create compelling 
user interfaces efficiently and easily. 

In this post, we are going to create a pagination component, able to have a dynamic number
of steps!

Our final application will look like this:

![Example pagination component](https://raw.githubusercontent.com/PavlosIsaris/Vue.js-pagination-example/master/public/img/example2.gif)

# Project set up

use the vue cli to create out app:
```bash
npm install -g @vue/cli
```

Next, we make use of the CLI to create all the boilerplate for our app:

```bash
vue create pagination-component-vue-example
```

In order to verify that the initialization part was successfully completed, 
let's go into our project's directory and `serve` our app:

```bash
cd pagination-component-vue-example
npm run serve
```

If everything goes as planned, we will receive no error messages, and will 
see the Vue.js default page upon navigating to [http://localhost:8080/](http://localhost:8080/):



Vue runs some initialization commands, and our project has been created.

It's time to design our app!

We will create a pagination component that will take as parameters

And we will use the app component in order to test it.

First thing first, let's get rid of the `src/components/HelloWorld.vue`.

We are going to create a `src/components/PaginationComponent.vue` which will hold 
the logic for our pagination component.

In this tutorial, I will use [Bootstrap](https://getbootstrap.com/) in order to
style the HTML in a clean way, but feel totally free to skip this step (or use another HTML library).

Change `src/App.vue` to reflect the following:

```vue
<template>
  <div id="app">
    <pagination-component></pagination-component>
  </div>
</template>

<script>
import PaginationComponent from './components/PaginationComponent.vue'
import 'bootstrap';

export default {
  name: 'app',
  components: {
    PaginationComponent
  }
}
</script>

<style>
  @import "~bootstrap/dist/css/bootstrap.min.css";
</style>

```

For the time being, we can leave our `src/components/PaginationComponent.vue` empty and dumb:

```vue
<template>
   I'm an example component.         
</template>

<script>
    export default {
        mounted() {
            console.log('Component mounted.')
        }
    }
</script>

```
# Designing our pagination component

Now, we need to converge on what our pagination component should do, what it's limitations would be,
and how it should be used by our app.

In my app, I decided that I would like to have a "only backwards free" strategy, meaning that
Once the user is on the n'th step, they should only be able to navigate to previous steps through
the navigation links.

In order fo the user to move forward, they need to click on the "Next" button.
This may come in handy when you have a paginated form with steps, in which you would like to\
perform some sort of validation before allowing the user to proceed to the next step.

Having these functional requirements in mind, we can construct a `steps` array that will
be passed from the app component own to the pagination component.

So, let's change our `App.vue` to reflect the following:

```vue
<template>
  <div id="app">
    <pagination-component></pagination-component>
  </div>
</template>
<script>
import PaginationComponent from './components/PaginationComponent.vue'
import 'bootstrap';
export default {
  name: 'app',
  components: {
    PaginationComponent
  },
  data() {
    return {
      steps: [
        {
          title: 'Introduction',
          allowNext: false
        }, {
          title: 'Second',
          allowNext: false
        }, {
          title: 'Third',
          allowNext: false
        }, {
          title: 'Final',
          allowNext: false
        }
      ]
    }
  }
}
</script>
```

We constructed a `steps` array, which will define the different "pages" in the pagination component.
The `allowNext` property can define whether we would like the user to be able to go to the next step by clicking on
the pagination link.

# Dynamic number of slots 

Next, we would like to hae a way of defining a <b>dynamic</b> number of slots in the pagination
component, so that we can pass as many steps as we want.
Before that, let's redesign our pagination component to reflect the following:

{{< gist PavlosIsaris be38f04905c67866c1ed73698484cb08 >}}

As we can see here, we use the `steps` prop in order to show the pgination header link (as a
bootstrap breadcrumb item), as well as the corresponding Vue `slot`.

# Creating the dynamic slots

Now, its time to tie it all together! 
Update `App.vue` to reflect the following:


{{< gist PavlosIsaris 04c7447f95a58d35f454ea2e7e739711 >}}

As you can see, we pass a dynamic number of `template` components, that will serve as each 
of the steps' content. The limitation here is that the number of the templates should be equal to
the number of the steps that we create in the `data` section of `App.vue`.


# Example Usage

![Example pagination component](https://raw.githubusercontent.com/PavlosIsaris/Vue.js-pagination-example/master/public/img/example2.gif)

Pretty cool, huh?
