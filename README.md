# Model Documentation

## Approach to structure the code

My assumption was that the state machine needed would be a larger piece of code. I also realized that the code needed to convert the desired trajectory via *spline* into the coordinates of the map is a lengthy procedure. I therefore tried to pack these activities in separate functions and leave the main function with its loop only to collect data from sensors and feed back the new coordinates to the Json object.

## Key components of the code

We can identify three main components in main.cpp

1. the part of the code where we take information from sensor fusion and filter out relevant data
2. Making decisions based on the data, which happens in a state machine using a set of helper functions
3. use the decision taken by the state machine and create a list of waypoints so that a smooth driving experience is the result. This happens in the function *make_wp_very_smooth()*

## Approach to design the state machine

The state machine is basically a construction of nested if then statements. it starts with the question if there is a problem with the current speed and direction. This could be because we are too slow or because there is an obstacle ahead. If there is no obstacle and we are too slow then increase speed. If there is an obstacle, then check the other lines if it would make sense so switch to the other lanes. if there is no space to move to the other lanes at the same speed then stay on the current lane and reduce speed. as soon as there is a space in the other lanes, then switch to the other lane.

The functions involved are:

1. **state_machine()**: The main function for this part of the code. It calls another function to check if it is safe to switch to another lane and it reduces speed if there is an obstacle ahead or if the car is too slow.
2. **speed_advantage()**: Measures if the other lane brings an advantage in speed compared to staying on the current lane and slowing down to prevent crashing into the car ahead
3. **collision_risk_check()**: It checks if there is enough space between the ego car and the closest car ahead and behind on all three lanes. It would be dangerous to switch to a lane if there is not enough space.
4. **check_lane_switch_dynamic()**: It uses the results of the previous two functions and makes a decision to which lane to switch or not to switch at all.



## Ideas for improvement

The code was tested several hours and debugging the code took many hours. Experiment showed that it is able to run up to 12 minutes without incident. However, there are cases in which the car crashed into other cars and it could not be identified why. The test scenarios are very complex and significantly more time would be needed. However, certain improvements can be envisioned. I partly tried them out but ultimately opted for a simpler state machine because the complexity of the model and the necessary testing effort becomes extreme.

**Potential improvements are:**

1. If we want to switch to another lane, then we check only the space behind and the space ahead then then say it is safe or not to move into this lane. But this is not optimal. If there is very little space but the car behind is slower than the ego car, then it would be still safe to switch lane. Similar, if we have enough space but the car behind is very fast, then it would be still a dangerous situation.
2. If the car is on lane 0 then it only checks if it is reasonable to go to lane 1, but maybe it would be the best to go to lane two and during this process accelerate if there is only little space. but this is a very complex scenario to test.  
3. speed control. The model so far has four actions connection to speed: a) keep speed, a) break c) accelerate a little bit d) accelerate maximum. It would be better to accelerate and break proportionally depending on circumstances.
