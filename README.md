# Introduction to Application State in Redux

## Overview

In this reading, you'll learn how Redux manages application state. If you've read the [Redux documentation](http://redux.js.org/), you'll see that it describes Redux a 'Redux is a predictable state container for JavaScript apps.' At a high level, this means that Redux helps us ensure that our application data and our application views are in-sync. We do this by keeping track of state.

## Objectives

1. Describe how Redux governs application state
2. Explain how state in Redux differs from other flux implementations
3. Identify the principles of Redux and how they affect application state

By now, you've learned about some of the core concepts of Redux (and the Flux pattern generally): the store, actions, and reducers.

As a refresher:

+ A **store** is an object that holds application state
+ Changes to application state are triggered by invoking **action creators**, functions whose return values are JavaScript objects that describe how the state should change
+ The objects returned from action creators are (not surprisingly) called **actions**. They describe how state should change
+ A store has a **dispatch function** that accepts an action as its parameter and passes it along to all the reducers in the application
+ When a **reducer** function receives an action, it updates application state based on instructions defined in the action object


### Application State vs. Component State

The concept of state is something we've seen before in the context of React Components.
For example, a checkbox component might have two states: checked and not checked. A React component knows about its state and probably has some logic that governs its styling and appearance based on this state.  You could have a React component that wraps a checkbox. When a user clicks the checkbox, that component might turn green to signal that it's checked. When it's unchecked, it might turn red. This is an example of component state - checked or unchecked - affecting UI behavior.

In React, having a component that needs to have state is also a clue that it should be a class-based component rather than a functional component. We need keep track of component state when we need it to be able to change how the component  behaves (or looks), depending on user interaction:


```
class myCheckBox extends React.Component {

  handleCheckboxClick(){
    this.setState({checked: !this.state.checked})
    var divColor = this.state.checked ? "green" : "red"
    this.setState({color: divColor})
  }

  render(){
    return (
      <div style={{color: this.state.color}}>
        <input type="checkbox" onClick={this.handleCheckboxClick.bind(this)}
      </div>
      )
  }
}

```


State in Redux is similar to state in React but at a larger scale. While state in React is focused on an individual component, **Redux holds the state for our whole application**. Redux state refers to both the data that our application needs to use in our layout **PLUS** the logic around how that data is displayed. For example, let's say that we're using our checkbox React component in an app used at the Flation School that keeps track of student progress. We need to keep track of all the students in a class and figure out if any of the students haven't met with an instructor to get their idea for project mode approved.

In this case, we might iterate over the list of students in the class, displaying their names and a checkbox next to each. The instructor can click the checkbox to indicate that the student has met with them and gotten approval on their idea for project mode. In this case, our application state would keep track of both: 1. the information about all the students, and 2. which students' checkboxes have been checked in the view.

Similarly, if our student application also lets us click on a specific student to view their information on a student profile page, application state will also hold information describing which student is the one currently being viewed. This is what makes Redux so useful--it gives us a container to hold all the information that our application needs.


### Redux Principles

To understand how Redux manages state, it's important to understand the principles behind it and why they matter.

1. Single Source of Truth

Redux stores the state of our entire application in a **single object**. This is different from how other libraries implement the flux pattern in that others suggest using a different store (and a different state object) for each model. Having a single store with a single state object means that Redux provides a single source of truth for everything we need in our application: both our data and our UI state. If we link our views to our application state, it means that our views will always be in-sync with our application state. With Redux, there will never be conflicting information about what the state of our application should be. There is only one place where this information about our data and our UI is stored--on application state.


2. State is Read-Only

If you've been reading closely, you're probably wondering how this could possibly be true. If state is read-only, how can we update it with new information like UI changes or data updates?

The answer is that changes to state are made, but we don't do it by updating the state object directly. Instead, we create a **new** state object and add the values we want to add or change, plus the old values from previous state that we want to copy over unchanged. Any time that we need our application state to change, we create a new object that reflects the current, updated state of our application and leave the old object untouched.

The benefit of this approach is that it makes it easier for us to understand when and why our application state has changed. One way to do this is to create function that writes our current application state to a log file and call this function whenever an action is dispatched. This way, we have a record of what our application state was when the action was triggered, making it easy to track down bugs.


3. State Changes are Made with Pure Functions

Another Redux principle is that the reducer functions, the functions that handle changes to application state, are pure functions. To be considered "pure," a function has to meet two criteria:

1. It will always return the same value(s) if given the same argument(s). The only thing that affects the return value of a pure function are the arguments it is passed. This means anything external to the function is irrelevant. For example, a function whose return value is affected by the time or day, day of the week, or application data (is a user is logged in, ) is not a pure function. The only thing that affects a pure function's return value is what it receives as arguments.

2. It has no side effects. All a pure function does is take action on its own internal information and return a value. Pure functions do not take actions that have external effects in your application like saving data in a database, fetching data over the network, or modifying the values of other objects. In Redux, reducers are pure functions because they never change state--they only create a new state object and update that object by copying-in existing values and modifying state based on instructions in the action passed in.

### Resources

+ [Three Principles of Redux](http://redux.js.org/docs/introduction/ThreePrinciples.html)
+ 
