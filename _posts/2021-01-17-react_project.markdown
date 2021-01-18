---
layout: post
title:      "React Project"
date:       2021-01-18 02:29:23 +0000
permalink:  react_project
---


My final portfolio project at Flatiron was a workout tracker app! I found React/Redux to be the most challenging project yet. One of the most challenging things about react and redux was the flow and it is something I will definitely need more practice with. In my app a user can create a workout with a title, duration of the workout and a date. A user can add exercises to that workout with a name, reps, sets and weights. A user can also delete workouts (which will delete the exercises associated with that workout).

Over the course of building the workout tracker app I had several issues. One of the most frequent and challenging issues I had was working with nested data. Another challenge was working with state and redux. My project probably has a million commented out debuggers in it.

For my project flow I decided to make an Exercise and Workout container that was connected to redux and pass props from these containers down to functional components called ExerciseDisplay and WorkoutDisplay and their job was to display the data on the page. I also seperated out my buttons into seperate files and passed them props from the display page.

My app also contained a WorkoutForm and an ExerciseForm. These forms use both local state and global state so they can be controlled forms. Below are some examples of my containers and components:

WorkoutContainer.js
```
//working with redux in this component, making it class based
import { connect } from 'react-redux'
import React, { Component } from 'react'
import { getWorkouts, deleteWorkout } from '../actions/workouts'
import { addExercise } from '../actions/exercises'
import WorkoutDisplay from '../components/WorkoutDisplay'

class WorkoutContainer extends Component {

  componentDidMount(){
      this.props.getWorkouts()
  }

  handleDeleteWorkout = (event) => {
    this.props.deleteWorkout(event.target.id)
  }

  render() {
 
    const workoutList = this.props.workouts.map((workout, index) => {
        return <WorkoutDisplay 
          key={workout.id}
          workout_id={workout.id}
          id={workout.id}
          title={workout.attributes.title}
          duration={workout.attributes.duration}
          date={workout.attributes.date}
          handleDeleteWorkout={this.handleDeleteWorkout}
        />
    }) 

    return (
      <div className="Workouts">
          {workoutList}
      </div>
        
    )
   }
}

const mapStateToProps = state => {
  return {
    workouts: state.workoutReducer.workouts, //found in reducer
    loading: state.workoutReducer.loading,
  }
}

export default connect(mapStateToProps, { getWorkouts, deleteWorkout, addExercise })(WorkoutContainer) 
```


WorkoutDisplay.js
```
import React from 'react'
import { Button } from 'react-bootstrap';
import AddExerciseButton from './AddExerciseButton';
import DeleteWorkoutButton from './DeleteWorkoutButton'
import {
    BrowserRouter as Router,
    Link
  } from "react-router-dom";
import Card from 'react-bootstrap/Card'

export default function WorkoutDisplay(props) {
//debugger 
//console.log("props is", props)
    return ( 

        <div className="workout-display">
        
        <ul>
        <Link to={`/workouts/${props.workout_id}/exercises/`}>
           <h4>{props.title}</h4>
        </ Link>

           <h5> {props.duration} <p> {props.date} </p> </h5>
         
           <AddExerciseButton 
                workout_id={props.id}
                type="button"
                handleAddExercise={props.handleAddExercise}
            />
     
           <DeleteWorkoutButton 
                workout_id={props.id}
                type="button"
                id={props.id}
                handleDeleteWorkout={props.handleDeleteWorkout}
                //onClick={props.handleDeleteWorkout}
                />

            </ul>
        </div>
    )
}
```


And heres my DeleteWorkoutButton.js
```
import React from 'react'
import { Button } from 'react-bootstrap';

const DeleteWorkoutButton = props => {
    //debugger 
    return (
        <Button
            workout_id={props.id}
            type="button"
            className="btn btn-danger"
            variant="primary"
            id={props.id}
            onClick={props.handleDeleteWorkout}
                > Delete
        </Button>
    )
}

export default DeleteWorkoutButton
```

The fetch flow in my app kinda goes like this:
1. My Containers or Forms have a call to an action, the action is located in my /actions folder
2. In the action file (the particular one that is called), there is a fetch request
3. The fetch request calls a dispatch method which then goes to (that particular) reducer and the proper case statement which returns new state
4. Next it will do something with that state (either add it to the page or delete it or whatever my app asks).

Heres an example of a get request to get all the workouts on the page:

1. Container with the get request and action call
```
import { getWorkouts, deleteWorkout } from '../actions/workouts'

class WorkoutContainer extends Component {
  //never arrow fct for performace

  componentDidMount(){
      this.props.getWorkouts()
  }
```

2. In the action file /actions/workouts theres the fetch request
```
export const getWorkouts = () => { 
    return dispatch => {
        dispatch({type: "LOADING_WORKOUTS"})
        fetch("http://localhost:3001/api/v1/workouts")
        .then(res => res.json())
        .then(workouts => {
            dispatch({type: "WORKOUTS_LOADED", payload: workouts.data}) //payload: workouts.data}
            })    
    }
}
```
3. In the reducer we have a couple case statements that match the dispatch type seen in /actions/workouts (above)
```
        case("LOADING_WORKOUTS"): 
            return {...state, loading: true}
        case("WORKOUTS_LOADED"):
            return {...state, 
                loading: false,
                workouts: action.payload
            }
```
4. Then the workouts are displayed on the page!

I definitely have a lot of studying to do before the assessment! Happy Coding!






