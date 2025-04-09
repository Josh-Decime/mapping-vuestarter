We are building with MVC structure, utilizing Vue. It is important to follow the organizational structure so here is a guide to the template structure.

src (folder) -> assets, components, handlers, models, pages, services, & utils (folders); App.vue, AppState, env, main & router (files)
images are stored under assets -> img
the navbar is in the components folder


***MODELS:
When we have an array of objects in the AppState we want to use a model whenever applicable. Model instances are better for scaling. We can also set variables to all objects that doesn't need to be listed on the object by using ||
Here is how out model should be set up: 
export class ExampleModel {
    constructor(data) {
        this.example = data.example
        this.inheritedVariable = data.inheritedVariable || (value, true or false)
    }
}
!!Important!! the model should ONLY store data & should never contain functions.


***PAGES:
HomePage.vue is the vue page that displays when the webpage is first accessed, this is where the main page goes. Any additional pages need a path in the router.js
Vue pages should be organized like:
<template>
  HTML goes here
</template>

  <script>
  export default {
    setup() {
        <!-- Reference/watch values in the AppState or call functions in the services -->
         const variable = computed(() => AppState.variable)
        function exampleFunction() {
         exampleService.exampleFunction()
        }
        return{
        <!-- Name of functions/values that will be called in the HTML -->
        exampleFunction,
        }
    }
}
  </script>

<style scoped lang="scss">
/* Styles that are exclusive to this page. Shared styles can be stored under src->assets->scss->styles.scss */
</style>
!!Important!! Vue pages should NEVER contain implementation functions, it should only have coordinator functions that call implementation functions from the services! 


***SERVICES:
!!Important!! This stores ALL of our implementation functions, also known as Business Functions. 
They should be grouped up by commonality, for example if we have characters they will have their own service, if we have enemies they would have their own service & overarching game rules would have their own, etc.
class ExampleService{
}
export const exampleService = new ExampleService()


***AppState:
In this template the AppState starts with this code, it is important that it remains in the AppState or login will be broken.
import { reactive } from 'vue'

// NOTE AppState is a reactive object to contain app level data
export const AppState = reactive({
  /**@type {import('@bcwdev/auth0provider-client').Identity} */
  identity: null,
  /** @type {import('./models/Account.js').Account} user info from the database*/
  account: null
})

The AppState stores our model instances & variables. Here are some examples:
exampleModelInstances: [
    new Example({
        name: 'Example Name',
        description: 'Instance object of the Example model'
    })
]
variableValue: 1000,
belongsHere: true,


***ROUTER:
Here is the starting code in the router
import { createRouter, createWebHashHistory } from 'vue-router'
import { authGuard } from '@bcwdev/auth0provider-client'

function loadPage(page) {
  return () => import(`./pages/${page}.vue`)
}

const routes = [
  {
    path: '/',
    name: 'Home',
    component: loadPage('HomePage')
  },
  {
    path: '/about',
    name: 'About',
    component: loadPage('AboutPage')
  },
  {
    path: '/account',
    name: 'Account',
    component: loadPage('AccountPage'),
    beforeEnter: authGuard
  }
]

export const router = createRouter({
  linkActiveClass: 'router-link-active',
  linkExactActiveClass: 'router-link-exact-active',
  history: createWebHashHistory(),
  routes
})
