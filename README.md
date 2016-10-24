# Introduction to Application State in Redux

## Overview

In this reading, you'll learn how Redux manages application state. If you've read the [Redux documentation](http://redux.js.org/), you'll see that it describes Redux a 'Redux is a predictable state container for JavaScript apps.' At a high level, this means that Redux helps us ensure that our application data and our application views are in-sync. We do this by keeping track of our application state, held inside our store.

## Objectives

1. Describe how Redux governs application state
2. Explain how state in Redux differs from other flux implementations
3. Identify the principles of Redux and how they affect application state

Before we stat looking at state, let's revisit what we've learned about the core concepts in Redux and the flux pattern generally: the store, actions, and reducers:

+ A **store** is an object that holds application state
+ Changes to application state are triggered by invoking **action creators**, functions whose return values are JavaScript objects, called **actions** that describe how the state should change
+ The store has a **dispatch function** that accepts an action as its parameter and passes it along to all the reducers in the application
+ When a **reducer** function receives an action, it updates application state based on instructions defined in the action object


### Application State vs. Component State

While application state might be a new concept, the concept of state in general is something we've seen before in the context of React Components. A React component knows about its state and probably has some logic that governs its styling and appearance based on this state. For example, you could have a React component that wraps a checkbox with two states: checked or not checked. When a user clicks the checkbox, the component might turn green to signal that it's checked. When it's unchecked, it might turn red. This is an example of component state - checked or unchecked - which is bound to the appearance of that element.

In React, having a component that needs to have state is also a clue that it should be a class-based component rather than a functional component. We need keep track of component state when we need it to be able to change how the component  behaves (or looks), depending on user interaction.

Here's an example of a React component whose state changes whenever its child element (a checkbox) is clicked.

```
class myCheckBox extends React.Component {

  handleCheckboxClick(){
    var divChangeColor = this.state.checked ? "red" : "green"
    this.setState({checked: !this.state.checked, color: divChangeColor})
  }

  render(){
    return (
      <div style={{color: this.state.color}}>
        <input type="checkbox" onClick={this.handleCheckboxClick.bind(this)} />
      </div>
      )
  }
}

```

When a user clicks the checkbox, this triggers the `handleCheckBoxClick` function to be called, updating the state of the parent `myCheckBox`. This causes the `myCheckBox` component to re-render, and causes its children (the div and the input checkbox) to re-render as well. This causes the color of the div holding the checkbox to toggle from green to red each time the checkbox is clicked.


### That's Great, but What Does it Have to Do with Redux?

State in Redux is similar to state in React but at a larger scale. While state in React is focused on an individual component and its children, **Redux holds the state for our whole application**. Redux state refers to both the data that our application needs to use in our layout **PLUS** the logic around how that data is displayed. For example, let's say that we're using our checkbox React component in an app used at the Flatiron School that keeps track of student progress. We need to keep track the students in the class and figure out if any of them haven't gotten approval from an instructor for their app idea.

In this application, we'll want to create a view that displays the names of all the students in the class and render our checkbox component next to each name. We'd want each checkbox element to somehow be connected to the student whose name is displayed next to it so that when we click the checkbox, this will change the checkbox element's color AND to update our application data so that the student record now reflects that the student has met with an instructor.

To create this view, we might iterate over the list of students in the class, displaying their names and a checkbox next to each. The instructor can click the checkbox to indicate that the student has met with them. In this case, our application state would keep track of both: 1. the information about all the students, and 2. which students' checkboxes have been checked in the view. In this way, Redux keeps track of both data (which students have met with an instructor) and the view logic (which checkboxes are green). This is what makes Redux so useful--it gives us a container to hold all the information that our application needs.


### Redux Principles

To understand how Redux manages state, it's important to understand the principles behind it and why they matter.

1. Single Source of Truth

Redux stores the state of our entire application in a **single JavaScript object**. This is a unique approach to the Flux design pattern in that other libraries might recommend using a different store for each model. Having a single store with a single state object means that Redux provides a single source of truth for everything we need in our application: both our data and our UI state. If we link our views to our application state, it means that our views will always be in-sync with our application state. With Redux, there will never be conflicting information about what the state of our application should be. There is only one place where this information about our data and our UI is stored--on application state.


2. State is Read-Only

If you've been reading closely, you're probably wondering how this could possibly be true. If state is read-only, how can we update it with new information like UI changes or data updates?

The answer is that changes to state are made, but we don't do it by updating the state object directly. Instead, we create a **new** state object and add the values we want to add or change, plus the old values from previous state that we want to copy over unchanged. Any time that we need our application state to change, we create a new object that reflects the current, updated state of our application and leave the old object untouched.

The benefit of this approach is that it makes it easier for us to understand when and why our application state has changed. We can create a function that writes our current application state to a log file and call this function whenever an action is dispatched. This way, we have a record of what our application state was when the action was triggered, making it easy to track down bugs.


3. State Changes are Made with Pure Functions

Another Redux principle is that the reducer functions, which handle changes to application state, are pure functions. To be considered "pure," a function has to meet two criteria:

1. It will always return the same value if given the same argument(s). The only thing that affects the return value of a pure function are the arguments it is passed. This means anything external to the function is irrelevant. For example, a function whose return value is affected by the time or day, day of the week, or application data (if a user is logged in, number of model records, etc.) is not a pure function. The only thing that affects a pure function's return value is its arguments.

2. It has no side effects. All a pure function does is take action on its own internal information and return a value. Pure functions do not take actions that have external effects in your application like saving data in a database, fetching data over the network, or modifying the values of other objects. In Redux, reducers are pure functions because they never change state--they only create a new state object and update that object by copying-in existing values and modifying state based on instructions in the action passed in.


### Accessing State

We can access our application's state object by calling the `getState()` method on our Redux store. 

```
import { createStore } from 'redux';
import { myReducer} from '../reducers/my-reducer'

const myStore = createStore(myReducer);

myStore.getState()

```

### Resources

+ [Three Principles of Redux](http://redux.js.org/docs/introduction/ThreePrinciples.html)
